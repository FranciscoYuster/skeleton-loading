---
name: skeleton-loading
description: "Trigger: skeleton screen, shimmer, loading placeholder, perceived performance. Implement skeleton loading: shimmer spec, layout promise, timing gates, slow-connection escalation."
license: MIT
metadata:
  author: franciscoyuster (https://github.com/franciscoyuster)
  version: 1.0.0
---

# Skeleton Loading

## Activation Contract

Load when building or reviewing a loading state for content whose layout is
known before the data arrives, or when choosing between skeleton, spinner,
progress bar, and optimistic UI.

## Hard Rules

1. Mount the skeleton only after a **200ms delay**. At 0ms a fast response
   gives a blank→skeleton→content flash, worse than no indicator.
2. Never ship a static skeleton. No shimmer available, ask if use Shimmer or Spinner.
3. Shimmer is a directional sweep: left→right (reversed under RTL), 1.5–2s per
   pass, constant speed, low contrast, one synchronized timeline per screen.
   Never a pulse or opacity breathe.
4. Under `prefers-reduced-motion: reduce` render no skeleton and no shimmer —
   render `<p role="status">Loading…</p>` instead.
5. Box sizes, `aspect-ratio`, and item count derive from the real component's
   tokens. CLS on swap must be 0.
6. Never skeleton a conditional element; a box resolving to nothing is a broken
   layout promise.
7. Never leave a skeleton up on error, or past the step-4 gates.
8. Boxes are `aria-hidden="true"` and outside the tab order; the region carries
   `aria-busy`, flipped to `false` on resolve.

## Decision Gates

| Condition | Pattern |
| --- | --- |
| Wait < 200ms | No indicator |
| 200ms–3s, layout known | Skeleton |
| 200ms–3s, layout unknown | Inline spinner |
| Progress measurable | Determinate bar, never a skeleton |
| User-initiated, predictable result | Optimistic UI, no loader |
| Unbounded or > 3s | Skeleton plus step-4 escalation |
| `prefers-reduced-motion` | Text status (rule 4) |
| One region loading | Scope the skeleton to that region |

## Execution Steps

1. Classify the wait against Decision Gates; reject the skeleton if an earlier
   row matches.
2. Derive geometry and item count from the loaded component. Match the usual
   row count; text lines 60–80% width, last line shorter.
3. Apply `assets/skeleton.css`. Keep the mechanics, retune only tokens.
4. Wire escalation in the loading component, never the fetch layer: **200ms**
   mount skeleton · **3s** swap to an informational state (spinner plus "Still
   loading…", or partial render) · **10s** add agency (estimate, retry, cancel,
   background) · **error** replaces it with a named failure plus retry.
5. Fade in ~150ms, stagger list items 30–50ms, preserve scroll position.
6. Verify throttled, not on localhost. `navigator.connection` is a hint to
   start at lower fidelity, never a replacement for the timers.

## Output Contract

Per loading state touched, report: chosen pattern and the gate row justifying
it; shimmer params or the reduced-motion path; each step-4 gate wired or N/A
with a reason; measured CLS and throttled result; `aria-busy` target, hidden
boxes, tab order.

## References

- `assets/skeleton.css` — baseline shimmer implementation and a11y markup.
- `references/research.md` — evidence behind the hard rules, with citations.
