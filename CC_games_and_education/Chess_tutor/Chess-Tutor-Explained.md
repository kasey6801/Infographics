# Chess Tutor: How This Was Made

A companion document for [`index.html`](index.html), the interactive chess tutor in the
Games &amp; Education section of the Infographics collection. It explains what the page is,
the design decisions behind it, how the engine works, and how to regenerate the whole thing
from a single prompt.

## What it is

A complete chess game and teaching tool in one self-contained HTML file:

- **Play** against an engine at four strength levels (Novice to Strong), as White or Black.
- **Tutor mode** draws the top three candidate moves as arrows on the board on your turn,
  each with a short explanation and a score in pawns.
- **Post-game report** replays your finished game, compares every move against the engine's
  preferred move, grades each one (Best, Good, Inaccuracy, Mistake, Blunder), and gives each
  side an accuracy percentage.

The full rules of chess are supported: castling, en passant, promotion with a piece picker,
check, checkmate, stalemate, the fifty-move rule, threefold repetition, and insufficient
material. Light and dark themes follow the site standard.

## Decision record: why there is no Stockfish here

Two planning documents preceded this page (kept in a local archive, not published in this
repository). `chess-tutor-claude-code-kickoff.md` was the original build spec: it called for
the chess.js rules library plus a Stockfish WASM engine (about 7 MB), vendored via npm into
a dedicated repository with its own GitHub Pages site.
`product-design-guide-chess-tutor-case-study.md` was the requirements narrative that
produced that spec.

When the tutor moved into the Infographics collection, the plan changed deliberately:

| Kickoff spec | What was built instead |
|---|---|
| chess.js for rules | Original move generator |
| Stockfish lite WASM (~7 MB) | Original minimax search, about 40 KB of source |
| Multi-file site, `js/` + `engine/` folders | One self-contained `index.html` |
| Separate repo `chess-tutor` on GitHub Pages | A folder in this collection |
| GPL-3.0 license file shipped alongside | Nothing vendored, MIT-clean |

The reasons: this collection's design principle is one self-contained HTML file per page with
no external dependencies, the audience is "five users, not five thousand" learning basics,
and 8 MB of GPL-licensed binary is a lot of weight for one card. The trade-off: this engine
is much weaker than Stockfish (roughly beginner-to-club strength depending on level) and its
analysis is approximate. For teaching beginners that is enough: it spots hanging pieces,
missed captures, and short tactics, which is most of what beginner feedback is about. If lichess-grade analysis is ever wanted, the
kickoff spec's separate-repo plan remains the right home for it.

## Architecture

Everything lives in `index.html`, in four blocks:

1. **Inline CSS** following the site style guide: the shared palette variables, the
   `[data-theme="dark"]` overrides, and the board (an 8x8 CSS grid with coordinate labels,
   Unicode piece glyphs, and highlight overlays).
2. **`<script id="engine-src">`, the engine core** (sections E1 to E10 in the source). Pure
   JavaScript with zero DOM access, so the exact same text runs in three places: the page's
   main thread, a Web Worker, and node for the test suite.
3. **The application script** (sections A0 to A9): board rendering, click-to-move input, the
   promotion picker, the game controller, tutor arrows and explanations, and the report.
4. **The theme toggle script**, identical to every other page in the collection.

