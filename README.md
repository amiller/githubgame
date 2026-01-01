# Git Tic-Tac-Toe

Turn-based tic-tac-toe played via GitHub PRs.

## Starting a Game

1. Create a game branch from main:
   ```bash
   git checkout main
   git checkout -b game/alice-vs-bob
   ```
2. Edit `game.json` with the two players:
   ```json
   {
     "board": [null, null, null, null, null, null, null, null, null],
     "players": { "X": "alice", "O": "bob" },
     "turn": "X",
     "winner": null
   }
   ```
3. Push the branch: `git push -u origin game/alice-vs-bob`

## Making a Move

1. Pull latest: `git pull origin game/alice-vs-bob`
2. Create move branch: `git checkout -b move/alice-1`
3. Edit `game.json`:
   - Place your symbol (X or O) in an empty cell
   - Update `turn` to the other player's symbol
   - If you won, set `winner` to your symbol
4. Push and open a PR **targeting the game branch**
5. CI validates, then merge

## Board Layout
```
 0 | 1 | 2
---+---+---
 3 | 4 | 5
---+---+---
 6 | 7 | 8
```

## Rules Enforced by CI
- Only the current player can make a move
- Exactly one empty cell must be filled per turn
- Must use correct symbol (X or O)
- Must correctly advance the turn
- Must correctly detect winner/draw


