# Git Tic-Tac-Toe

A turn-based game where moves are git commits and rules are enforced by GitHub Actions. Anyone can play - no collaborator access needed.

## How It Works

The game state lives in `game.json`. Players take turns by opening Pull Requests that modify this file. A GitHub Action validates each move and **auto-merges** if valid.

**The key insight:** GitHub branch protection requires status checks to pass before merging. By making the status check a game rule validator that auto-merges, we turn GitHub into a trustless game server.

### Turn Enforcement

The Action checks that the PR author's GitHub username matches the player whose turn it is. Alice cannot submit a move for Bob - the PR author is cryptographically tied to their GitHub account.

### Architecture

```
main                         <- Template + workflows (protected)
 └── game/alice-vs-bob       <- Game instance (auto-validated)
      ├── fork PR from alice <- Validated & auto-merged
      ├── fork PR from bob   <- Validated & auto-merged
      └── ...
```

### Security Model

The workflow uses `pull_request_target` to safely handle fork PRs:
- Runs with base repo permissions (can merge)
- Only checks out trusted base repo code
- Fetches PR's `game.json` via API (never executes fork code)

## Starting a New Game

Anyone can start a game by opening an Issue:

**Title:** `new game: alice vs bob`

The Action automatically creates the game branch. No collaborator access needed.

## Making a Move

1. Fork the repository
2. Fetch the game branch:
   ```bash
   git fetch origin game/alice-vs-bob
   git checkout -b my-move origin/game/alice-vs-bob
   ```
3. Edit `game.json`:
   - Place your symbol (X or O) in an empty cell
   - Update `turn` to the other player's symbol
   - If you won, set `winner` to your symbol
4. Push to your fork and open a PR **targeting the game branch**
5. If valid, the Action auto-merges. If invalid, it fails with an explanation.

### Board Layout

```
 0 | 1 | 2
---+---+---
 3 | 4 | 5
---+---+---
 6 | 7 | 8
```

### Game State Format

```json
{
  "board": [null, null, null, null, "X", null, null, null, null],
  "players": { "X": "alice", "O": "bob" },
  "turn": "O",
  "winner": null
}
```

## GitHub Setup (for your own copy)

### Branch Protection Rulesets

**1. `protect-main`** (targets: default branch)
- Require pull request before merging
- Block force pushes
- *Prevents tampering with validation rules*

**2. `game-branches`** (targets: `game/**`)
- Require status checks to pass: `validate`
- Block force pushes
- Bypass: repo owner (to manually create games)
- *Note: With auto-merge, the "require PR" rule is optional*

### First-Time Contributors

GitHub may require approval for first-time contributors before Actions run. Check **Settings → Actions → General → Fork pull request workflows** and adjust as needed.

### Required Permissions

The workflow needs write access to merge PRs. Ensure **Settings → Actions → General → Workflow permissions** is set to "Read and write permissions".

## Rules Enforced by CI

- ✓ PR author must match the player whose turn it is
- ✓ Exactly one empty cell filled per turn
- ✓ Correct symbol (X or O) placed
- ✓ Turn advances to other player
- ✓ Winner/draw correctly detected
