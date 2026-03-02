# Circle Packing Assignment — Project Context

## What This Project Is
We're modifying a Python web app called `Gumballs.py` that runs on `http://localhost:8000`. It lets users manually place unit-radius circles inside containers (square, circle, diamond) and visualizes the packing. The goal is to fit 23 unit-radius circles into the smallest possible container.

## Core Constraints
- All circles have radius = 1 (diameter = 2)
- Circles cannot overlap (center-to-center distance must be ≥ 2)
- Every circle must be entirely inside the container boundary
- Circle inside a square: center must be ≥ 1 unit from every edge
- Circle inside a circle of radius R: center must be ≤ R - 1 from container center
- Circle inside a diamond: see diamond geometry below

## Diamond Container Geometry
The diamond is two equilateral triangles sharing a common base (a rhombus with 60° and 120° angles). If the edge length is `s`:
- Minor diagonal (horizontal) = s
- Major diagonal (vertical) = s × √3
- The four edges have inward-pointing normals
- A circle of radius 1 is inside the diamond if and only if its center's perpendicular distance to each of the 4 edges is ≥ 1
- For an edge defined by normal vector `n` and offset `d`: point `p` is inside if `dot(n, p) ≤ d - 1`
- The minimum enclosing diamond for a set of circles: find the smallest `s` such that all circle centers satisfy the above constraint for all 4 edges

## Simulated Annealing for Shrink Container
The [Shrink Container] button should use simulated annealing:
1. Start from the user's initial placement
2. Each step: pick a random circle, jitter its center by a small random offset
3. Accept the move if: no overlaps AND all circles stay inside the container
4. Occasionally accept moves that increase container size (to escape local minima)
5. Temperature schedule: start high (accept bad moves often), cool down over time
6. Objective: minimize the container size (side length, radius, or edge length)

Key parameters:
- Initial temperature: ~1.0
- Cooling rate: ~0.9995 per step
- Jitter magnitude: scales with temperature
- Iterations: 50,000–200,000
- Track the best valid configuration seen so far

## Problem-Specific Notes

### Problem 1: Pack 23 circles in a square < 10 units
- Both ChatGPT (9.96) and Gemini (9.728) gave unverified claims — don't trust LLM packing claims without checking
- Hex grid packing is generally better than square grid for circles
- Known best for 23 circles in a square is approximately side length 9.7 (research this)

### Problem 2: Add [Submit Layout] button
- When clicked, download a .json file containing an array of circle center coordinates
- Format: `{"circles": [{"x": 1.0, "y": 1.0}, {"x": 3.0, "y": 1.0}, ...], "container": {"type": "square", "size": 9.8}}`

### Problem 4: Add container dropdown
- Options: Box (existing), Circle, Diamond
- Each container type needs its own minimum-enclosing calculation
- Update the display and boundary checking when container type changes

### Problem 5: Diamond enclosure
- Two equilateral triangles sharing a base
- Equal edge lengths
- Minor diagonal is horizontal
- Must calculate minimum enclosing diamond for placed circles

### Problem 7: [Shrink Container] button
- Uses simulated annealing (see above)
- Works with whichever container type is selected
- Must maintain: no overlaps + all circles inside container
- Display should update as optimization runs (or show final result)

### Problem 8: 23 circles in a diamond
- There may be an obvious layout (hint from the professor)
- Try hex-like arrangements that fit the diamond's 60°/120° angles

## Tech Stack
- Python backend serving on localhost:8000
- HTML/CSS/JS frontend
- Circles are dragged and placed via mouse interaction
- Grid snapping (square and hex grids available)

## File Output
All [Submit Layout] outputs should be valid JSON that can be pasted directly into the assignment submission.

## Common Pitfalls
- Forgetting that circle radius is 1, so center must be offset by 1 from all boundaries
- Off-by-one in distance checks (center-to-center ≥ 2, not ≥ 1)
- Diamond normal vectors pointing the wrong direction
- Simulated annealing cooling too fast (gets stuck in local minimum) or too slow (doesn't converge)
- Not tracking the best solution seen (only keeping the current one)
