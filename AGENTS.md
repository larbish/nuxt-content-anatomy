# Hand-off: "Nuxt Content v4 Anatomy" HTML page

- **File:** `index.html`, one self-contained HTML document: inline CSS and JS, no build step, and no external requests apart from GitHub links.
- **Owner:** Baptiste Leproux (GitHub `larbish`).
- **Created:** 23–24 Nov 2026, in a Claude session.
- **Visual model:** [larbish/eve-anatomy](https://github.com/larbish/eve-anatomy). The CSS, legend box, brick-and-inspector layout and timelines are adapted from it.

## 1. What it is

The page is an interactive explainer of how Nuxt Content v4 works, written for the live-mode design. It answers four questions:

- What does the v4 module add on top of comark-content?
- How does a read move through it, in server-only, hybrid and live modes?
- What are the manifest and the snapshot, and when are they written and read?
- What does draft PR #42 (live surfaces, per-request ref) change?

It also compares v4 with Nuxt Content v3 and with comark-content on its own.

The page is static. It describes fixed revisions and does not fetch anything at runtime.

## 2. Sources and revisions

| Code | Revision |
| --- | --- |
| `nuxt/content-v4-temp`, branch `v4` | `30efe01` |
| Draft PR #42, `feat/live-surfaces` | `3744e90` |
| comark-content v0.6.2 (private, comarkdown org) | `e0a3214` |
| comark-docs | `a3ebf99` |
| `nuxt/content` v3.17.0 (main) | `3c49cff` |

The page is built from Baptiste's write-up "Nuxt Content v4: architecture and behaviour", and its claims were spot-checked against a local checkout of `content-v4-temp`. The v3 facts were researched separately at `3c49cff`. Every claim links to the lines it comes from.

The page shows v4 behaviour only. The v3 compat layer appears as dashed grey blocks with a red badge, and draft #42 as dashed darker-grey blocks with a blue badge; #42 is explained in its own tab.

Draft PR #60 (`feat/app-content`) was intentionally left out.

## 3. Structure

There are five tabs. Their state is stored in `location.hash` and `localStorage['nc-anatomy-tab']` (the old `#refs` hash maps to `#live`).

1. **Global overview:** nested bricks. The Nuxt app holds the entry bricks, then `createNuxtContent()`, then the comark-content instance. Next to them are boxes for the Nitro server, Build output, App side and Dev.
2. **A read, step by step:** a step-through timeline with five lanes (Browser, App content, Nitro route, Instance, Artifacts · cache) in two modes: `server-only` and `hybrid`. Each mode has "what if" cards.
3. **Manifest & snapshot:** a comparison table with a v3 SQL-dump column, a lifecycle timeline, and rule cards.
4. **Live mode in #42:** the PR's state, v4 vs #42 formulas, a live read step-through, the stamped-snapshot build timeline, `withRef()` cards, what #42 adds, what it changes in existing v4 blocks (built from each brick's `br.pr42`), and limits.
5. **v3 · v4 · comark-content:** a 12-row comparison table and a "who owns what" table.

The legend box sits above the tabs and has three rows:

- **Path:** solid is v4, dashed grey is v3 compat (to be deleted), dashed darker grey is #42 live mode (draft).
- **Owner:** comark-content, Nuxt Content, v3 compat, #42 live mode (see section 4).
- **Runs in:** Config, Build, Nitro, Browser or Dev.

The **inspector** is a sticky panel on the right; on screens 980px wide or less it becomes a bottom sheet. For a brick it shows the owner, where it runs, a #42 badge when relevant, what it does, notes ("v3 compat", "From comark-content", "In v3"), usage, sources and a zoom link. For a timeline moment it shows the owner and runs-in badges (from the row, the `MOV` override map, or its linked brick), the lane, the explanation and sources.

## 4. Colour meaning (asked for by Baptiste)

- **Nuxt Content** (the module) is green.
- **comark-content** (the engine) is yellow.
- **v3 compat** is red, because it is to be deleted.
- **#42 live mode** is blue, on dashed darker-grey bricks. Amber was avoided so it wouldn't clash with the comark-content yellow.
- Badges with no owner meaning, such as inspector labels, are neutral grey.

## 5. How to edit

Everything is in one IIFE at the end of `index.html`.

- **Links:** `L(rev, path, lines)` builds GitHub links. The shortcuts are `v()` for v4, `p42()`, `cc()` for comark-content, `cd()` for comark-docs and `v3()`. `src/` is prepended automatically for content-v4-temp paths. To move to a new revision, change the SHA in `REV`, then check the line numbers.
- **Bricks:** each brick is an entry in `B` with these fields: `name`, `api`, `own` (`cc` / `nc` / `v3`), `rt`, `on` (`['pr42']` marks a #42-only brick), `what`, `src[]`, `v3`, `cc`, `br.pr42` (what #42 changes, listed in the Live mode tab), `note`, `open` and `zoom`. To add a brick, add an entry to `B` and an empty `<button class="brick" data-id="…">` in the markup.
- **Timelines:** `makeTimeline(mount, { lanes, rows })`. Each row is `{ lane, t, s, x, src, brick, v3, isNew, warn }`. The read timeline's rows are in `READ.so` / `READ.hy`; the live read is `READ.live`, shown in the Live mode tab. The lifecycle rows are built in `renderArtifacts()`, the #42 build rows in `renderLive()`.
- **Tables:** built in `renderArtifacts()`, `renderLive()` and `renderCompare()`, once on load.
- **Usage:** the `U` map (merged into `B` as `use`) holds `{ intro, blocks: [{ t, code }], after, src }` per brick, rendered in the inspector under "Usage". Examples come from `migration.md` and `examples/hybrid-v4-pure` on `v4`, and from #42's `migration.md` for live surfaces.
- **Tooltips:** `TIPS`, shown on any element with `data-tip`.
- **Fonts:** Bricolage Grotesque, IBM Plex Sans and IBM Plex Mono when they are installed, otherwise system fonts. Unlike eve-anatomy, Google Fonts are not loaded.

## 6. Open points

- PR states change fast. Re-check #42 and open PR #61 (layer lookup for `server/content.ts`) before relying on the page.
- The comark-content details come from the write-up's v0.6.2 links, because the repo is private.
