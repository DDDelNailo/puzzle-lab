# Puzzle Lab

Puzzle Lab is a desktop puzzle workbench built with Tauri + Svelte.

It begins as a focused Sudoku application and progressively evolves into a small puzzle framework with puzzle generation, solving, visualization, and reusable domain abstractions.

The project is intentionally versioned as a learning exercise: each version introduces a new concept rather than attempting to build the final architecture immediately.

## What it is

The finished project should support Sudoku end to end:

* Play puzzles.
* Generate new puzzles.
* Validate moves.
* Detect completion.
* Solve puzzles.
* Visualize solving.
* Keep puzzle logic independent from the UI.

The internal design should also make it practical to introduce a second puzzle type without rewriting the application around Sudoku-specific assumptions.

## Core features

### Board and play

* 9×9 Sudoku board.
* Component-based grid, row, and cell rendering.
* Mouse and keyboard selection.
* Arrow-key navigation.
* Number-key input.
* Backspace/Delete to clear editable cells.
* Distinct given/pre-filled cells.
* Immediate conflict highlighting.
* Completion detection.

Optional polish:

* Pencil marks.
* Timer.
* Move counter.
* Completion animation.

### Puzzle generation

The generator should eventually produce valid, uniquely solvable Sudoku puzzles.

It should support:

* New puzzle generation.
* Resetting the current puzzle.
* Difficulty selection.
* Validation of generated puzzles.

`Reset` and `New Puzzle` are intentionally different operations:

* **Reset** restores the originally generated puzzle.
* **New Puzzle** generates a different puzzle.

Difficulty can initially be approximated through clue removal and uniqueness checking, then become more sophisticated if the project needs it.

### Solver

The solver should operate on the puzzle model rather than the rendered UI.

It should eventually support:

* Solving any valid Sudoku board.
* Full automatic solving.
* Step-by-step solving.
* A trace of solver decisions.
* Headless use from tests or other non-UI code.

The initial solver can use backtracking. More advanced solving techniques can be introduced later if they provide a useful learning problem.

## Architecture

The application should gradually separate:

```text
Puzzle model
    ↓
Validation / generation / solving
    ↓
Application state
    ↓
Svelte UI
```

The final architecture should have clear boundaries between:

### Puzzle model

Represents the board, cells, values, givens, candidates, and other puzzle state.

### Generator

Creates valid puzzle instances.

### Validator

Checks moves, conflicts, and completion.

### Solver

Consumes a puzzle model and produces a solution or solving trace.

### UI

Renders the current model and translates user actions into domain operations rather than directly implementing puzzle rules.

The project should eventually expose a small puzzle contract that a second puzzle type could implement, for example:

```text
generate()
validate(model)
isComplete(model)
solve(model)
```

The exact abstraction should be discovered through implementation rather than forced prematurely.

## Representative data model

```text
Cell {
  value: number | null
  isGiven: boolean
  candidates: Set<number>
}

Board {
  cells: Cell[9][9]
  difficulty: string
}
```

This is representative rather than a fixed implementation contract.

## Roadmap

### V1 - Play one Sudoku

* Hard-coded puzzle.
* Board rendering.
* Cell selection.
* Value entry.
* Given cells.
* Basic validation.
* Completion detection.
* Reset.

### V2 - Multiple puzzles

* New puzzle.
* Basic puzzle generation.
* Difficulty selection.

### V3 - Solver

* Backtracking solver.
* Solve button.
* Solver tests.
* Headless solver API.

### V4 - Solver visualization

* Step-by-step solving.
* Solver trace.
* Highlight the current operation.
* Explain solver decisions where practical.

### V5 - Puzzle architecture

* Extract reusable puzzle/domain interfaces.
* Improve separation between domain logic and UI.
* Experiment with a second puzzle type.

Further versions are intentionally left open for discoveries made while building the project.

## Non-goals

* Multiplayer.
* Online leaderboards.
* Accounts or cloud synchronization.
* A full puzzle marketplace or plugin ecosystem.
* Supporting many puzzle types before the core architecture is understood.
* Perfect difficulty classification from the beginning.

## Stretch ideas

* Undo/redo.
* Save and resume puzzles.
* Candidate/pencil marks.
* Advanced Sudoku solving techniques.
* Difficulty rating based on required solving techniques.
* A second puzzle type such as KenKen, Kakuro, or a simple logic grid.
* CLI/headless puzzle tools.

## Learning goals

Puzzle Lab is primarily an exercise in:

* Svelte component design.
* Reactive state.
* Domain/UI separation.
* Algorithms and backtracking.
* Constraint validation.
* Procedural generation.
* Testing logic independently from the UI.
* Gradually introducing abstractions only when they become useful.
