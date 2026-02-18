# CSC111 Winter 2026 — Project 1

Authors: Nicolas Miranda Cantanhede; Cade McNelly

---

## Running the game

You can run the game in two different ways:

- **Terminal (core game):** run `adventure.py`
- **Game with visuals (Enhancement):** run `ui.py`  
  Make sure you have `pygame` and the regular CSC111 libraries installed.

---

## Game map
-1 -1  7  8 -1 -1 32 15 -1 -1 -1

-1 -1  6 -1 -1 -1 14 16 17 -1 -1

 5  4  3  2  1 -1 13 -1 18 19 -1
 
-1 -1 -1 -1  9 10 11 12 -1 20 21

24 23 22 -1 -1 -1 31 -1 -1 33 25

-1 -1 26 27 28 30 29 -1 -1 -1 -1

-1 -1 -1 -1 34 -1 -1 -1 -1 -1 -1

**Starting location:** `2`

---

## Commands

Movement / core:
- `go north`, `go south`, `go east`, `go west`
- `look`, `inventory`, `log`, `quit`

Items / game:
- `take <item>`
- `drop <item>`
- `inspect <item>`
- `submit early`
- `score`

---

## Game solution (win walkthrough)
[
“take tcard”, “go west”, “take signed extension request”, “go west”, “take dorm key”,
“go west”, “take lucky mug”, “go east”, “go east”, “go east”, “go east”, “go south”, “go east”,
“go east”, “go east”, “take usb drive”, “go west”, “go north”, “go north”, “go east”, “go east”,
“go south”, “go east”, “take toonie”, “go west”, “go north”, “go west”, “go west”, “go south”,
“go south”, “go south”, “go south”, “go west”, “go west”, “go west”, “drop toonie”, “go east”,
“go east”, “go east”, “go north”, “go north”, “go north”, “go north”, “go east”, “go east”,
“go south”, “go east”, “go south”, “go south”, “drop coffee”, “go north”, “go east”,
“take laptop charger”, “go west”, “go north”, “go west”, “go north”, “go west”, “go west”,
“go south”, “go south”, “go west”, “go west”, “go north”, “drop lucky mug”, “drop usb drive”,
“drop laptop charger”, “submit early”
]

Fun fact: this is also the most optimal way we have found to win the game.

---

## Lose condition(s)

### Lose Condition 1: Running out of moves
Players lose if they use up the maximum number of moves.

- Default max turns: **67**
- An enhancement can grant **extra moves** (see Enhancements).

Lose demo (from `simulation.py`):
[“go west”, “go east”] * 33 + [“go west”]   # 67 moves

### Lose Condition 2: Submitting early without meeting requirements
Using `submit early` immediately ends the run. It only counts as a loss if the win requirements are not met.

Lose demo:
[“submit early”]

Code involved:
- `adventure.py`
  - `DEFAULT_MAX_TURNS`, `PlayerState.max_turns`, `AdventureGame.turn`
  - `AdventureGame.submit_early()`
  - `AdventureGame.has_required_returns()`, `AdventureGame.has_storage_solution()`
  - `AdventureGame.apply_location_rewards()` for +30 move extension
- `ui.py`
  - `GameUI.do_move()` increments turns
  - `GameUI.do_submit()` triggers early submission
  - `GameUI._resolve_end_state()` decides win/lose

---

## Inventory

### Locations with items and/or item-related functionality
1, 2, 3, 4, 5, 6, 7, 9, 10, 11, 12, 13, 14, 15, 16, 17,
18, 19, 20, 21, 22, 23, 24, 25, 27, 28, 29, 30, 32, 33, 34

### Item data (name: start -> target)
usb drive: 12 -> 1                    laptop charger: 21 -> 1
lucky mug: 5 -> 1                     tcard: 2 -> 1
stapler: 29 -> 29                     campus map: 6 -> 6
gym pass: 6 -> 6                      bus ticket: 7 -> 7
umbrella: 9 -> 9                      protein bar: 9 -> 12
sticky notes: 10 -> 33                study timer: 11 -> 12
lecture notes: 13 -> 33               clicker: 14 -> 14
camera strap: 3 -> 34                 hand sanitizer: 16 -> 24
notebook: 1 -> 33                     scarf: 17 -> 7
spare usb cable: 18 -> 1              toonie: 19 -> 27
python cheat sheet: 20 -> 33          pencil: 15 -> 29
flashcard deck: 23 -> 33              printed report: 25 -> 32
blue pen: 22 -> 32                    coffee: 27 -> 33
headphones: 28 -> 12                  cookie: 2 -> 33
water bottle: 28 -> 12                sticker sheet: 29 -> 33
spare paper: 25 -> 25                 calculator: 30 -> 12
lab access form: 33 -> 32             marker: 30 -> 21
dorm key: 4 -> 3                      office hours token: 28 -> 33
assignment cover sheet: 29 -> 32      signed extension request: 3 -> 32
library book: 12 -> 24                group meeting notes: 23 -> 33
lost-and-found tag: 15 -> 34

