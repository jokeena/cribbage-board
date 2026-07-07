# Cribbage Board — Spec

A digital cribbage scoring board for when you have physical playing cards but no board.
The app is a beautiful board, not a rules referee: scoring is free-form, players enforce the rules.

## Tech

- **Single self-contained HTML file** (`index.html`) — inline CSS/JS, no build step, no dependencies.
- **localStorage persistence**: full game state (including undo history) saved on every change.
  Opening the app **auto-resumes silently** into the in-progress game. "New game" lives in the menu.
- **Landscape-first mobile layout** (board left, controls right); gracefully stacks vertically in portrait.
- No haptics, no sounds (explicitly out of scope).

## Board

- **One continuous 121-hole oval loop** (single ring, no laps). Two start holes per lane; hole 121 is the finish (starred), adjacent to start.
- **2 or 3 players**, one concentric lane each.
- **Skunk line** marked between holes 90/91 and **double-skunk line** between holes 60/61 (🦨 markers on the track).
- Hole labels every 10; classic 5-hole grouping cues.
- **Classic front-peg / back-peg leapfrog**: scoring N moves the back peg to (front + N); it becomes the new front peg. Pegs animate along the track.
- Because 121 holes on one ring is tight on a phone, each player's current score is **also shown as a big numeral** on their player card.
- **Themes** re-skin colors/materials (Walnut Classic default, plus Midnight, Ocean, Neon). Selected in the menu, persisted.

## Players & setup

- Choose 2 or 3 players. Names default to Player 1/2/3; editable.
- Each player picks a **peg color from presets** (duplicates disabled). Colors carry into pegs, player cards, and the history log.

## Scoring input

- **Tap a player toggle, then tap a number** — two taps per score, no typing.
- Number grid **1–29 always visible**: **1–6 oversized** (most common pegging scores), 7–29 in a compact grid beside them.
- Selection is sticky — the selected player stays selected until another is tapped ("swap back and forth").
- Quick combo buttons ('go', '15', '31', pair, trips, quads, runs) are **deliberately deferred to a later version**.
- No turn/phase enforcement, no dealer tracking.

## Undo / history

- **Unlimited undo** (steps back one score at a time) with **redo** after undo.
- **Slide-out history panel** listing every entry (player, points, from→to), newest first. Reference only — no tap-to-revert.
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
- **Skunk** = one extra step, taken from the loser first:
  loser above 0 → loser −1 and winner +1 total; loser at 0 → winner +2 total.
- **Double skunk** = two extra steps, loser-first:
  loser at 0 → winner +3; loser at 1 → winner +2, loser −1; loser at 2+ → winner +1, loser −2.
- **3-player**: apply the rule **per opponent independently**; effects stack. Each skunked opponent
  is evaluated separately (double skunk = 2 steps against that opponent). For each step a skunked
  opponent cannot pay (already at 0), the winner gains +1 **and every non-skunked other player gains +1**
  ("for not being skunked").
- Reaching 7 → **match celebration**, then the match pegs reset for a new match (game winner takes
  precedence if multiple players cross 7 in the same resolution).

## Tools (menu drawer — overlays that don't disturb the live game)

- **Hand counter**: pick 4 hand cards + a cut card from a tap grid (rank × suit, no typing) →
  exact score with breakdown (fifteens, pairs, runs, flush, nobs). "Crib hand" toggle (crib requires a 5-card flush).
- **Discard optimizer**: pick 6 cards + whose crib it is → for each of the 15 possible keeps,
  expected hand value (exact over all 46 cuts) ± expected crib value (Monte Carlo over opponent
  discards and cut). Shows ranked results; best keep highlighted.

## Menu drawer

☰ menu: Hand counter · Discard helper · Theme picker · New game (same players, confirm) ·
New match / change players (back to setup, confirm).

## Deferred / v2 ideas

- Quick combo buttons (Go=1, 15=2, pair, trips, quads, run-length picker) as an alternate input mode.
- More themes.

## Testing hook

`index.html?demo` auto-starts a throwaway 2-player demo game with sample scores (no persistence) — used for headless screenshot verification. Variants: `?demo=win` (plays to 121 to show the game-over screen), `?demo=hand` (opens the hand counter pre-filled with the perfect 29), `?demo=disc` (opens the discard helper pre-filled with 6 cards).
