# Architecture

This document describes the layered architecture for the Minesweeper game.

## Overview

The game follows a clean layered architecture where dependencies flow in one direction:
`types → config → repo → service → runtime → ui`

## Layers

### types
Pure type definitions for the Minesweeper domain. Contains no logic, only data structures.

**Responsibilities:**
- Define `Difficulty`, `CellState`, `GameResult`, `Board`, `Cell`, `Game` types
- Define input/output types for service layer

**Interfaces:**
- No external imports except from `types` itself

### config
Application configuration and constants.

**Responsibilities:**
- Define difficulty level settings (rows, cols, mines)
- Define visual constants (cell symbols, colors)
- Environment configuration

**Interfaces:**
- `DIFFICULTY_PRESETS`: Dict[str, Dict[str, int]]
- `CELL_SYMBOLS`: Dict[CellState, str]

### providers
Cross-cutting concerns - utilities that don't belong to domain logic.

**Responsibilities:**
- Random number generation for mine placement
- Time handling for game timers

**Interfaces:**
- `get_random_int(low, high) -> int`
- `get_current_time() -> datetime`

### service
Core game logic and rules.

**Responsibilities:**
- Game initialization
- Cell uncovering logic
- Mine detection and adjacency counting
- Flag placement/removal
- Win/loss determination

**Interfaces:**
- `create_game(difficulty: Difficulty) -> Game`
- `uncover_cell(game: Game, row: int, col: int) -> Game`
- `flag_cell(game: Game, row: int, col: int) -> Game`
- `check_win(game: Game) -> bool`
- `check_loss(game: Game) -> bool`

### ui
User interface layer - CLI implementation.

**Responsibilities:**
- Display the game board
- Handle user input
- Display game state and results
- Render messages

**Interfaces:**
- `display_board(game: Game) -> None`
- `get_user_input() -> Tuple[str, int, int]`
- `display_message(message: str) -> None`

### runtime
Application lifecycle and wiring.

**Responsibilities:**
- Wire all components together
- Run the game loop
- Handle shutdown

### utils
Pure helper functions with no internal imports.

**Responsibilities:**
- String formatting
- Input validation helpers

## Architecture Rules

1. Every source file must live in exactly one layer directory
2. Imports must only reference layers in the file's "may import from" set
3. No file exceeds 300 lines
4. Parse-don't-validate at boundaries - parse external data into types, internal code trusts types
5. Tests live in `tests/` directory, not under `src/`

## Running the Linter

```bash
python lint.py
```

The linter enforces layer dependencies, file locations, and line limits.
