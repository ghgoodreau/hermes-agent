# Desktop performance review stack

Canonical review location: [ghgoodreau/hermes-agent, PRs #1–#6](https://github.com/ghgoodreau/hermes-agent/pull/1). These are six draft PRs in **native GitHub stack #7**, not a monolithic PR or just cross-linked independent branches.

Durable public index: [`tracking/desktop-performance-stack:PERFORMANCE-STACK.md`](https://github.com/ghgoodreau/hermes-agent/blob/tracking/desktop-performance-stack/PERFORMANCE-STACK.md). This documentation-only branch is separate from all six application layers.

Open [the whole-set tip, PR #6](https://github.com/ghgoodreau/hermes-agent/pull/6), then click the **6/6 stack-position badge** beside Draft. The navigator lists all six layers and opens each focused diff. The navigator and a click through to layer 3 were exercised in the web UI. The CLI's standalone `/stack/7` URL returned 404 signed out; the PR badge is the verified entry point.

## Canonical groups

| Layer | Scope | Fork PR | Cumulative branch | Exact head | Upstream counterpart |
| --- | --- | --- | --- | --- | --- |
| 1 | Navigation context | [#1](https://github.com/ghgoodreau/hermes-agent/pull/1) | `stack/desktop-01-navigation` | [`50f8d3cb`](https://github.com/ghgoodreau/hermes-agent/commit/50f8d3cba9dc0a97578fe52f74a7bf985ad4de93) | [#146191](https://github.com/NousResearch/hermes-agent/pull/146191) |
| 2 | Terminal retention and hidden rows | [#2](https://github.com/ghgoodreau/hermes-agent/pull/2) | `stack/desktop-02-terminals` | [`836b7db8`](https://github.com/ghgoodreau/hermes-agent/commit/836b7db8a027f2cba64f86e29ffdc7ce2b9a09b9) | [#146192](https://github.com/NousResearch/hermes-agent/pull/146192) |
| 3 | Theme observation | [#3](https://github.com/ghgoodreau/hermes-agent/pull/3) | `stack/desktop-03-theme` | [`9cc13b44`](https://github.com/ghgoodreau/hermes-agent/commit/9cc13b447c3b0870bc8966c569b2fae185d10679) | [#146194](https://github.com/NousResearch/hermes-agent/pull/146194) |
| 4 | Owned preview activity | [#4](https://github.com/ghgoodreau/hermes-agent/pull/4) | `stack/desktop-04-preview` | [`523d9c51`](https://github.com/ghgoodreau/hermes-agent/commit/523d9c51b095f6d46ab6d1226c93b6ffd5515b0d) | [#146200](https://github.com/NousResearch/hermes-agent/pull/146200) |
| 5 | Audio-clock voice prerequisite | [#5](https://github.com/ghgoodreau/hermes-agent/pull/5) | `stack/desktop-05-voice` | [`26b5b2ca`](https://github.com/ghgoodreau/hermes-agent/commit/26b5b2cae8d185bc8f8c9faa6cddada451ebdbda) | [#146201](https://github.com/NousResearch/hermes-agent/pull/146201) |
| 6 | Safe hidden-window rendering | [#6](https://github.com/ghgoodreau/hermes-agent/pull/6) | `stack/desktop-06-hidden-render` | [`857f362b`](https://github.com/ghgoodreau/hermes-agent/commit/857f362bd2ce1593d80bc50e351b3dc153c019e2) | Fork-only for now |

Trunk: `stack/desktop-base` at upstream **`780d5a3313d7e1f64c0a0f3ec6b644d50f156e90`**. Each layer targets the preceding branch. The existing fork `main` at `7d4648461a4f294dc7abe710f8bb8ec50ad79a5b` was not changed. The pinned trunk avoids silently rebasing the measured code onto a different fork default branch.

- Layers 1–4 retain their existing cumulative commits. Terminal retention and hidden DOM rows are one component group with two reviewable commits.
- Layer 5 is the existing voice change on the performance chain. Its patch identity and all eight runtime/test file blobs match the independent upstream voice PR. Its standalone upstream version still targets main.
- Layer 6 contains the native rendering policy **and all required recovery/Stop/preview safeguards** as one commit. Unsafe intermediate experimental checkpoints are not separately mergeable PR layers.
- Navigation, terminals and theme are ordered for review, not because each is a runtime prerequisite of the next. The last layer genuinely needs preview activity and the voice prerequisite.
- Upstream drafts remain open and unchanged, with comments linking their fork counterparts. No sixth upstream PR was attempted while the contribution limit applies. No draft was merged or marked ready.

## Test the final state, then each layer

Fetch the named fork branches into a clean disposable checkout, then inspect the top layer first:

```sh
git fetch https://github.com/ghgoodreau/hermes-agent.git \
  'refs/heads/stack/desktop-*:refs/remotes/perf-fork/stack/desktop-*'
git switch --detach refs/remotes/perf-fork/stack/desktop-06-hidden-render
```

Use the focused commands and manual scenarios in each PR. To check earlier cumulative states, detach at `desktop-01-navigation` through `desktop-05-voice` in order. Builds, tests, application runs and profiling belong inside the Firecracker lab, not on the host. Git/GitHub publication and static byte verification may run on the host.

The whole-set source tree is **`2d62d01e12ad64620bde8533724f0f593594cc19`**, exactly equal to previously measured candidate `94d32f907c1a98f19343b3141ee4a318e4de556d`. Publication changes commit organization, not application files. The new top commit is **`857f362bd2ce1593d80bc50e351b3dc153c019e2`**. The original measured branch and its history remain preserved.

Existing guest checks, native preview/voice/recovery scenarios and the completed benchmark cover that identical source tree. No new performance run was performed merely to republish it. The new fork CI jobs were queued at the publication audit; this is not a claim that the new PR checks finished green.

## Benchmark attribution

The following is a **whole-stack** comparison, not an estimate of any single layer's marginal benefit. Baseline is upstream `780d5a33`; candidate is the complete source tree shared by `94d32f90` and `857f362b`. Eight serial ABBA BAAB runs, four per build, in Linux Firecracker with software graphics, production Electron/gateway, matched synthetic 59-reply history, two inline HTML cards, one visible and one minimized window, voice off and a 24-paragraph stream. Medians [observed min–max].

| Metric | Upstream baseline | Whole-stack candidate |
| --- | ---: | ---: |
| Total Electron CPU, ms/s | 2135 [2105–2152] | 1286 [1229–1351] |
| Visible renderer CPU, ms/s | 980 [965–1002] | 712 [680–746] |
| Minimized renderer CPU, ms/s | 916 [908–932] | 366 [352–392] |
| Minimized layouts/s | 29.67 [29.36–29.78] | 0.200 [0.200–0.200] |

Whole Electron CPU is 39.8% lower in that workload. No demonstrated typing-latency improvement, physical GPU/battery percentage or universal 2× speedup. The standalone voice PR established no voice-off CPU improvement; its separate measurements stay on upstream #146201. The earlier unsafe 46.0% candidate is not this result. Raw profiling data and private investigation notes remain local rather than being uploaded with this public-safe index.

## Updating and moving upstream

- Keep the six canonical group boundaries. Change the owning layer, then propagate the change through later layers and rerun affected checks/native scenarios inside the VM.
- Preserve the pinned reference and original measured branch. Do not silently substitute a changed tree under old benchmark numbers.
- The official `github/gh-stack` extension created this stack with `gh stack link --remote fork --base stack/desktop-base`, followed by the six branch names in table order. `link` created remote native-stack membership without adopting local rebase tracking. Do not assume local `gh stack sync` state exists.
- Once upstream write access exists, reconcile the chain with then-current upstream main and verify it. Publish the six branches into the upstream repository and create its native stack with the same boundaries. A PR cannot switch its head repository: replace/cross-link the upstream drafts only when the replacement stack is ready, preserving existing review history.
- Until then, use fork PR #1 as the review map and #6 as the whole-set checkout. Keep these drafts unmerged while staging upstream delivery.
