---
title: Obsidian Theme – CLAUDE Project Instructions
created: 2026-04-29
updated: 2026-06-01
tags: [project, obsidian, theme, css]
---
# OBSIDIAN Theme Project

Project-local rules for working on the One Oracle Developer Obsidian theme. These rules are only relevant when editing files in this project (`~/Dropbox/PROJECTS/OBSIDIAN/`) or theme/snippet files under the brain's `.obsidian/themes/One Oracle Developer/` and `.obsidian/snippets/ood-*.css`.

The brain's `~/Dropbox/BRAIN/CLAUDE.md` rules still apply when working in the brain itself; this file extends them with theme-specific guidance.


## Obsidian theme CSS – inspect DOM before writing selectors

When authoring or fixing CSS for an Obsidian theme, **always verify the actual class/attribute names via DevTools before writing selectors**. Don't guess based on training-data assumptions – Obsidian's CM6 live preview uses non-obvious class names that differ from what you might expect.

**Why:** Lost a whole round-trip chasing `.cm-strikethrough` which does not exist in the DOM. The real selectors for completed tasks (where Obsidian applies strikethrough) are:

- Live preview: `<div data-task="x" class="HyperMD-task-line">`
- Reading view: `<li class="task-list-item is-checked" data-task="x">`
- Raw markdown `~~text~~`: `<del>` / `<s>`

**How to apply:** Open Obsidian DevTools via `Cmd+Opt+I` (the computer-use MCP can drive this). Use the Console to run `Array.from(document.querySelectorAll('*')).filter(e=>getComputedStyle(e).textDecorationLine==='line-through')[0].outerHTML` – that returns the actual struck element with its real class list and attributes. Write the CSS selector to match that, not what you assume is there.


## Obsidian theme CSS – Baseline "accented-interface" gotcha

Baseline theme has this rule: `body:not(.accented-interface) { --interactive-accent: var(--color-base-100) }` – silently overrides `--interactive-accent` to a near-black color on light themes unless the user toggles `.accented-interface` in Style Settings.

**Why:** Spent multiple turns debugging why tags, H1 color, and blockquote accent bar were rendering black instead of the user's picked accent. Root cause was this hidden rule.

**How to apply:** When layering custom CSS on Baseline (or any forked theme), force-set `--interactive-accent` at the top of the override block with `!important` and derive from accent HSL vars:

```css
body {
    --interactive-accent: hsl(var(--accent-h), var(--accent-s), var(--accent-l)) !important;
    --interactive-accent-hover: hsl(var(--accent-h), var(--accent-s), calc(var(--accent-l) * 0.9)) !important;
}
```


## Obsidian theme CSS – list-bullet widget replacement

Obsidian live preview draws unordered bullets via a `<span class="list-bullet">` widget whose glyph is its OWN TEXT CONTENT – not a `::before` or `::after`. Adding a pseudo-element stacks a second character next to the bullet instead of replacing it.

**Why:** Spent two turns trying `::after { content: "—" }` approaches that produced a bullet AND an em-dash at offset positions.

**How to apply:** To replace the bullet glyph, collapse the widget text with `font-size: 0; color: transparent`, then inject the replacement via `::before` inline (no absolute positioning – that mispositions against the text baseline):

```css
.list-bullet {
    font-size: 0 !important;
    color: transparent !important;
}
.list-bullet::before {
    content: "—" !important;
    color: #333 !important;
    font-size: var(--font-text-size, 1rem) !important;
    display: inline !important;
    vertical-align: baseline !important;
}
.list-bullet::after { content: "" !important; display: none !important; }
```

Reading view uses a real `<ul><li>`, so `list-style-type: "— " !important` + `::marker { content: "— " !important; color: #333 !important }` handles that path.


## Obsidian theme CSS – font switch via snippets

For fast font experimentation without editing the theme, drive font via CSS variables and switch via CSS snippets toggled in Settings → Appearance:

- Theme sets defaults: `--ood-font-heading: "Source Serif Pro", ...; --ood-font-body: "Open Sans", ...;`
- Each candidate font lives in `.obsidian/snippets/ood-fonts-<name>.css` with its own `@import` + `body { --ood-font-heading: ...; --ood-font-body: ...; }`
- Toggle one snippet ON in Settings → Appearance → CSS snippets – instant swap, no theme edit, no reload.

**How to apply:** When the user wants to test fonts, create snippet files in `.obsidian/snippets/` (naming pattern `ood-fonts-<variant>.css`), each overriding only the relevant CSS variables. Never hardcode the font-family in the theme file – always reference the variables.


## Log theme/snippet changes to CHANGELOG

