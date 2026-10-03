# skeleton-loading

An agent skill for building skeleton screens that actually feel faster — not
just gray boxes that shimmer.

Most loading-state guidance stops at "show a skeleton while you fetch". That
advice ships two bugs into production:

- **Mounting at 0ms.** A fast response then produces a blank → skeleton flash →
  content sequence, which tests *worse* than showing no indicator at all. This
  skill gates the mount at 200ms.
- **No slow-connection path.** A skeleton carries zero duration information, so
  when the data takes 15 seconds the user just stares at it. This skill wires
  explicit 3s / 10s / error escalation gates.

It also pins the shimmer spec that the research actually supports: a
left-to-right directional sweep, 1.5–2s per pass, constant speed, low contrast,
one synchronized timeline per screen — never a pulse, never static.

## Install

```bash
npx skills add FranciscoYuster/skeleton-loading --agent claude-code
```

Other agents work too — swap `claude-code` for `codex`, `cursor`, `opencode`,
or pass `--agent '*'`. Add `-g` to install globally instead of per project.
See [vercel-labs/skills](https://github.com/vercel-labs/skills) for the CLI.

## What's inside

| File | Purpose |
| --- | --- |
| `skills/skeleton-loading/SKILL.md` | The runtime contract: 8 hard rules, a decision-gate table, execution steps, output contract |
| `skills/skeleton-loading/assets/skeleton.css` | Baseline shimmer implementation and the required a11y markup |
| `skills/skeleton-loading/references/research.md` | The evidence, with citations and open questions |

## Decision gates

The skill refuses the skeleton when something else fits better:

| Condition | Pattern |
| --- | --- |
| Wait < 200ms | No indicator |
| 200ms–3s, layout known | Skeleton |
| 200ms–3s, layout unknown | Inline spinner |
| Progress measurable | Determinate bar, never a skeleton |
| User-initiated, predictable result | Optimistic UI, no loader |
| Unbounded or > 3s | Skeleton plus escalation |
| `prefers-reduced-motion` | Text status, no skeleton |

## On the evidence

The research on skeleton screens is split and thin, and the skill says so
rather than cherry-picking:

- **Viget (2017), n = 136** — the skeleton produced the *longest* perceived
  wait: 2.82s, against 2.41s for a spinner and 2.29s for a blank screen.
- **"The effect of skeleton screens" (2018), n = 80, mobile** — found the
  opposite; the skeleton performed best on perceived duration.
- **LinkedIn's reported case** — skeleton pages perceived as faster than
  spinner pages at identical measured load time.

The studies disagree because they did not test the same artifact. "Skeleton"
covers both a frozen gray box and a slow directional shimmer, and those are
different interventions. Where results favor skeletons, the implementation was
animated.

Shimmer *direction* and *speed* have not been isolated by any study — the
1.5–2s left-to-right spec is convergent practice, not a measured optimum.
`references/research.md` marks that explicitly, along with the response-time
origins of the 3s and 10s gates.

## License

MIT — see [LICENSE](LICENSE).
