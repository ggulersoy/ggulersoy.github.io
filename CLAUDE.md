# CLAUDE.md — Site Knowledge Base
## gurcangulersoy.com

---

## Stack & Deployment

- **Static site generator**: Hugo (v0.143.1 — pinned in `hugoblox.yaml` and the publish workflow `WC_HUGO_VERSION`, matching the local install)
- **Theme**: [Hugo Blox](https://hugoblox.com) (`blox-tailwind` module), vendored into `_vendor/`
- **CSS**: Tailwind CSS, but processed through Hugo's asset pipeline using the vendor's `tailwind.config.js`
- **Deployment**: **GitHub Pages** via GitHub Actions (`.github/workflows/publish.yaml`) — builds with Hugo + Pagefind and deploys on every push to `main`. (Netlify is NOT used; confirmed via the repo's deployment environments, commit statuses, and webhooks. The old `netlify.toml` was removed, and the inert `blox-plugin-netlify` Hugo module has now been dropped entirely — from `module.yaml`, `go.mod`/`go.sum`, and `_vendor/`.)
- **Repo**: `ggulersoy/ggulersoy.github.io` on GitHub, branch `main`
- **Live URL**: https://www.gurcangulersoy.com (apex + www both served by GitHub Pages)

### Key build commands
```bash
hugo --minify                        # build site into public/
hugo --minify --cleanDestinationDir  # clean build (removes stale files)
hugo server --port 1314              # local dev server (port 1313 may be in use)
git add <files> && git commit && git push origin main  # deploy
```

> **Do NOT commit the `public/` or `resources/` directories** — GitHub Actions rebuilds them on deploy. Both are now in `.gitignore`. Only commit source files.

---

## Project Structure

```
/
├── config/_default/          # Hugo config (hugo.yaml, params.yaml, menus.yaml, module.yaml)
├── content/
│   ├── authors/admin/_index.md   # ← PRIMARY FILE: all CV data (work, education, skills, languages)
│   ├── experience.md             # CV page layout config (section blocks)
│   ├── _index.md                 # Homepage
│   ├── publication/              # Journal articles
│   ├── working-paper/            # Working papers
│   ├── contribution/             # Contributions to others' work
│   └── event/                    # Talks/events
├── layouts/
│   └── partials/blox/
│       ├── resume-experience.html    # ← CUSTOM OVERRIDE: experience+education timeline
│       ├── resume-biography-3.html   # Custom biography block
│       └── collection.html           # Custom collection block
├── static/
│   ├── uploads/resume.pdf            # CV PDF download
│   └── media/icons/companies/        # ← Company/institution logos (PNG/SVG)
│       ├── oecd.png
│       ├── insead.png
│       ├── scpo.png          # Sciences Po
│       ├── kcl.png           # King's College London
│       └── bbva.png          # BBVA Research (extracted from favicon)
├── assets/
│   └── media/
│       ├── icons/            # Custom SVG icons (custom/, brands/, devicon/, academicons/)
│       └── companies/        # ← DO NOT USE: logos here are NOT served; use static/media/icons/companies/ instead
├── _vendor/                  # Hugo module dependencies (do not edit)
│   └── github.com/HugoBlox/hugo-blox-builder/
│       └── modules/blox-tailwind/
│           ├── tailwind.config.js    # Tailwind content scan config
│           └── layouts/partials/blox/resume-experience.html  # original (overridden)
├── hugoblox.yaml             # Hugo version pin
├── hugo_stats.json           # Auto-generated Tailwind class list (commit this)
└── .claude/launch.json       # Preview server config (port 1314)
```

---

## The Main Content File

**`content/authors/admin/_index.md`** controls nearly everything on the CV page:

- `work:` — experience timeline entries. Fields: `position`, `company_name`, `company_url`, `company_logo`, `date_start`, `date_end`, `summary` (supports HTML)
- `education:` — education timeline entries. Fields: `area`, `institution`, `institution_logo`, `date_start`, `date_end`, `summary`
- `skills:` — skills section with icons
- `languages:` — language proficiency
- `profiles:` — social links (email, X, LinkedIn, Google Scholar)
- `interests:`, `organizations:` — bio section data

---

## Company/Institution Logos

Logos live in **`static/media/icons/companies/`** and are served at `/media/icons/companies/`.

| File | Organisation |
|------|-------------|
| `oecd.png` | OECD |
| `insead.png` | INSEAD |
| `scpo.png` | Sciences Po Paris |
| `kcl.png` | King's College London |
| `bbva.png` | BBVA Research / Garanti Bank |

To **add a new logo**: drop the file into `static/media/icons/companies/`, then set `company_logo: filename.png` (or `institution_logo:`) in `_index.md`.

To reference a logo in the template: `{{ printf "/media/icons/companies/%s" .company_logo | relURL }}`

---

## Custom Resume Template

**`layouts/partials/blox/resume-experience.html`** overrides the vendor default. Key design decisions:

- Icons are 3rem × 3rem (48px) circles, positioned at `inset-inline-start: -1.5rem` (centered on the timeline border)
- List items use `margin-inline-start: 3.5rem` (inline style, not Tailwind class — see Tailwind caveat below)
- Outer container uses `padding-left: 3rem` to prevent icons bleeding off-screen on mobile
- Falls back to briefcase SVG (experience) or graduation cap SVG (education) if no logo is set
- `company_logo` → work entries; `institution_logo` → education entries

---

## Critical Tailwind CSS Caveat

**New Tailwind utility classes added to custom layout overrides will NOT be included in the generated CSS.**

The vendor's `tailwind.config.js` scans only `_vendor/.../layouts/**/*.html`, not the project's own `layouts/`. Therefore:

- **Always use inline `style="..."` attributes for any sizing, spacing, or positioning values** that differ from what was already in the vendor template
- Safe to use as Tailwind classes (already in CSS): `ms-6`, `w-6`, `h-6`, `-start-3`, `px-6`, `sm:px-0`, `ring-8`, `rounded-full`, `overflow-hidden`, `absolute`, `flex`, `items-center`, `justify-center`, `bg-white`, `dark:bg-gray-800`, `mb-10`, and all text/typography classes
- **Do NOT use new Tailwind classes** like `ms-14`, `w-12`, `-start-6`, `px-10` etc. — they won't render

---

## Navigation / Pages

Defined in `config/_default/menus.yaml`. Current nav: Publications, Talks, CV.

Pages are defined by `content/*.md` files with `type: landing` and `sections:` blocks referencing Hugo Blox block names.

---

## Skills Icons

Custom SVG icons are in `assets/media/icons/custom/`. Referenced in `_index.md` as `icon: custom/icon-name` (without `.svg`). Also supports `devicon/`, `brands/`, `academicons/` prefixes.

---

## Publication Types

- `publication/` — journal articles (type: `article-journal`)
- `working-paper/` — working papers (custom section)
- `contribution/` — contributions to others' papers

Each entry is a subfolder with an `index.md`. Featured images go in the same folder.

---

## Color Palette & Hero (warm brown/red theme)

The site uses a **warm brownish-red palette** (matching the avatar photo's backdrop), replacing the original teal/green. Reference palette: Black Bean `#461910`, Liver `#6F281A`, Chinese Red `#993623`, Dark Coral `#D6634C`, plus warm amber/mustard accents.

**Accent/theme color**: `appearance.color: red` in `params.yaml` (switched from `emerald`). This drives links, buttons, navbar, and focus rings site-wide via the vendored theme `assets/css/themes/red.css`; the `red` preset's dark shades (red-800 `#991B1B`, red-950) closely match the palette. Valid presets live in `_vendor/.../blox-tailwind/assets/css/themes/`; a fully custom theme would require adding `assets/css/themes/<name>.css`.

**Hero background** is the dotted world map SVG at `assets/media/world-map-lived.svg`. It has three meaningful `rgba()` fills (edit with `sed`):
- Ocean (background rect): `rgba(153, 54, 35, 1)` — Chinese Red `#993623`
- Land/continent dots: `rgba(115, 41, 26, 1)` — subtly darker than ocean (texture, must not compete with text)
- Visited-country highlights: `rgba(214, 99, 76, 1)` — Dark Coral
- (also contains `#ffffff` and `none` fills — leave those alone)

The hero block's background color is set separately in `content/_index.md` under the `resume-biography-3` block's `design.background.color` — keep it in sync with the ocean color (`#993623`).

**Design rule learned**: ocean must be *lighter* than the land dots (lighter water, darker continents) — mirrors the original teal version and keeps the page feeling light. Keep land-dot contrast subtle or it competes with body text.

## Avatar

- **`content/authors/admin/avatar.png`** — a **pre-cropped square PNG**. The Hugo template does `$avatar.Fill "300x300 Center"`, which just 1:1 resizes a square source, so all framing is baked into the PNG itself.
- Current crop: a square box from the source headshot, framed with full hair at top and chin, neck, and shoulders visible (head about 70% of the circle, LinkedIn style). Keep the high-res source headshot somewhere outside the repo for re-cropping.
- **When re-cropping, ALWAYS view the result with the Read tool before committing** — pixel-value scans are unreliable (a dark beard reads as "chin" too early). Clear `resources/_gen/images/` after replacing the file.
- Avatar ring: set in `layouts/partials/blox/resume-biography-3.html` as inline `style="background-color: #2C0E07;"` (dark warm brown — not pure black, which is too harsh).

## Custom CSS (`assets/css/custom.css`)

Natively loaded by the vendor `site_head.html` if it exists (no config needed). Use this for global style overrides instead of inline styles where possible.

- **Bio body text** is recolored to warm near-white `#FFE8DF` via `.blox-resume-biography-3 .prose p, li, strong, td`. **Scoped to the bio block only** — a global `.dark .prose` rule wrongly recolored "My Research" and other dark sections.
- **Tailwind gotcha**: `dark:prose-invert` compiles to `.dark .dark\:prose-invert`, NOT `.prose-invert`. The `.dark` class lives on the *section container*, not `<html>` (except in dark theme mode, where JS also adds it to `<html>`). Target `.<block-class> .prose <tag>` directly.
- **Inline prose links use the warm accent.** Links *inside `.prose`* (CV summaries, "My Research", the CV download button) default to blue `#2563EB`, which clashed with the red theme. They are recoloured via Tailwind's link variable: `.prose { --tw-prose-links: #DC2626 }` (light) and `.dark .prose { --tw-prose-links: #F87171 }` (dark). Citation `PDF/CITE/DOI` buttons already use the accent on their own (they're not `.prose`).
- **Justified body text is intentional.** `.bio-text`, `.blox-markdown .prose` ("My Research"), and event abstracts are `text-align: justify` **by user preference** — do NOT "fix" this to left-align.
- **Serif display font (Source Serif 4).** The "DESIGN POLISH" block at the bottom of `custom.css` self-hosts Source Serif 4 (variable woff2, two subsets — `latin` + `latin-ext` for Turkish — in `static/fonts/`, served at `/fonts/`) via `@font-face`, exposed as `--font-serif`. It is applied to the **display/identity type**: the hero name (`.hero-name` / `.portrait-title .text-3xl`), all section titles, the CV page title, the **navbar wordmark + nav links** (`header .order-0`, `header .nav-link` — a journal-masthead look), and bio content headings (`.bio-text h1/h2/h3`, e.g. "About Me"). Body/UI text stays Inter (the theme's own self-hosted font, loaded separately in `site_head.html`). There are **no external font requests** — keep it that way. To swap the serif, replace the two woff2 files and the `@font-face` `unicode-range`s.
- **Section-title accent rule + heading hooks.** Section headings carry a short accent bar (`::after`, theme red `--accent` / dark-coral `--accent-dark`). Two hook classes drive this, added in the overridden partials: `.section-title--centered` (homepage/collection + the vendor `.blox-markdown` title, centered) and `.section-title--left` (CV timeline `Experience`/`Education`, left-aligned). `collection.html`'s title was also promoted from `<div>` to a semantic `<h2>` (a11y).
- **Card hover, button, social-icon, focus styling.** Also in the DESIGN POLISH block: a soft shadow/lift on `.blox-collection .group` cards on hover; the archive "See all" button restyled to a warm accent outline with hover fill; hero `.network-icon` social icons get a hover lift/colour; and a global `:focus-visible` accent outline for keyboard a11y. Hover transforms are disabled under `prefers-reduced-motion`.
- **Block gutters.** Several vendor blocks ship with no horizontal padding, so on narrow viewports their content touches the screen edge. Custom rules add a gutter: `.blox-markdown .max-w-prose.mx-auto` and `.blox-resume-skills .max-w-prose` get `0.75rem` (matching the bio's `px-3`); `.blox-collection .container.max-w-3xl` (the **citation** view on the Publications page) gets `2rem` to match the `px-8` the article-grid views already have. The `max-w-3xl` selector scopes the citation fix away from the card grids (`max-w-screen-lg`).

## CV Page Header (`content/experience.md`)

The CV page leads with a `markdown` block (not `cta-button-list`): centered "Curriculum Vitae" title, a subtitle `<p>` ("PhD candidate in Economics · King's College London" — note lowercase "candidate", kept consistent with the homepage role), and an **outlined** (not solid) Download PDF button — all wrapped in `<div style="text-align:center;">`. Requires goldmark unsafe renderer (`markup.goldmark.renderer.unsafe: true` in `hugo.yaml`) for raw HTML in markdown blocks. The button no longer hardcodes a colour: it uses `border:1.5px solid currentColor` and inherits the warm prose-link accent (see Custom CSS), so it adapts to light/dark.

---

## Notes for Common Tasks

**Add a new job entry**: edit `content/authors/admin/_index.md`, add entry under `work:`, add logo to `static/media/icons/companies/` if needed, run `hugo --minify`, commit & push.

**Add a new publication**: create `content/publication/slug/index.md` following existing examples. Use `hugo --minify` to rebuild.

**Change navigation**: edit `config/_default/menus.yaml`.

**Change CV page layout/sections**: edit `content/experience.md`.

**Download CV link**: points to `static/uploads/resume.pdf` — replace that file to update it.

**Dark mode**: fully supported by the theme. Custom template already handles `dark:` variants.

---

## Gotchas

**Markdown links don't render inside `markdown` blocks.** In a `block: markdown` section (e.g. "My Research" on the homepage), `[text](url)` silently renders to *nothing*: the text and href both vanish. This is a Hugo Blox quirk (the vendor's own render-link hook drops it too; affects every markdown link in these blocks, not the project's `render-link.html` override). **Workaround:** use a raw HTML `<a href="…" target="_blank" rel="noopener">text</a>` (goldmark `unsafe: true` is enabled). Links in other contexts (bio, page content) render normally.

**Downloadable CV** (`static/uploads/resume.pdf`) is built from a separate LaTeX project and copied in. Its publication list must stay in sync with `content/contribution/`, `content/publication/`, and `content/working-paper/`.

**⚠️ `hugo mod vendor` will silently upgrade the theme.** `go.mod` pins `blox-tailwind` at a *Sept 2024* pseudo-version, but the committed `_vendor/` copy is the *June 2024* one — they have been mismatched since a `hugo mod get` on 2024-09-08 that was never followed by a re-vendor. The build always uses `_vendor/`, so the site runs on June 2024. Running `hugo mod vendor` re-fetches per `go.mod` and overwrites the whole vendored theme (a real upgrade). Do NOT run it casually. To remove a single module cleanly *without* bumping the theme, edit `module.yaml`/`go.mod`/`go.sum`/`_vendor/modules.txt` by hand and delete that module's vendored dir (that's how `blox-plugin-netlify` was removed). A deliberate theme upgrade is a separate, test-heavy task because of the custom overrides.

**Reading-time ("N min read") is disabled site-wide** via `cascade:` → `reading_time: false` in `config/_default/hugo.yaml`. The theme's `_default/single.html` only shows it when `.Params.reading_time != false`; the config-level cascade sets that on every page (no layout override needed). Re-enable by removing the cascade.

**Social profile icon labels**: `layouts/partials/blox/resume-biography-3.html` maps icon names to human-readable `aria-label`s via an `$icon_labels` dict (e.g. `brands/x` → "X"), falling back to a profile's explicit `label`. Add new socials to that dict so screen readers don't announce the raw icon name.

**No bibtex auto-import.** `publications.bib` is reference-only (and has had its local Zotero `file =` paths stripped). The old `import-publications.yml` workflow that auto-imported it into `content/publication/` was removed because it mis-filed entries across the hand-sorted `publication/`, `working-paper/`, and `contribution/` folders. Maintain publication pages by hand.

---

## Recent changes (2026-06-15 session)

A UX + structure pass. Highlights (details documented in the sections above):

**UX / design**
- Inline `.prose` links and the CV Download PDF button recoloured blue → warm accent (light/dark).
- Body text kept **justified** (user preference; do not left-align).
- Added horizontal gutters so content stops touching the screen edge: CV **skills** block (`0.75rem`) and the **citation** view on the Publications page (`2rem`).
- Social profile icons given human-readable `aria-label`s.
- Reading-time ("N min read") turned off site-wide via a `reading_time: false` cascade.

**Content** (only explicitly-approved edits)
- Fixed a stray "and" typo in the Research Assistant entry.
- Unified role capitalisation to "PhD candidate" (homepage + CV subtitle).
- Still open for a future discussion (not yet done): de-duplication of research/sailing text repeated across bio/skills/"My Research"; broadening the Turkey-only research framing to include the Uganda work; and regrouping pre-doctoral student writing out of "Authored Publications".

**Structure / CI / cleanup**
- Bumped pinned Hugo `0.126.3` → `0.143.1` (pin + workflow).
- Removed two workflows: `import-publications.yml` (mis-filing footgun) and `updater-wip.yml` (dead, owner-gated to HugoBlox).
- Dropped the inert `blox-plugin-netlify` module (GitHub Pages, not Netlify) — surgically, theme untouched.
- Stripped local Zotero paths from `publications.bib`; pointed the `uganda_vat` redirect at the `www` host; added a `README.md`.
- Discovered (not changed): the `go.mod` vs `_vendor/` theme version mismatch — see the `hugo mod vendor` gotcha above.

---

## Recent changes (2026-06-20 session)

A design-polish pass to shed the "default HugoBlox template" feel — restrained, editorial, academic. No content/copy changes; palette and theme untouched.

- **Typography:** introduced a self-hosted **serif display font (Source Serif 4)** for the hero name, all section titles, and the CV page title; body stays Inter. Fonts live in `static/fonts/` (two woff2 subsets); no external requests. See the Custom CSS notes above.
- **Section rhythm:** every section heading now has a short accent rule (centered on the homepage, left-aligned on the CV), via `.section-title--centered` / `.section-title--left` hooks.
- **Components:** subtle card hover lift/shadow (image cards), warm accent-outline archive button, social-icon hover feedback, and a keyboard `:focus-visible` outline; all motion respects `prefers-reduced-motion`.
- **Accessibility:** collection section title promoted from `<div>` to semantic `<h2>`; added focus-visible styles.
- **Files:** all visual work is in `assets/css/custom.css` (a "DESIGN POLISH" block at the end). Layout hooks only in the three overridden `layouts/partials/blox/*.html` partials (biography name, collection `<h2>`, experience headings). New assets: `static/fonts/source-serif-4-{latin,latin-ext}.woff2`.