How the single file supports a worker: the engine block executes normally when the page loads,
which gives the UI synchronous access to move legality and notation. The application then
reads that same block's `textContent` and feeds it to `new Worker(URL.createObjectURL(new
Blob([...])))`, so searches run off the main thread without a second copy of the engine and
without an external file. If the worker cannot be created, the app falls back to running the
same functions on the main thread in small timed chunks.

## How the engine works

- **Board representation:** the 0x88 scheme, a 128-slot array where a single bit test tells
  you whether a square is off the board. Standard, compact, and fast enough.
- **Move generation:** pseudo-legal moves first, then each is played and rejected if it
  leaves the king in check. Slower than tracking pins directly but far harder to get wrong.
- **Evaluation:** material (pawn 100, knight 320, bishop 330, rook 500, queen 900) plus
  piece-square tables that reward centralized knights, advanced pawns, a castled king, and
  so on, with a small bishop-pair bonus and a separate king table for endgames.
- **Search:** negamax with alpha-beta pruning, captures ordered most-valuable-victim-first,
  and a quiescence search that keeps extending through capture sequences so the engine does
  not stop counting in the middle of a trade.
- **Strength levels** map to search depth plus deliberate noise: Novice searches one move
  deep with no quiescence and heavy noise, Strong searches four plies with none. The tutor
  and the report always analyze clean at depth 3, whatever the opponent level, so advice
  quality never degrades just because you chose an easy opponent.

## Tutor honesty rules

Every explanation is assembled only from facts the engine can verify on the board: the move
is a capture, gives check, castles, promotes, moves an attacked piece to safety, defends a
hanging piece, develops a minor piece, or fights for a center square. The ranking phrases
("clearly the best move here", "the top choices are close") come from the score gap between
the first and second candidates. Nothing is ever claimed that is not derived from a detected
feature or from the engine's own numbers.

## Report grading

For each move you played, the loss is how much better the engine's preferred move scored at
depth 3, clamped at 10 pawns: Best is a loss of 0.2 pawns or less, Good up to 0.49,
Inaccuracy up to 1.49, Mistake up to 2.99, Blunder 3.0 and above. Accuracy per move is
`100 * e^(-loss/350)` (loss in centipawns), averaged per side. This is this page's own
formula on this engine's own scale, labeled as not comparable to lichess or
chess.com accuracy numbers. Once a position is already completely decided (a swing of ten
pawns or more), the remaining moves are marked "decided" and left ungraded.

## Correctness evidence

Hand-written move generators have well-known failure modes: en passant discovering a check
on your own king, castling through an attacked square, castling rights surviving a rook
capture. The standard check is perft: count every legal move sequence to a fixed depth and compare
against published node counts. This engine passed the full suite before any UI was built,
byte-identical to the code that ships in the page:

| Position | Depth and expected nodes | Result |
|---|---|---|
| Start position | d1 20 · d2 400 · d3 8,902 · d4 197,281 · d5 4,865,609 | all pass |
| Kiwipete (castling and pins) | d1 48 · d2 2,039 · d3 97,862 · d4 4,085,603 | all pass |
| CPW Position 3 (en passant pin) | d1 14 · d2 191 · d3 2,812 · d4 43,238 · d5 674,624 | all pass |
| CPW Position 4 (promotions, rights by capture) | d1 6 · d2 264 · d3 9,467 · d4 422,333 | all pass |
| CPW Position 5 (promotion and discovery) | d1 44 · d2 1,486 · d3 62,379 · d4 2,103,487 | all pass |

On top of perft: unit checks for FEN round-trips and SAN edge cases (castling both sides,
`exd6` en passant, `e8=Q+`, mate suffixes, `Nbd2`-style disambiguation), then an end-to-end
driver that plays a full random game through the real click path, exercises the promotion
picker, undo, board flip, and the report, and finally a headless-Chrome check that the Web
Worker path delivers tutor arrows and engine replies in a real browser.

## Regenerate this page

Paste the following into Claude Code (or a comparable coding agent):

> Build a complete interactive chess tutor as ONE self-contained HTML file with no external
> dependencies, no CDN, no web fonts, and no build step. Write an original chess engine
> in JavaScript: 0x88 board representation, pseudo-legal move generation filtered by
> make/unmake king-safety checks, full rules (castling, en passant, promotions, fifty-move,
> threefold repetition, insufficient material), FEN parsing and SAN generation with proper
> disambiguation, evaluation from material plus piece-square tables, and negamax search with
> alpha-beta pruning and quiescence. Before writing any UI, validate the move generator
> against the standard perft suite (start position to depth 5 = 4,865,609; Kiwipete to depth
> 4 = 4,085,603; the CPW en-passant-pin position 8/2p5/3p4/KP5r/1R3p1k/8/4P1P1/8 to depth 5
> = 674,624; plus CPW positions 4 and 5) using a node test harness, and keep the shipped
> engine byte-identical to the validated code. UI: a CSS-grid board with rank and file
> coordinates and filled Unicode glyphs colored via CSS outlines for both themes,
> click-to-move with legal-target highlighting, a promotion picker dialog, last-move and
> check highlights, and board flip. Put the engine in a `<script id="engine-src">` block
> with zero DOM access and run searches in a Web Worker created from that block's
> textContent via a Blob URL, with a synchronous chunked fallback. Features: play as either
> color against four strength levels (depth 1 to 4, with root-score noise of 120/60/20/0
> centipawns and no quiescence at level 1); tutor mode that requests the top three moves
> (multipv) at depth 3 and draws them as SVG arrows with rule-based explanations derived
> only from verifiable board facts (captures, checks, escapes, defenses, development,
> center) plus the engine's scores; and a post-game report that grades every move by its
> centipawn loss against the engine's choice at depth 3 (Best <= 20, Good <= 49, Inaccuracy
> <= 149, Mistake <= 299, Blunder >= 300, clamped at 1000, with positions already beyond
> 1000 marked "decided" and ungraded) and shows per-side accuracy as the mean of
> 100*e^(-loss/350). Style: system font stack, cream-paper palette with a dark mode driven
> by a data-theme attribute and a persistent toggle, accessible (aria-labels on every
> square, aria-live status line, focus-visible outlines, Escape closes the picker).

## File map

| File | Purpose |
|---|---|
| `index.html` | The game: engine, UI, tutor, and report in one file |
| `Chess-Tutor-Explained.md` | This document |

The superseded planning documents (the chess.js + Stockfish kickoff spec and the
product-design case study that produced it) are preserved locally in an archive folder that
is intentionally not part of this repository.
