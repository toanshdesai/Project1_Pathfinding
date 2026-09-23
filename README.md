# Project1_Pathfinding

This project is a visual comparison of three pathfinding algorithms: BFS, Dijkstra, and A* using a Manhattan-based heuristic.
It is written in Python and uses PyGame for graphics.

The grid uses weighted terrain. Normal cells cost 1, mud cells cost 5, and walls are infinity.
## How A* works

f(n) = g(n) + h(n)

Every cell gets two numbers. **g** is the cost of getting there from the start,
and **h** is the guess of the cost left to reach the goal. Their sum is **f**.

A* starts at the start cell and repeatedly takes the cell with the lowest f, 
since that one looks most promising to reaching the goal. It adds that cell's neighbors to a priority 
queue with their own f values, and marks each neighbor with the cell it came from. 
Once the goal comes out of the queue(has been found), A* walks the chain backwards to build the path.

BFS takes whichever cell it found first, so it finds the fewest steps but ignores cost.
Dijkstra takes the cell with the lowest g, so it finds the cheapest path but still searches in every direction.
**A\* when h = 0 is Dijkstra**, so the Dijkstra mode is the A* code called with a heuristic of 0.

## Design notes

- Every cell stores a cost, not a type. A wall is `math.inf`, which is too expensive and A* does not need a wall check
- The heuristic is Manhattan distance, `abs(dx) + abs(dy)`.
Movement is 4-directional, so using Manhattan gives you exactly the fewest moves possible.
It never guesses too high, which is what keeps A* optimal, and it is the highest safe guess,
so A* explores the fewest cells. Euclidean is also safe but guesses lower (sqrt(2) when Manhattan gives 2),
so A* would explore more.
- A* checks whether it reached the goal when a cell is popped(enters closed set), not when it is discovered(when added to the open set).
A cell found early through mud can be reached more cheaply later, so its cost and parent get rewritten when that happens.
- `bfs.py` and `astar.py` contain no PyGame code, so both can be tested from the terminal with `python bfs.py` or `python astar.py`.

## Files

- `main.py` — PyGame UI, grid drawing, mouse and keyboard controls
- `bfs.py` — BFS, and the shared `neighbors()` + `reconstruct()` methods
- `astar.py` — A* and Dijkstra, heuristics, terminal tests

## To use

Run `main.py` with Python 3.12 and PyGame installed (`pip install pygame`).

The start is the green cell in the bottom-left corner.

- Click or click and drag to cycle cells: normal → mud → wall → normal
- Right click to move the goal
- `B` / `A` / `D` / `E` — pick BFS, A*, Dijkstra's, or Euclidean Heuristic, shown in the header
- `SPACE` — run the selected algorithm
- `R` — reset the search
- `M` — load the test maze
- `C` — clear the grid, reset back to starting grid

The terminal window prints the cost, steps, and cells explored after each successful run.

## Test Results

Test maze (`M` key): a band of mud between the start and the goal, with a clear route above it.

| Algorithm | Path cost | Steps | Cells explored |
|-----------|-----------|-------|----------------|
| BFS       | 73        | 41    | 749            |
| Dijkstra  | 57        | 57    | 796            |
| A*        | 57        | 57    | 400            |

BFS took the fewest steps but the most expensive path, since it walked through 8 mud cells.
Dijkstra and A* both found the cheapest path, but A* got there after exploring about half as many cells.

Unexpected: on the empty beginning grid, all algorithms explore all the cells.
I think this is because every cell returns the same f value. Moving the goal anywhere else makes the A* algorithm best again

## Limitations

- 4-directional movement only. Manhattan would overestimate with diagonal movement, so 8-d would need euclidean or Chebyshev instead.
  - Euclidean would be an overestimation - shown in demonstration how it is less efficient
- Fast mouse drags can skip cells, and when dragging, the transition from cell types can be confusing and fast

## AI use

**FlintK12:** https://app.flintk12.com/activities/a-pathfinding-h-26efda/sessions/dee4ad8b-1ceb-4a11-a89b-9f6538af0d01

**Claude:** https://claude.ai/share/2797da4a-1da9-45bf-9334-536825896548