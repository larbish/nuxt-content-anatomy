# Hand-off: "Nuxt Content v4 Anatomy" HTML page

- **File:** `index.html`, one self-contained HTML document: inline CSS and JS, no build step, and no external requests apart from GitHub links.
- **Owner:** Baptiste Leproux (GitHub `larbish`).
- **Created:** 23–24 Nov 2026, in a Claude session.
- **Visual model:** [larbish/eve-anatomy](https://github.com/larbish/eve-anatomy). The CSS, legend box, brick-and-inspector layout and timelines are adapted from it.

## 1. What it is

The page is an interactive explainer of how Nuxt Content v4 works, written for the live-mode design. It answers five questions:

- What does the v4 module add on top of comark-content?
- How does a read move through it, in server-only, hybrid and live modes?
- What are the manifest and the snapshot, and when are they written and read?
- What does draft PR #42 (live surfaces, per-request ref) change?
- In the proposed live mode that replaces #42, when is `live.ref` called, when is GitHub called, and what is read from the cache or the snapshot?

It also compares v4 with Nuxt Content v3 and with comark-content on its own.

The page is static. It describes fixed revisions and does not fetch anything at runtime.

## 2. Sources and revisions

| Code | Revision |
| --- | --- |
| `nuxt/content`, branch `v4` | `1e99a5a` (#60, #61, #63) |
| Draft PR #42, `feat/live-surfaces`, on `nuxt/content-v4-temp` | `3744e90` |
| Proposed live mode | no code |
| comark-content v0.6.2 (private, comarkdown org) | `e0a3214` |
| comark-docs | `a3ebf99` |
| `nuxt/content` v3.17.0 (main) | `3c49cff` |

The page is built from Baptiste's write-up "Nuxt Content v4: architecture and behaviour", and its claims were spot-checked against a local checkout of `content-v4-temp`. The v3 facts were researched separately at `3c49cff`. Every claim links to the lines it comes from.

The page shows v4 behaviour only. The v3 compat layer appears as dashed grey blocks with a red badge, and draft #42 as dashed darker-grey blocks with a blue badge; #42 is explained in its own tab.

PR #60 (`app/content.ts`), #61 (layer `server/content.ts`) and #63 (hybrid deadlock fix, comark-content 0.6.2) are merged into `v4` and shown as v4 behaviour. `app/content.ts` has its own entry brick (`appentry`).

`nuxt/content-v4-temp` is frozen at `d48d094`. Its commits are on the `v4` branch of `nuxt/content`, where `1e99a5a` has the same `src/`, `test/`, `examples/`, `migration.md` and `package.json` trees as `d48d094`, so the `v()` links moved there with the same line numbers. #42 only exists on `content-v4-temp`, so `p42()` links still point there.

The proposed live mode (added 24 Nov 2026) is the design Baptiste will open as a new PR on `nuxt/content` `v4`, rewriting #42 from scratch. No code exists for it. It comes from the design thread, not from a repository, so nothing on the page links to lines of the proposal. Its tab links to the comark-content 0.6.2 and v4 code it would run on, and to #42 for comparison. The comark-content mechanics it shows were read in the private repository at `e0a3214`: pinned instances, `ref:` cache namespaces, `latest-ref` and `by-hash` entries, the foreign build manifest, and reuse by blob ID. comark-content 0.6.3 is out and changes neither `withRef` nor snapshots. The links stay on 0.6.2, which both repos still lock, because the comment clean-up in 0.6.3 (#162) moved the line numbers.

## 3. Structure

There are six tabs. Their state is stored in `location.hash` and `localStorage['nc-anatomy-tab']` (the old `#refs` hash maps to `#live`).

1. **Global overview:** nested bricks. The Nuxt app holds the entry bricks (`server/content.ts`, `app/content.ts`, module options, and the two compat entries), then `createNuxtContent()`, then the comark-content instance. Next to them are boxes for the Nitro server, Build output, App side and Dev.
2. **A read, step by step:** a step-through timeline with five lanes (Browser, App content, Nitro route, Instance, Artifacts · cache) in two modes: `server-only` and `hybrid`. Each mode has "what if" cards.
3. **Manifest & snapshot:** a comparison table with a v3 SQL-dump column, a lifecycle timeline, and rule cards.
4. **Live mode in #42:** the PR's state, v4 vs #42 formulas, a live read step-through, the stamped-snapshot build timeline, `withRef()` cards, what #42 adds, what it changes in existing v4 blocks (built from each brick's `br.pr42`), and limits.
5. **Live mode · proposed:** the design that replaces #42, labelled "proposed" everywhere. It has the entry and a per-request formula; a matrix of seven situations (commit in the shared cache, empty cache with the same commit, empty cache with a newer commit, a push, `maxAge` passed, `live.ref()` fails, build) against `live.ref()`, GitHub, `cache.driver` and the build snapshot; a step-through timeline per situation with one lane for each of those; the push flow with a sketch of the app's webhook route; a kept / deleted / updated table against #42; what it changes in existing v4 blocks (built from each brick's `br.prop`); and the open points.
6. **v3 · v4 · comark-content:** a 12-row comparison table and a "who owns what" table.

The legend box sits above the tabs and has three rows:

- **Path:** solid is v4, dashed grey is v3 compat (to be deleted), dashed darker grey is #42 live mode (draft), dashed purple is the proposed live mode (no code yet).
- **Owner:** comark-content, Nuxt Content, v3 compat, #42 live mode, proposed (see section 4).
- **Runs in:** Config, Build, Nitro, Browser or Dev.

The **inspector** is a sticky panel on the right; on screens 980px wide or less it becomes a bottom sheet. For a brick it shows the owner, where it runs, a #42 badge when relevant, what it does, notes ("v3 compat", "From comark-content", "In v3"), usage, sources and a zoom link. For a timeline moment it shows the owner and runs-in badges (from the row, the `MOV` override map, or its linked brick), the lane, the explanation and sources.

## 4. Colour meaning (asked for by Baptiste)

- **Nuxt Content** (the module) is green.
- **comark-content** (the engine) is yellow.
- **v3 compat** is red, because it is to be deleted.
- **#42 live mode** is blue, on dashed darker-grey bricks. Amber was avoided so it wouldn't clash with the comark-content yellow.
- **Proposed live mode** is purple, on dashed purple bricks and purple timeline cards. It always carries a "proposed" badge, because no code exists for it.
- **Open** (not decided yet) is an amber status badge on a dashed amber card. It is a status like kept / deleted / updated, not an owner.
- Badges with no owner meaning, such as inspector labels and "The app", are neutral grey.

## 5. How to edit

Everything is in one IIFE at the end of `index.html`.

- **Links:** `L(rev, path, lines)` builds GitHub links. The shortcuts are `v()` for v4, `p42()`, `cc()` for comark-content, `cd()` for comark-docs and `v3()`. `src/` is prepended automatically for `v4` and #42 paths. To move to a new revision, change the SHA in `REV`, then check the line numbers.
- **Bricks:** each brick is an entry in `B` with these fields: `name`, `api`, `own` (`cc` / `nc` / `v3`), `rt`, `on` (`['pr42']` marks a #42-only brick), `what`, `src[]`, `v3`, `cc`, `br.pr42` (what #42 changes, listed in the Live mode tab), `note`, `open` and `zoom`. To add a brick, add an entry to `B` and an empty `<button class="brick" data-id="…">` in the markup.
- **Timelines:** `makeTimeline(mount, { lanes, rows })`. Each row is `{ lane, t, s, x, src, brick, v3, isNew, warn }`. The read timeline's rows are in `READ.so` / `READ.hy`; the live read is `READ.live`, shown in the Live mode tab. The lifecycle rows are built in `renderArtifacts()`, the #42 build rows in `renderLive()`.
- **Proposed live mode:** everything is in `renderProposed()` and the data above it. `SITS` holds the situations: `{ id, label, name, sub, ref, gh, cache, snap, rows }`, where `ref` and `gh` are `[called, text]`, `cache` and `snap` are lists of `['r' | 'w' | '—', text]`, and `rows` is the timeline. The matrix, the picker and the summary strip are all built from `SITS`, so a situation is described once. `PM` holds the moments several situations share. A row takes `prop` (proposed behaviour, purple, no source), `skip` (a call that is not made, dashed grey), `open` (not decided, dashed amber) and `und` (a "Not decided" note in the inspector). Rows without `prop` are comark-content as it works today and must link to its code. Proposed bricks have `on: ['prop']` and an empty or engine-only `src`; `isOpen` adds the open badge. Do not add source links that suggest code exists for the proposal, and keep undecided points in the "Still open" cards.
- **Tables:** built in `renderArtifacts()`, `renderLive()`, `renderProposed()` and `renderCompare()`, once on load.
- **Usage:** the `U` map (merged into `B` as `use`) holds `{ intro, blocks: [{ t, code }], after, src }` per brick, rendered in the inspector under "Usage". Examples come from `migration.md` and `examples/hybrid-v4-pure` on `v4`, and from #42's `migration.md` for live surfaces.
- **Tooltips:** `TIPS`, shown on any element with `data-tip`.
- **Fonts:** Bricolage Grotesque, IBM Plex Sans and IBM Plex Mono when they are installed, otherwise system fonts. Unlike eve-anatomy, Google Fonts are not loaded.

## 6. Open points

- PR states change fast. Re-check #42, and open PRs #58 (typed `createNuxtContent()`) and #62 (build-time values in `server/content.ts`), before relying on the page.
- The comark-content details come from the write-up's v0.6.2 links, because the repo is private. The proposed tab's engine steps were read in the repository itself at `e0a3214`.
- The proposed live mode is not confirmed. Still open: `forgeRef(forgeOptions)`; whether the build reads the snapshot from the source at the commit `live.ref` returns (leaning yes); whether `live` is only accepted for a source that can be pinned to a commit. Not decided: how old instances are released, types, dev behaviour, the `resolveContent()` and `handleContent()` helpers, the key the commit is stored under, and whether a request waits for `live.ref()` once `maxAge` has passed (the timeline draws it as waiting).
- Once the new PR exists on `nuxt/content`, add its revision to `REV`, link the proposed rows to its lines, and drop the "proposed" labels that no longer apply.
