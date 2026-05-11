[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/I7NCKCh8)
# Week 10 Coding #8: Haunted Hotel Sweep

## Summary

This assignment models a haunted hotel as a graph where each room or area is a node and each connection between rooms is an edge stored in an adjacency list. BFS (Breadth-First Search) explores the hotel level by level using a queue, visiting all immediate neighbors before going deeper, which produces the shortest-path-style sweep order. DFS (Depth-First Search) explores the hotel by going as deep as possible along one path before backtracking, using a stack to track the next area to visit. The `visited` set is essential in both algorithms to prevent revisiting areas we have already swept, which avoids infinite loops in rooms that connect back to each other (cycles) and ensures every area is counted exactly once.

---

## Approach

- **Getting neighbors safely:** Used `graph.get(area, [])` so that if an area is not in the graph, an empty list is returned instead of raising a `KeyError`.
- **Checking whether a path exists:** First validated that both `start` and `target` exist in the graph, then ran BFS from `start`, returning `True` the moment `target` was dequeued. If the queue empties without finding it, returned `False`.
- **BFS with a queue:** Initialized a `deque` with the start area and a `visited` set. On each iteration, popped from the left, appended to the result list, then enqueued any unvisited neighbors, marking them visited immediately on enqueue to avoid duplicates entering the queue.
- **DFS with a stack:** Initialized a plain list as a stack with the start area. On each iteration, popped from the right, skipped if already visited, otherwise marked visited, appended to the result, then pushed neighbors in `reversed(...)` order so the first neighbor ends up on top and is visited first — preserving the original neighbor order in the output.
- **Preventing repeated visits:** Both BFS and DFS maintain a `visited` set that is checked before processing any area, ensuring no area is added to the result more than once even in graphs with cycles or multiple paths to the same node.

---

## Complexity

### `get_neighbors`

- Time: O(1)
- Space: O(1)
- Why: A single dictionary lookup with `.get()` is constant time, and no new data structure is created — we just return the existing list reference.

### `has_path`

- Time: O(V + E)
- Space: O(V)
- Why: In the worst case BFS visits every vertex (V) and traverses every edge (E). The queue and visited set each hold at most V elements.

### `bfs_order`

- Time: O(V + E)
- Space: O(V)
- Why: Every vertex is enqueued once and every edge is examined once. The result list, queue, and visited set together hold O(V) elements.

### `dfs_order`

- Time: O(V + E)
- Space: O(V)
- Why: Every vertex is pushed onto the stack at most once per incoming edge, but only processed once thanks to the visited check. The stack and visited set are at most O(V) in size.

### Stretch: `count_reachable_areas`

- Time: O(V + E)
- Space: O(V)
- Why: It delegates entirely to `bfs_order`, which already runs in O(V + E) time and O(V) space. Taking the length of the returned list is O(1).

---

## Edge-Case Checklist

- [x] empty graph
- [x] missing start area
- [x] missing target area
- [x] `start == target`
- [x] graph with a cycle
- [x] disconnected graph
- [x] area with no neighbors

**Notes on tricky edge cases:**

- **`start == target`:** BFS naturally handles this because it checks `current == target` after dequeuing, so the start node itself satisfies the condition immediately. No special-case branch needed.
- **Disconnected graph:** Because BFS and DFS only explore nodes reachable from `start`, disconnected components (like the Locked Wing) are simply never visited — which is the correct behavior. `has_path` correctly returns `False` across disconnected components.
- **Cycles:** Without a `visited` set, rooms like Lobby → Hallway → Lobby would loop forever. Marking nodes visited on enqueue (BFS) or on pop (DFS) breaks every cycle cleanly.
- **Area with no neighbors (`Attic`):** `graph.get("Attic", [])` returns `[]`, so the neighbor loop does nothing and only the start node itself appears in the result — confirmed by `test_dfs_order_area_with_no_neighbors`.

---

## Tests Added

- No new tests were added; all 18 tests provided in the original test file pass without modification.
- Verified the stretch tests (`test_count_reachable_areas_from_lobby_stretch` and `test_count_reachable_areas_missing_start_stretch`) also pass.
- Ran the full suite locally and confirmed **18/18 passed** with zero failures or warnings.

---

## Known Limitations

```text
No known limitations.
```

---

## Assistance & Sources

AI used? Y

It helped with:

- **Explanations:** Clarified why neighbors should be pushed in `reversed(...)` order during iterative DFS so that the traversal output matches the original neighbor order from the adjacency list.
- **Debugging:** Confirmed that BFS should mark nodes visited on *enqueue* (not on *dequeue*) to prevent duplicate entries in the queue when multiple paths lead to the same node.
- **Syntax reminders:** Reminded me that `collections.deque` supports `popleft()` for O(1) front removal, unlike a plain list which would be O(n).

Other sources used:

- Course lecture notes on BFS and DFS traversal algorithms
- Python documentation for `collections.deque`