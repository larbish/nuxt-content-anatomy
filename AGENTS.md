# Hand-off: "Nuxt Content v4 Anatomy" HTML page

- **File:** `index.html`, one self-contained HTML document: inline CSS and JS, no build step, and no external requests apart from GitHub links.
- **Owner:** Baptiste Leproux (GitHub `larbish`).
- **Created:** 23–24 Nov 2026, in a Claude session.
- **Visual model:** [larbish/eve-anatomy](https://github.com/larbish/eve-anatomy). The CSS, legend box, brick-and-inspector layout and timelines are adapted from it.

## 1. What it is

The page is an interactive explainer of how Nuxt Content v4 works. It answers three questions:

- What does the v4 module add on top of comark-content?
- How does a read move through it, in server-only and hybrid modes?
- What are the manifest and the snapshot, and when are they written and read?

It also shows the coming worker search (section 2), and compares v4 with Nuxt Content v3 and with comark-content on its own.

The page is static. It describes fixed revisions and does not fetch anything at runtime.

## 2. Sources and revisions

| Code | Revision |
| --- | --- |
| `nuxt/content-v4-temp`, branch `v4` | `d48d094` (#60, #61, #63) |
| comark-content v0.6.2 (private, comarkdown org) | `e0a3214` |
| comark-docs | `a3ebf99` |
| `nuxt/content` v3.17.0 (main) | `3c49cff` |
| Worker search: comarkdown/comark-content#172 | `cf8a861` |
| Worker search: nuxt/content#3931 (base `v4`) | `816307f` |

The page is built from Baptiste's write-up "Nuxt Content v4: architecture and behaviour", and its claims were spot-checked against a local checkout of `content-v4-temp`. The v3 facts were researched separately at `3c49cff`. Every claim links to the lines it comes from.

The page shows v4 behaviour, plus the coming worker search. The v3 compat layer appears as dashed grey blocks with a red badge.

### Worker search (coming)

Two PRs that ship together, drawn the way draft #42 (live surfaces) used to be: dashed darker-grey blocks with a blue "worker search" badge next to their owner badge. Only their final state is shown; Nuxt Content uses the comark-content PR directly, with no 0.6.2 workarounds.

- **comarkdown/comark-content#172**: `comark-content/worker` with `serveContentWorker(createHub)` and the main-thread `connectContentWorker(worker)` (`fetch`, `init`, `update`, `remove`, `terminate`), and `buildSearchIndex()` on the search plugin. The names can still change in review. Navigation by instance names was dropped from it, and the sqlite-wasm Vite plugin moved to a separate comark-content PR, so Nuxt Content keeps its own `sqliteWorkerFilesPlugin()` for now.
- **nuxt/content#3931** (base `v4`), pinned to cc#172's pkg.pr.new build: `app/content.worker.ts` with `createNuxtContentWorker({ plugins })` from `@nuxt/content/worker`, looked up across layers; `useNuxtContentWorker({ instances })` with `status`, `warmup`, `search`, `get` and `list`; the generated worker entry, kept out of preloads; snapshots published in server-only mode when the worker file exists. The worker uses the build's `app.baseURL`, and `init()` isn't exposed yet.
- **comarkdown/comark-docs#102** moves comark-docs' search worker to the comark-content API to try it. It isn't meant to be merged and has no brick; the `ccworker` note mentions it.

Sources: the PRs' code at the head commits above, checked against their descriptions. The bundle sizes come from the first prototype's test fixture and weren't re-measured after the rebuild on the comark-content PR.

PR #60 (`app/content.ts`), #61 (layer `server/content.ts`) and #63 (hybrid deadlock fix, comark-content 0.6.2) are merged into `v4` and shown as v4 behaviour. `app/content.ts` has its own entry brick (`appentry`).

## 3. Structure

There are four tabs. Their state is stored in `location.hash` and `localStorage['nc-anatomy-tab']`.

1. **Global overview:** nested bricks. The Nuxt app holds the entry bricks (`server/content.ts`, `app/content.ts`, module options, and the two compat entries), then `createNuxtContent()`, then the comark-content instance. Next to them are boxes for the Nitro server, Build output, App side and Dev. The worker search bricks sit with the blocks they belong to: `app/content.worker.ts` (`wentry`) in Entry, `comark-content/worker` (`ccworker`) and `buildSearchIndex()` (`ccindex`) in the comark-content instance, Worker build (`wbuild`) in Build output, and `useNuxtContentWorker()` (`wcompose`) in App side.
2. **A read, step by step:** a step-through timeline with five lanes (Browser, App content, Nitro route, Instance, Artifacts · cache) in two modes: `server-only` and `hybrid`. Each mode has "what if" cards. The server-only read ends with four worker search moments and a static-host card.
3. **Manifest & snapshot:** a comparison table with a v3 SQL-dump column, a lifecycle timeline, and rule cards.
4. **v3 · v4 · comark-content:** a 12-row comparison table and a "who owns what" table.

The legend box sits above the tabs and has three rows:

- **Path:** solid is v4, dashed grey is v3 compat (to be deleted), dashed darker grey with the blue badge is worker search (coming).
- **Owner:** comark-content, Nuxt Content, v3 compat (see section 4).
- **Runs in:** Config, Build, Nitro, Browser or Dev.

The **inspector** is a sticky panel on the right; on screens 980px wide or less it becomes a bottom sheet. For a brick it shows the owner, where it runs, the worker search badge when relevant, what it does, a blue "Worker search · adds" note on new bricks or "Worker search · changes" on a v4 brick the PRs change, notes ("v3 compat", "From comark-content", "In v3"), usage, sources and a zoom link. For a timeline moment it shows the owner and runs-in badges (from the row, the `MOV` override map, or its linked brick), the lane, the explanation and sources.

## 4. Colour meaning (asked for by Baptiste)

- **Nuxt Content** (the module) is green.
- **comark-content** (the engine) is yellow.
- **v3 compat** is red, because it is to be deleted.
- **Worker search** is blue, on dashed darker-grey bricks, like draft #42 was. It isn't an owner: each of these bricks also carries its comark-content or Nuxt Content badge. Amber was avoided so it wouldn't clash with the comark-content yellow.
- Badges with no owner meaning, such as inspector labels, are neutral grey.

## 5. How to edit

Everything is in one IIFE at the end of `index.html`.

- **Links:** `L(rev, path, lines)` builds GitHub links. The shortcuts are `v()` for v4, `cc()` for comark-content, `cd()` for comark-docs and `v3()`. `src/` is prepended automatically for content-v4-temp paths. To move to a new revision, change the SHA in `REV`, then check the line numbers.
- **Worker PR links:** `wpr(k, path, lines)` with `k` = `cc` or `nc` links to a file and lines at the PR's head commit in `WPR`; `prl(k)` links the PR itself. When a PR gets new commits, update its SHA in `WPR` and check the line numbers.
- **Bricks:** each brick is an entry in `B` with these fields: `name`, `api`, `own` (`cc` / `nc` / `v3`), `rt`, `on` (`[]` draws it dashed grey, off the v4 path; `['worker']` marks a worker search brick), `why` (a short line on the brick), `what`, `src[]`, `v3`, `cc`, `compat`, `br.worker` (what the worker PRs change in a v4 brick, shown in the inspector), `note`, `open` and `zoom`. To add a brick, add an entry to `B` and an empty `<button class="brick" data-id="…">` in the markup.
- **Timelines:** `makeTimeline(mount, { lanes, rows })`. Each row is `{ lane, t, s, x, src, brick, v3, warn, isNew }`; `isNew` marks a worker search moment (blue card and badge). The read timeline's rows are in `READ.so` / `READ.hy`. The lifecycle rows are built in `renderArtifacts()`.
- **Tables:** built in `renderArtifacts()` and `renderCompare()`, once on load.
- **Usage:** the `U` map (merged into `B` as `use`) holds `{ intro, blocks: [{ t, code }], after, src }` per brick, rendered in the inspector under "Usage". Examples come from `migration.md` and `examples/hybrid-v4-pure` on `v4`, and for worker search from the comark-content PR's Vite docs and the Nuxt Content PR's public API.
- **Tooltips:** `TIPS`, shown on any element with `data-tip`.
- **Fonts:** Bricolage Grotesque, IBM Plex Sans and IBM Plex Mono when they are installed, otherwise system fonts. Unlike eve-anatomy, Google Fonts are not loaded.

## 6. Open points

- PR states change fast. Re-check open PRs #58 (typed `createNuxtContent()`) and #62 (build-time values in `server/content.ts`) before relying on the page.
- The comark-content details come from the write-up's v0.6.2 links, because the repo is private.
- Worker search: re-check #172 and #3931 for new commits and renames in review. Switch `wbuild` to the comark-content sqlite plugin once its separate PR lands. Once both are merged into `v4`, drop the blue marking and move the links to `v()` / `cc()`.
