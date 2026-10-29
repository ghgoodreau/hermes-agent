# Desktop performance stack

The canonical review is now in **NousResearch/hermes-agent**, native stack **#146603**. Open [PR #146602](https://github.com/NousResearch/hermes-agent/pull/146602) and click its **6/6** badge to navigate the focused PRs.

| Order | Change | Upstream PR | Branch |
| --- | --- | --- | --- |
| 1 | Keep streamed tokens out of navigation context | [#146597](https://github.com/NousResearch/hermes-agent/pull/146597) | `perf/desktop-stack-01-navigation` |
| 2 | Skip unchanged terminal snapshots and hidden row paints | [#146598](https://github.com/NousResearch/hermes-agent/pull/146598) | `perf/desktop-stack-02-terminals` |
| 3 | Ignore geometry-only changes in theme observation | [#146599](https://github.com/NousResearch/hermes-agent/pull/146599) | `perf/desktop-stack-03-theme` |
| 4 | Throttle idle previews while preserving active operations | [#146600](https://github.com/NousResearch/hermes-agent/pull/146600) | `perf/desktop-stack-04-preview` |
| 5 | Keep voice detection working when minimized | [#146601](https://github.com/NousResearch/hermes-agent/pull/146601) | `perf/desktop-stack-05-voice` |
| 6 | Reduce minimized-window streaming work safely | [#146602](https://github.com/NousResearch/hermes-agent/pull/146602) | `perf/desktop-stack-06-hidden-render` |

- Each branch targets its predecessor; the first targets main. All six remain drafts for review.
- Base reviewed: `fcc26fbd9f1641755e49793d7ed608f3b7214c3d`.
- Whole-set tip: `32d726ff871700fb8178bdddfae5b19e5ede86a8`.
- Whole-set tree: `09a0f439eb52a29ea467829876dc0f4847792afc`.
- At the 2026-10-29 publication audit, all six were mergeable and their reported CI checks had completed without failures. Skipped lanes are not claimed as exercised.

## Test the final state, then each layer

```sh
git fetch https://github.com/NousResearch/hermes-agent.git \
  'refs/heads/perf/desktop-stack-*:refs/remotes/perf-upstream/perf/desktop-stack-*'
git switch --detach refs/remotes/perf-upstream/perf/desktop-stack-06-hidden-render
```

Use the desktop's normal development setup and the manual scenario in each PR. To inspect intermediate cumulative states, switch to the corresponding earlier branch. Layer 6 needs the preview and voice safeguards; it is not a standalone background-throttling toggle.

## Review history and measurement scope

Fork PRs #1–#6 and earlier upstream drafts #146191, #146192, #146194, #146200 and #146201 are closed with replacement links. Their original branches and discussions remain preserved. This documentation-only branch is not an application layer; fork main was not rewritten.

The earlier **39.8% lower whole-Electron CPU** measurement belongs to upstream `780d5a33` versus the old complete stack `94d32f90`/`857f362b`, in a matched Linux software-rendered streaming workload. It is not a new benchmark of this rebased/review-fixed tree, an individual layer's benefit, or a GPU/battery percentage. The voice PR's capture comparison is tied to its actual target and candidate; voice alone has no demonstrated voice-off CPU gain.

Composer, Files-pane and quiet-backend follow-ons remain separate from these six changes. Raw profiles and private investigation notes stay local.