### Example inventory commands
go west -> go west -> take dorm key -> go east -> drop dorm key -> inventory
### Code involved (`inventory`)
- `adventure.py`
  - `AdventureGame.inventory` (property)
  - `AdventureGame.pick_up()`, `AdventureGame.drop()`,
    `AdventureGame.get_item()`, `AdventureGame.inspect()`
- `ui.py`
  - `GameUI.open_take_modal()`, `GameUI.open_drop_modal()`,
    `GameUI.open_inspect_modal()`, `GameUI.do_inventory()`

---

## Score

### How scoring works
Players earn score when dropping items at their target location. Points are read from each item’s
`target_points` in `game_data.json`.

- Main objective items (USB drive, laptop charger, lucky mug): **30 points each**
- Optional side tasks add smaller points.

Special rule:
- `spare usb cable` can substitute for `usb drive` if USB drive was not returned yet.
  Dropping `spare usb cable` can award the USB-drive value.

### First score increase (demo)
First score increase happens at **Location 1 (Dorm Room)** by dropping the USB drive:
[take tcard, go west, go west, take dorm key, go east, go east, go east,
go south, go east, go east, go east, take usb drive,
go west, go west, go west, go north, drop usb drive, score]

### `scores_demo` list from `simulation.py`
[
“take tcard”,
“go west”, “go west”, “take dorm key”, “go east”, “go east”, “go east”,
“go south”, “go east”, “go east”, “go east”, “take usb drive”,
“go west”, “go west”, “go west”, “go north”, “drop usb drive”, “score”
]

### Code involved (`score`)
- `adventure.py`
  - `AdventureGame.score` (property)
  - `AdventureGame.check_quest()` (awards points on correct drop)
  - `AdventureGame.has_required_returns()`, `AdventureGame.has_storage_solution()`
  - `AdventureGame.lock_score()` (score freeze after post-win explore mode)
- `ui.py`
  - `GameUI.do_score()` (prints score + percentage)
  - `GameUI._draw_header()` (shows score in UI chip)
- `ui_endscreen.py`
  - `EndScreenView._summary_lines()` (shows final project grade percentage)

---

## Enhancements

### Enhancement 1: Game visuals (pygame UI)
- UI that imitates ACORN style with:
  - mini-map
  - clickable actions
- Complexity: **High**
- Why:
  - ~1500 extra lines of code
  - 3 extra files: `ui.py`, `ui_endscreen.py`, `ui._primitives.py`
- Challenges:
  - MiniMap direction consistency (fixed by simplifying map layout)
  - Scrolling UI (fixed by per-panel scroll areas)

Code involved: `ui.py`, `ui_endscreen.py`, `ui._primitives.py`

Demo commands (`enhancement1_demo` in `simulation.py`):
[
“take tcard”, “go west”, “take signed extension request”,
“go west”, “take dorm key”, “go east”, “go east”, “go east”, “go south”,
“go east”, “go east”, “go north”, “go north”, “go north”,
“drop signed extension request”
]

### Enhancement 2: Puzzle progression + time extension
Item-gated puzzle chain + extra moves:
- Dorm Room locked behind `dorm key`
- Bahen 2F Labs locked behind `lab access form`
- Trade `toonie` -> `coffee` at Cafe Alley
- Trade `coffee` -> `lab access form` at TA Help Table
- Drop `signed extension request` at Professor’s Office to gain **+30 moves once**

Complexity: **High**

Code involved:
- `game_data.json` (restrictions/rewards)
- `adventure.py` (`can_enter_location`, `apply_location_rewards`, `_apply_item_reward`)
- `simulation.py` (demo sequences)
- `ui.py` (move + drop flow)

Demo commands:
take toonie -> drop toonie (at Cafe Alley) -> drop coffee (at TA Help Table)
take lab access form -> enter Bahen 2F Labs -> take laptop charger
drop signed extension request (at Professor’s Office) -> +30 moves
