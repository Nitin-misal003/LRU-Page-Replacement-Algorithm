# LRU Page Replacement Simulator: Traditional LRU vs LRU-K

## 1. Objective
Simulate the traditional LRU page replacement algorithm and compare it with one research-based approach, **LRU-K**, on the same input (Operating Systems, Review 3).

## 2. Research paper used
E. J. O'Neil, P. E. O'Neil, G. Weikum, *The LRU-K Page Replacement Algorithm for Database Disk Buffering*, ACM SIGMOD, 1993.

## 3. Traditional LRU
On a page fault with all frames full, evict the page whose **last** reference is oldest. It uses only recency, so a burst of pages that are each used once (a sequential scan) pushes frequently used pages out of memory.

## 4. Research concept: LRU-K
Keep the times of the **last K references** of every page. The *backward K-distance* is the current time minus the time of the K-th most recent reference. Evict the page with the **largest** backward K-distance.
- A page with fewer than K references has infinite distance, so it is evicted first. One-time pages leave quickly.
- Ties among such pages are broken by ordinary LRU.
- With K = 1, LRU-K is identical to LRU (checked on 2,000 random inputs).

## 5. Comparison

| Criterion | Traditional LRU | LRU-K |
|---|---|---|
| Information kept per page | Last reference time | Last K reference times |
| Victim | Oldest last reference | Oldest K-th most recent reference |
| One-time (scan) pages | Can evict hot pages | Evicted first |
| Uses frequency? | No | Yes (recency + frequency) |
| New working set | Adapts fast | Adapts slower (needs K references) |
| Space | 1 timestamp/page | K timestamps/page, history may outlive the page |
| Victim search | O(F) | O(F) |

## 6. Simplifications
- Reference history is kept for evicted pages without limit. The paper limits this with a *Retained Information Period*.
- The paper's correlated-reference period is not modelled.
- Only page faults and hits are compared. No real I/O times.

## 7. Folder structure
```
index.html          page structure and static comparison table
css/style.css       styling
js/main.js          wires modules, handles Run / Reset / example selection
js/lru.js           traditional LRU (no DOM)
js/lruk.js          research-based LRU-K (no DOM)
js/validation.js    reference string, frame count and K validation
js/metrics.js       rates, metrics and LRU vs LRU-K comparison
js/presets.js       example inputs
js/ui.js            DOM rendering
```

## 8. How to run
Browsers block ES modules on `file://`. Serve the folder: VS Code *Live Server*, or `python -m http.server 8000` and open `http://localhost:8000`.

## 9. Using the simulator
Enter a reference string (numbers separated by spaces or commas, max 100), frames (1 to 10) and K (1 to 4), or pick an example. Run Simulation shows a comparison table, then a step table for each algorithm. Rows where LRU-K's frames differ from LRU are marked with a bar on the left. Click a row for its explanation.

## 10. Verified results (3 frames, K = 2)

| Example | Reference string | LRU faults | LRU-2 faults | Better |
|---|---|---|---|---|
| Basic | 7 0 1 2 0 3 0 4 2 3 | 8 (80.00%) | 8 (80.00%) | Tie |
| Hot pages + scan | 1 2 1 2 1 2 3 4 5 6 7 8 1 2 1 2 | 10 (62.50%) | 8 (50.00%) | LRU-K |
| Working set changes | 1 2 3 1 2 3 4 5 4 5 4 5 4 5 | 5 (35.71%) | 7 (50.00%) | LRU |

Earlier traditional-LRU tests still hold: `1 2 3 1 2 4 1 2 3 4` gives 6 faults, `1 1 1 1 1` gives 1 fault (3 frames), and `1 2 3 4 5` gives 5 faults (1 frame).

**Observation for the report:** LRU-K is not always better. It helps when frequently used pages are mixed with one-time references, and it costs extra memory and adapts more slowly when the working set changes.

## 11. Limitations
Small input limits, simplified history retention, no timing model, only two algorithms compared.

## 12. Future improvements
Add ARC, 2Q or LIRS for a wider comparison, a bounded retained-information period, a fault chart, and unit tests for `lru.js` and `lruk.js`.
