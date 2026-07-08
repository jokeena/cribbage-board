# Cribbage Board — Spec

A digital cribbage scoring board for when you have physical playing cards but no board.
The app is a beautiful board, not a rules referee: scoring is free-form, players enforce the rules.

## Tech

- **Single self-contained HTML file** (`index.html`) — inline CSS/JS, no build step, no dependencies.
- **localStorage persistence**: full game state (including undo history) saved on every change.
  Opening the app **auto-resumes silently** into the in-progress game. "New game" lives in the menu.
- **Landscape-first mobile layout**: **board on top, controls below** (player cards left, number pad right).
  Portrait stacks the same way with compacted cards.
- No haptics, no sounds (explicitly out of scope).

## Board

- **Snail / 3-street track** like a classic long wooden board (modeled on the reference photo
  `en_280.png`): the track runs along the top edge, U-turns around the right end, runs back along
  the bottom, U-turns at the left end **curling inward**, and finishes along the middle street —
  ★ hole 121 near the board's center. Long and narrow slab proportions.
- **2 or 3 players**, one parallel lane each, drawn as **colored lane stripes in each player's peg
  color** (like the painted tracks on the reference board). Two start holes per lane.
- **Skunk line** between holes 90/91 and **double-skunk line** between holes 60/61: an accent line
  across the track anchored to a 🦨 plaque (double marked ×2). The 60/90 hole labels are omitted —
  the plaques mark those spots.
- Hole labels every 10 (outside the track on straights, inside the U on curves); separator ticks
  every 5 holes.
- **On-board match scoreboard**: 7 small holes per player with their name, on the slab in the gap
  below the middle street ("MATCH · FIRST TO 7"), pegged in player colors.
- **Classic front-peg / back-peg leapfrog**: scoring N moves the back peg to (front + N); it becomes
  the new front peg. Pegs animate along the track and have gradient shading + drop shadows.
- Each player's current score is **also shown as a big numeral** on their player card below the board.
- Wood-grain texture (SVG turbulence), slab gradient and vignette, **wooden rail with brass corner
  screws** framing the slab, and hole 121 separated from the 116–120 group by its own tick.
- **One theme, mastered** (theme picker removed): green felt card table with fuzz texture and a
  warm top sheen, walnut board, gold accents.
- **Point buttons are 1–16 poker chips** (inline SVG modeled on a real dice chip: colored body,
  white edge stripes, embossed dice faces 1–6, serif numeral). Scores above 16 are entered as two
  taps (e.g. 20 = 16 then 4). The chips recolor to the **currently-selected player's color**, so it's
  always clear whose points the next tap adds. Player cards are ivory card-stock.
- No caption text on the board's match scoreboard, and no scoring-hint text under the board.

## Players & setup

- Choose 2 or 3 players. Names default to Player 1/2/3; editable.
- **Fixed palettes**: 2-player is red vs blue, 3-player is red vs blue vs green — players only pick
  *which* of those colors is theirs (tapping a taken color swaps with its holder). Colors carry into
  lane stripes, pegs, player cards, and history.
- **Player cards** (below the board): color band with peg-hole motif, serif name, a 0→121 progress
  bar with notches at the double-skunk (61) and skunk (91) lines, and a big serif score in the
  player's color.

## Scoring input

- **Tap a player toggle, then tap a number** — two taps per score, no typing.
- Number grid **1–29 always visible**: **1–6 oversized** (most common pegging scores), 7–29 in a compact grid beside them.
- Selection is sticky — the selected player stays selected until another is tapped ("swap back and forth").
- Quick combo buttons ('go', '15', '31', pair, trips, quads, runs) are **deliberately deferred to a later version**.
- No turn/phase enforcement, no dealer tracking.

## Undo / history

- **Unlimited undo** (steps back one score at a time) with **redo** after undo.
- **Slide-out history panel** listing every entry (player, points, from→to), newest first. Reference only — no tap-to-revert.
- **Recent moves** (last ~6) always visible as small fading chips in the felt margin left of the
  board — no tap needed. Hidden in portrait.
