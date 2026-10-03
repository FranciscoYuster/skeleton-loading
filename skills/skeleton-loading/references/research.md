# Evidence behind the hard rules

Background for `../SKILL.md`. Not runtime instructions — read this to argue the
rules, cite them, or revise them when better evidence appears.

## The evidence is split and thin

Treat conclusions here as directional, not settled. Only a handful of
controlled studies exist, with small samples and different devices.

**Viget (2017), n = 136** — measured how long people *thought* they had waited.
The skeleton produced the **longest** perceived wait of the three conditions:

| Condition | Perceived wait |
| --- | --- |
| Skeleton | 2.82s |
| Spinner | 2.41s |
| Blank screen | 2.29s |

The skeleton group also took longer to complete the task and rated the wait
more negatively. This is the most-cited result and it is *against* skeletons.

**"The effect of skeleton screens" (2018), n = 80, mobile** — found the
opposite: the skeleton performed **best** on perceived duration.

**LinkedIn's own reported case** — users perceived skeleton pages as loading
faster than identical pages with spinners at the same measured load time.

The studies disagree because they did not test the same artifact. "Skeleton"
covers both a frozen gray box and a slow left-to-right shimmer, and those are
different interventions. Where results favor skeletons, the implementation was
animated. Hence SKILL.md rules 2 and 3: a static skeleton is not a weaker
skeleton, it is a different and worse pattern.

## Why users report hating skeletons

A learned-association hypothesis, not a measured finding, but it explains the
intensity of the reaction better than the perceived-duration numbers do:
skeletons historically appeared on slow connections, so gray boxes became a
signal for "slow". The association fires even on a 2-second load that was
objectively fast. Users are not reporting the wait; they are reporting the
cue.

Practical consequence: a skeleton is not a safe default. It is a technique with
preconditions. Meet every precondition in SKILL.md or pick another pattern.

## Why rule 1 is 200ms, not 0ms

A skeleton shown for under ~200ms produces blank → skeleton flash → content.
That sequence tests worse than showing nothing, because it adds visual
complexity with no information. Mounting at 0ms guarantees this flash on every
fast response — which is most responses in production. Delay the mount; fast
requests then never render a loader at all, which is the correct outcome for
anything under the 200ms gate.

## Why rule 4 drops the skeleton under reduced motion

Three facts collide:

1. A large moving gradient is a vestibular trigger, so shimmer must stop under
   `prefers-reduced-motion`.
2. A frozen gray box is the worst-measured loading state (Viget, above).
3. A spinner is also motion.

So neither "freeze the shimmer" nor "swap in a spinner" is consistent. The only
option that satisfies all three is to carry the information as **text** — a
`role="status"` message — and drop the visual placeholder entirely. Users who
request reduced motion get the one loading state that conveys the most and
animates the least.

## Open questions

- No study isolates shimmer *direction* or *speed*; the 1.5–2s left-to-right
  spec in rule 3 is convergent practice plus the animated/static split above,
  not a measured optimum. Worth testing.
- The 3s and 10s escalation gates come from established response-time
  guidance (10s is the classic limit for holding attention without feedback),
  not from skeleton-specific research.

## Sources

- Viget — *A Bone to Pick with Skeleton Screens*:
  https://www.viget.com/articles/a-bone-to-pick-with-skeleton-screens
- *The effect of skeleton screens: Users' perception of speed and ease of
  navigation* (2018):
  https://www.researchgate.net/publication/326858669
- *Skeleton Loading Screen: The CSS and the Evidence*:
  https://theplusaddons.com/blog/skeleton-loading-screen/
- *Everything you need to know about skeleton screens*:
  https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a
