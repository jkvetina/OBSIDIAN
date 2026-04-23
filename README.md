# One Oracle Developer

An Obsidian theme tuned for long-form technical writing, tight note taxonomies, and daily use by a developer who lives in the editor. Built on top of the excellent [Baseline](https://github.com/aaaaalexis/obsidian-baseline) theme, with opinionated typography and a few quality-of-life fixes on top.

Now a human version from me:

> I checked all themes in Obsidian, and 95% of them were total garbage. Baseline was almost perfect, but I had to make a few small tweaks here and there so it works better with lists, checklists, and tags. I also adjusted the headers and fonts. The biggest issue was the folders/files tree, which felt a bit dull. Install Baseline and this theme and compare them yourself.
>
> More details (generated) below. Enjoy!

<br>

## Preview

![Screenshot](./screenshot-full.png)

## What's different from Baseline

- **Serif headings with a fixed scale.** H1–H6 use Source Serif Pro at 3 / 2 / 1.75 / 1.5 / 1.25 / 1 rem, weight 500, line-height 1.35. Headings read like a book, not a UI component.
- **Hidden frontmatter.** The properties block is hidden in both Live Preview and Reading view – notes open straight to content, properties stay editable via the command palette and right sidebar.
- **Em-dash list bullets.** Unordered list items render with `—` instead of `•`, in both Live Preview and Reading view.
- **Readable strikethrough.** Completed tasks, `<del>`, and `<s>` render in grey (#999), and struck bold text is forced back to weight 400 so it never competes with the strike line.
- **Accent tag pills, visible when struck.** Tags render as solid accent-coloured pills with white text. Inside completed tasks they drop the pill but keep a grey foreground so `#project` stays legible.
- **Light-theme heading colors.** H1 inherits the user's accent colour; H2–H3 render in `#333`, H4–H6 in `#555`. Dark theme keeps Baseline defaults.
- **Enhanced file explorer.** Dotted indentation guides, accent-coloured guide + semibold folder titles for the branch containing the active file, and a `⟵` arrow on the active file (no border, no dot).
- **Small chrome polish.** Tighter tab bar with an accent underline on the active tab, accent-on-hover scrollbars, calendar month in accent.
- **Everything else from Baseline.** Layout flavours, input styles, callouts, tables, colour schemes, Style Settings integration, light/dark support – untouched.

## Install

### Manual

1. In your vault, create the folder `.obsidian/themes/One Oracle Developer/`.
2. Download `theme.css` and `manifest.json` from this repo into that folder.
3. In Obsidian: `Settings → Appearance → Themes → One Oracle Developer`.

### Community themes

Once listed: `Settings → Appearance → Manage → Browse → One Oracle Developer → Install and use`.

## Requirements

- Obsidian 1.6.0 or newer.
- Works in both light and dark modes.
- [Style Settings](https://github.com/mgmeyers/obsidian-style-settings) plugin recommended (inherited from Baseline) for colour and typography tweaks.

## Fonts

Headings use **Source Serif Pro**, falling back to Georgia → Times New Roman → the system serif. Body text uses **Open Sans**, falling back to `system-ui` → `-apple-system` → Segoe UI → the system sans-serif. Both families are pulled in via a Google Fonts `@import` at the top of the override block, so no local install is required – but a local install will render slightly faster on first paint.

Swap either family by overriding the `--ood-font-heading` / `--ood-font-body` tokens in a snippet or in Style Settings' custom CSS. The heading scale, weight, and line-height stay pinned regardless of the family you pick.

## Complete list of changes

Everything below lives in a clearly fenced override block appended to Baseline's CSS, so a future Baseline update is a clean diff-merge.

### Foundation

- **Custom property tokens.** `--ood-accent-bg-weak`, `--ood-accent-bg-hover`, `--ood-accent-underline-width`, `--ood-font-heading`, `--ood-font-body` – single source of truth for tuning.
- **Forced accent on light theme.** Baseline's `body:not(.accented-interface)` rule would flatten `--interactive-accent` to black. Overridden with `hsl(var(--accent-h) ...) !important`, with a darkened hover variant via `calc(var(--accent-l) * 0.9)`.
- **Google Fonts import.** Source Serif Pro + Open Sans pulled via `@import url(...)` so the theme works without any local font install.
- **Softer blockquote background.** `--blockquote-background-color` nudged to a warmer, lower-contrast tint.

### Content area

- **First-H1 flush top.** The leading H1 of a note drops its top margin and padding so the title sits tight against the tab bar.
- **Heading scale pinned.** H1–H6 forced to 3 / 2 / 1.75 / 1.5 / 1.25 / 1 rem with `!important` so other plugins can't push them around.
- **Heading weight + line-height pinned.** `font-weight: 500`, `line-height: 1.35`. Reads like a book, not a UI component.
- **Heading typography.** `font-family: var(--ood-font-heading)` + `letter-spacing: -0.05rem` across H1–H6 in both Live Preview and Reading view.
- **Light-theme heading colour.** H1 = `var(--interactive-accent)`; H2–H3 = `#333`; H4–H6 = `#555`. Dark theme keeps Baseline defaults.
- **Body font.** `.markdown-preview-view`, `.markdown-source-view`, and `.cm-content` all use `var(--ood-font-body)` (Open Sans) so Live Preview and Reading view match.

### Lists, tasks, tags

- **Em-dash bullets.** `—  ` replaces `•` on unordered list items. Reading view uses `list-style-type` + `::marker { color: #333 }`; Live Preview hides `.list-bullet` and injects `::before { content: "—" }`.
- **Bold is 700.** `strong, b, .cm-strong` forced to `font-weight: 700 !important` so Open Sans bolds actually look bold.
- **Strikethrough #999.** `<del>`, `<s>`, `[data-task="x"]`, `.HyperMD-task-line[data-task="x"]`, `.task-list-item.is-checked`, and `li[data-task="x"]` (plus their descendants) render in grey with matching `text-decoration-color`.
- **Struck bold de-escalates.** Inside struck text, `strong/b/.cm-strong` is forced back to `font-weight: 400` so the strike line wins over the boldness.
- **Accent tag pills.** Tags render as solid accent-coloured pills with white text, `font-family: var(--ood-font-body)`, `font-size: 0.85rem`, `font-weight: 500`, `letter-spacing: normal`, `line-height: 1` – so tags inside H1–H6 don't inherit heading serif, size, or negative tracking.
- **Struck tags stay legible.** Inside a completed task, tags drop the pill background and render as grey `#999` text so `#project` remains readable without shouting.
- **Checked checkbox colour.** On light theme, the checked checkbox glyph renders in `#666` so it doesn't fight with the surrounding grey text.

### Chrome

- **Hidden frontmatter.** `.metadata-container` is hidden with `display: none !important` in both Live Preview and Reading view. Properties remain editable via command palette and right sidebar.
- **Tab bar polish.** `.workspace-tab-header` gets `position: relative` + `padding-block: 2px`; the active tab grows a `::after` pseudo underline in `var(--interactive-accent)` at `var(--ood-accent-underline-width)` (2px).
- **Scrollbar accent on hover/active.** `::-webkit-scrollbar-thumb:hover` and `:active` flip to `var(--interactive-accent)`. Default thumb width unchanged.
- **Active-line highlight disabled.** `.cm-line.cm-active { background: transparent !important }` – kept as a hook for future accent-tint experiments, currently off.

### File explorer

- **Accent collapse icon.** The disclosure triangle picks up `var(--interactive-accent)`.
- **Dotted indentation guides.** `.tree-item-children` gets `--nav-indentation-guide-width: 2px`, `--nav-indentation-guide-color: #999`, and `border-inline-start-style: dotted`.
- **Ancestor-chain highlight.** Via `.nav-folder:has(.tree-item-self.is-active)`, every folder on the path to the active file gets its guide in `var(--interactive-accent)` and its folder title in `font-weight: 500` + accent colour.
- **Tighter nav density.** `.nav-file-title`, `.nav-folder-title` get `padding-block: 3px` and `line-height: 1.6` so deep trees stop feeling loose.
- **Active file mark.** No border, no background dot – instead a right-aligned `⟵` arrow injected via `::after` so the active file reads as a cursor mark rather than a selected row.

### Calendar plugin

- **Today bold.** `.calendar .today` renders at `font-weight: 700`.
- **Month accent.** `.calendar .title .month` picks up `var(--interactive-accent)` while `.title .year` stays inherited, so the month is the heavy visual anchor.

## Author

[Jan Kvetina](http://www.jankvetina.cz) – Oracle APEX developer, blogger at [oneoracledeveloper.com](https://www.oneoracledeveloper.com).

### Credits

This theme is a fork of [Baseline](https://github.com/aaaaalexis/obsidian-baseline) by [aaaaalexis](https://github.com/aaaaalexis). All the structural work – the colour system, the Style Settings schema, the light/dark balance – comes from there. This fork only layers personal typography choices and a few rendering fixes on top. If you like the bones of this theme, go star Baseline.

### License

MIT. Baseline is also MIT; this fork inherits the same terms.