- Undo **remains available after a game ends**, so a mis-tap that "won" the game can be reversed (this also reverses the match points that were awarded).
- History resets when the next game starts.

## Game end (auto-detected)

When a front peg reaches/passes 121 (capped at 121):

- Board locks, **winner celebration** (confetti).
- Skunks auto-detected from opponents' final scores: **≤ 90 = skunk, ≤ 60 = double skunk**.
- **Skunk animation** (skunk + stink lines) for any skunked player.
- Match points applied automatically (see below).
- End-of-game stats screen: final scores, each player's **biggest single score entered** this game, and skunks.
  (No pegging-vs-hand distinction — deliberately dropped to avoid a mode toggle.)
- Buttons: **Undo last** (in case of misinput) and **Next game**.

## Session match scoreboard (house rules)

Shown as up to 7 peg dots on each player card. **First to 7 wins the match.**
Match points can never go below 0.

- **Win** = winner +1.
- **Each skunked opponent drops** by their skunk depth: single skunk (opponent finished ≤ 90) = −1,
  double skunk (≤ 60) = −2 — but never below 0 match points.
- **Unpayable ("floating") points**: whatever a skunked opponent can't pay (because they'd go below 0)
  doesn't just vanish or all pile on the winner. Those points are handed out one at a time, in
  priority order: **the winner first, then the least-skunked *other* opponent(s)**. This keeps a skunk
  meaningful even against a broke opponent while spreading the benefit.
- **3-player** works the same way, per opponent, with floating points shared as above. Worked examples
  (blue wins, double-skunks green, single-skunks red):
  - Everyone at 0/0/0 → **blue 3, red 1, green 0** (green's 2 unpayable points float to blue + red;
    red's 1 unpayable point floats to blue; plus blue's +1 win).
  - Starting 4/3/3 → **5/2/1** (nobody's broke, so it's just win +1, red −1, green −2).
- **2-player** falls out of the same rule: skunk of a broke opponent → winner +2; double skunk of a
  broke opponent → winner +3; if the loser has points they drop instead (down 1 / down 2).
- Reaching 7 → **match celebration**, then the match pegs reset for a new match (game winner takes
  precedence if multiple players cross 7 in the same resolution).

## Tools (menu drawer — overlays that don't disturb the live game)

- **Hand counter**: pick 4 hand cards + a cut card from a tap grid (rank × suit, no typing) →
  exact score with breakdown (fifteens, pairs, runs, flush, nobs). "Crib hand" toggle (crib requires a 5-card flush).
- **Discard optimizer**: pick your dealt cards — **6 in a 2-player game, 5 in a 3-player game**
  (the tool reads the current player count) — plus whose crib it is. Always keeps 4; for each
  possible discard it shows expected hand value (exact over all cuts) ± expected crib value
  (Monte Carlo, with the crib filled to 4 cards from unseen cards). Shows ranked results; best
  keep highlighted. (2-player has 15 keeps, 3-player has 5.)

## Menu drawer

☰ menu: Hand counter · Discard helper · New game (same players, confirm) ·
New match / change players (back to setup, confirm).

## Deferred / v2 ideas

- Quick combo buttons (Go=1, 15=2, pair, trips, quads, run-length picker) as an alternate input mode.
- More themes.

## Testing hook

`index.html?demo` auto-starts a throwaway 2-player demo game with sample scores (no persistence) — used for headless screenshot verification. Variants: `?demo=win` (plays to 121 to show the game-over screen), `?demo=hand` (opens the hand counter pre-filled with the perfect 29), `?demo=disc` (opens the 2-player discard helper pre-filled with 6 cards), `?demo=3` (3-player game, Amy/blue selected — shows blue chips), `?demo=disc3` (opens the 3-player discard helper pre-filled with 5 cards).