Every time the Obsidian theme or any companion snippet changes (anything under `.obsidian/themes/One Oracle Developer/` or `.obsidian/snippets/ood-*.css`), append an entry to `.obsidian/themes/One Oracle Developer/CHANGELOG.md` under the `## Unreleased` heading in the same commit. Don't ask – do it as part of the change.

**Why:** Jan tracks these for the next theme release. Without an inline changelog entry, the diff is lost in commit history and the release notes have to be reconstructed from scratch.

**How to apply:** Before staging the theme/snippet change, edit the CHANGELOG: add a new bullet under `## Unreleased` describing what changed and why. Keep entries terse (one bullet per logical change). When a release is cut, the `## Unreleased` block gets renamed to a versioned heading. If a `## Unreleased` section doesn't exist yet, create it at the top of the changelog.


## Obsidian theme CSS – check display:none wrappers before changing display

Before changing any `display:` property on table headers (or any element that "should be visible but isn't"), inspect the DOM for a parent/sibling wrapper with `display: none`. Obsidian frequently double-renders content – the visible copy is the styled one, the `display:none` copy is the source. Flipping that wrapper to `inline-block` or `flex` un-hides duplicate text.

**Why:** Lost a round-trip on a table-header fix where an `inline-block` override unhid Obsidian's hidden source span and produced duplicate header text. The fix needed a higher-specificity selector on the *visible* element only, not a wrapper-level display change.

**How to apply:** When a CSS change "should just work" but renders weirdly, run `Array.from(document.querySelectorAll('SELECTOR')).map(e => [e.outerHTML.slice(0,120), getComputedStyle(e).display])` in DevTools before retrying. If multiple matches show one with `display: none`, target the visible one specifically and leave the hidden wrapper alone.


## Obsidian theme CSS – inspect winning rule first, beat it by specificity (no blind !important)

Before writing any CSS fix, inspect the live element in DevTools (driven via the Chrome MCP attached to Obsidian's Chromium DevTools, or the computer-use MCP if Chrome MCP can't reach it) and identify the **full selector chain that's currently winning** for the property you want to change. Report that chain back, then write a single targeted override that beats it by specificity – not by stacking `!important` everywhere.

`!important` is allowed only when the winning rule is itself `!important` (e.g. theme-injected vars like `--interactive-accent` on Baseline) or when no specificity-only fix is possible. "Blind" `!important` – sprinkled because the change didn't take on the first try, without first checking which rule is winning – is the failure mode.

**Why:** 2026-05-02 insight set. Multiple CSS round-trips in the past went: edit → reload → still wrong → add `!important` → reload → side effect. The skipped step is "look at the cascade first". Specificity-aware fixes are stable; `!important` chains break each other and are hard to unwind.

**How to apply:**
- Step 1: open DevTools on the target element (`Cmd+Opt+I` in Obsidian; the Chrome MCP can drive this once attached to the Obsidian window). Inspect the **Computed** panel for the property in question – DevTools shows which rule won and which were overridden.
- Step 2: report the winning selector chain back to Jan in the chat ("currently winning: `body.theme-light .markdown-source-view.mod-cm6 .HyperMD-list-line .list-bullet { ... }`"). This is the diagnostic step the user wants to see before any edit.
- Step 3: write **one** override selector that beats the winning rule by specificity (more specific class chain, parent context, attribute selector, etc.). Test against known parent selectors (`body.theme-light`, `body.theme-dark`, `.markdown-source-view.mod-cm6`, `.markdown-preview-view`) so the fix doesn't break the other view mode.
- Step 4: only if step 3 genuinely can't beat the existing rule (e.g. the winning rule is already `!important`), use `!important` – and say so explicitly in the commit/changelog entry, with the reason.
- Combine with the incremental-iteration rule below: one specificity fix per edit, reload, confirm.


## Obsidian theme CSS – iterate incrementally, confirm visually before squash

Don't bundle a multi-step CSS fix into one large edit and ask the user to "verify the whole thing". Each selector / pixel value change is one round-trip: edit, ask user to reload Obsidian, confirm result, then move on. Only squash commits **after** Jan has confirmed the visual result – not before.

**Why:** Claude can't see the rendered DOM. Multiple-iteration CSS work (blockquote indent, table headers, sidebar layout) consistently needed 2-4 correction cycles. Bundling them produces a "big bang" change that's hard to bisect when it overshoots.

**How to apply:**

- One conceptual change per edit (e.g. "fix blockquote indent" – not "fix blockquote indent + table header + sidebar").
- After each edit, ask Jan to reload and report back ("does the indent look right now?") before moving to the next item.
- Only squash the iteration commits into a clean changelog entry once Jan has signed off on the final visual.
