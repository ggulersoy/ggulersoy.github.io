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
│   ├── _default/
│   │   ├── baseof.html               # ← CUSTOM OVERRIDE: adds skip-to-content link + #main-content target (a11y); else verbatim vendor copy
│   │   └── single.html               # ← CUSTOM OVERRIDE: `data-pagefind-body` (search), `.article-title` serif h1, full-width abstract block, featured-image 1600px/webp-q85
│   └── partials/blox/
│       ├── resume-experience.html    # ← CUSTOM OVERRIDE: experience+education timeline
│       ├── resume-skills.html        # ← CUSTOM OVERRIDE: skills block (left-aligned title + tidy multi-column layout)
│       ├── resume-biography-3.html   # Custom biography block
│       ├── markdown.html             # ← CUSTOM OVERRIDE: semantic <h2> title + `title_level: 1` opt-in for h1
│       └── collection.html           # ← CUSTOM OVERRIDE: section title (+ `title_level: 1` opt-in for h1), archive "See all" link
├── static/
│   ├── uploads/resume.pdf            # CV PDF download
│   └── media/icons/companies/        # ← Company/institution logos (PNG/SVG)
│       ├── oecd.png          # padded with transparent margin so the circular frame doesn't clip it
│       ├── insead.png
│       ├── scpo.png          # Sciences Po
│       ├── kcl.png           # King's College London
│       ├── ie.png            # IE University ("ie" mark, from Wikimedia SVG)
│       ├── sj.png            # Lycée Saint-Joseph crest (full-colour, see Logos)
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
- `profiles:` — social links (email, X, LinkedIn, Google Scholar). The email is the **institutional address** `gurcan.gulersoy@kcl.ac.uk` (swapped from the old gmail for a stronger trust signal); it drives the hero mail icon, the hero "Email me" CTA (`content/_index.md`), and the footer contact row. Change it in **both** `_index.md` files if updated.
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
| `ie.png` | IE University (the "ie" mark) |
| `sj.png` | Lycée français Saint-Joseph d'Istanbul |

To **add a new logo**: drop the file into `static/media/icons/companies/`, then set `company_logo: filename.png` (or `institution_logo:`) in `_index.md`.

To reference a logo in the template: `{{ printf "/media/icons/companies/%s" .company_logo | relURL }}`

**Logos must fit a circle.** The timeline frame clips each logo to a circle (`overflow-hidden rounded-full`), so a full-bleed square logo loses its corners. Prep accordingly:
- **Solid-colour square marks** (KCL, BBVA) round cleanly — no prep needed.
- **Circular marks** (INSEAD, Sciences Po) already fit.
- **Marks whose content reaches the corners** must be **padded with transparent margin** so content sits inside the circle's safe area (~68–70% of the canvas). `oecd.png` was re-exported this way (its globe + arrows + "OECD" wordmark were being clipped). These are served straight from `static/` (no Hugo image processing), so there is **no `resources/_gen` cache to clear** — just replace the file. **View the result with the Read tool before committing.**
- **`sj.png` is a built composite** (Pillow/`rsvg-convert`): the school's hi-res *white* institutional logo provides the crisp shapes (oval, ring text, wreath, "1870" banner), recoloured to the brand **blue `#183C90` + yellow `#FFD21E`** by transferring the colour regions from the school's colour original (the two artworks register near-perfectly when each is cropped to its content bbox and scaled to the same size). A single-colour silhouette is more legible at ~68px than the muddy two-colour raster, and the crest's own blue oval gives the yellow monogram contrast in *both* light and dark mode (so it works as one static logo, no per-mode trickery). Keep the source PNGs outside the repo for re-builds.

---

## Custom Resume Template

**`layouts/partials/blox/resume-experience.html`** overrides the vendor default. Key design decisions:

- Icons are **4.25rem × 4.25rem** circles (carry the class `cv-logo-frame`), positioned at `inset-inline-start: -2.125rem` (= half the diameter, so they stay centred on the timeline border). The size was chosen so the frame **spans the three-line entry header**: its top edge aligns with the position/area line and its bottom edge with the date line (the span's `top:auto` anchors it to the line-1 top, so the height extends it down to line 3).
- List items use `margin-inline-start: 3.5rem` (inline style, not Tailwind class — see Tailwind caveat below)
- Outer container uses `padding-left: 3rem` to prevent icons bleeding off-screen on mobile
- Falls back to briefcase SVG (experience) or graduation cap SVG (education) if no logo is set (sized `1.75rem` inline to match the larger frame)
- `company_logo` → work entries; `institution_logo` → education entries
- **Dark-mode ring/fill neutralised:** the vendor frame uses cool `dark:ring-gray-900` / `dark:bg-gray-800` (navy against the warm theme). A `custom.css` rule (`.dark .cv-logo-frame`) recolours both the ring **and** the circle fill to the page background `rgb(23,24,28)`, so the icon floats cleanly over the timeline with no coloured disc (matching the fill matters: a transparent-padded logo like OECD shows the fill in its corners). Light mode keeps `ring-white`/`bg-white`.

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

## Search (Pagefind)

Site search uses **Pagefind** (`show_search: true` in `params.yaml`; index built in CI via `npx pagefind --site public`, which the local `hugo server` does **not** run — search only works on the deployed site). The vendor `#search` dropdown is the `<div id="search">` in `navbar.html`; `custom.css` gives it an opaque panel background + shadow (it shipped transparent, so results showed the page through them).

**Index scope (deliberate):** `layouts/_default/single.html` (custom override) adds `data-pagefind-body` to its `<main>`. Because at least one page carries that attribute, Pagefind indexes **only** pages that have it — i.e. single content pages (publications, working papers, contributions, talks). The homepage, CV, and all section/taxonomy **list pages are intentionally excluded**: indexing everything returned ~19 noisy hits for one query and near-duplicate "Working-Paper" (taxonomy) vs "Working-Papers" (section list) results, plus the same title pulled from the site-wide sidebar furniture on every page. The pager/last-edited furniture inside `<main>` is marked `data-pagefind-ignore`. **The homepage/CV exclusion is a known, revisitable choice** — to re-include them, add `data-pagefind-body` to the landing template's main wrapper.

---

## Skills Icons

Custom SVG icons are in `assets/media/icons/custom/`. Referenced in `_index.md` as `icon: custom/icon-name` (without `.svg`). Also supports `devicon/`, `brands/`, `academicons/` prefixes.

---

## Publication Types

- `publication/` — journal articles (type: `article-journal`)
- `working-paper/` — working papers (custom section)
- `contribution/` — contributions to others' papers

Each entry is a subfolder with an `index.md`. Featured images go in the same folder.

### Detail-page featured images (`single.html` override)

- **Resolution:** fit to **1600px** wide (vendor default was 720px, which was soft on high-DPI/retina screens — a 720px image displayed at ~700px gets upscaled). The display width is still capped by the content column (`.featured-image` is `width:100%`), so 1600 only raises resolution, not layout size. `.Fit` never upscales, so smaller sources stay as-is.
- **Encoding:** `.Process "webp q85"` (not the site-wide `imaging.quality: 100`, which produced ~1.5MB files for full-size photo covers). q85 is visually lossless for photos and fine for line-art figures. **The homepage/Talks *cards* still use the global q100** via the vendor `article-grid.html` (not overridden) — an open optimisation.
- **Abstract layout:** the abstract is rendered as a **full-width `.pub-abstract` block above** the metadata grid (was a cramped ~44ch cell beside the 200px label column, which ran very tall). "Abstract" is a semantic `<h2>`. The metadata grid keeps only the short rows (Type / Publication / Date / Location). Full column width (~65ch) is the ideal reading measure; the article column can't go wider anyway (theme reserves an empty 256px sidebar + 256px TOC, capping `<main>` content at ~672px).
- **Alt text:** set `image.alt_text` in front matter — `single.html` reads it for the detail-page `<img alt>` (the card `alt` uses the page title). Featured-image sources are described for a11y.

**Featured-image aspect / cover-render gotcha:** to rasterise a PDF cover (e.g. the OECD report), render with **`pdftoppm -r <DPI>`** (scales both dimensions together, preserving aspect). **Do NOT use `-scale-to-x` alone** — it scales only the width and leaves the height at the default resolution, **stretching the image horizontally** (this squished the Hungarian cover on a first pass). A4 at `-r 194` gives ~1604×2269 (aspect 0.707). The `gulersoy-tax-2026` thumbnail is a Stata figure PNG cropped to 16:9 (drop the multi-line notes block, pad to exactly 16:9 with white) so the homepage card's centre-crop clips nothing.

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
- **Section-title accent rule + heading hooks.** Section headings carry a short accent bar (`::after`, theme red `--accent` / dark-coral `--accent-dark`). Two hook classes drive this, added in the overridden partials: `.section-title--centered` (homepage/collection + the vendor `.blox-markdown` title, centered) and `.section-title--left` (CV timeline `Experience`/`Education`, **and the CV `Skills & Interests` title** via the `resume-skills.html` override, left-aligned). `collection.html`'s title was also promoted from `<div>` to a semantic `<h2>` (a11y); the skills title likewise became an `<h2>`.
- **Card hover, button, social-icon, focus styling.** Also in the DESIGN POLISH block: a soft shadow/lift on `.blox-collection .group` cards on hover; the archive "See all" button restyled to a warm accent outline with hover fill; hero `.network-icon` social icons get a hover lift/colour; and a global `:focus-visible` accent outline for keyboard a11y. Hover transforms are disabled under `prefers-reduced-motion`.
- **Navbar font sizes + the `.navbar-brand` pin (gotcha).** The wordmark and nav links are enlarged in the DESIGN POLISH block: nav links (`header .nav-link`) to `1.125rem` (18px) and the brand to `1.375rem` (22px). **Gotcha:** the vendored theme pins `.navbar-brand`'s own `font-size`, so sizing the logo container `header .order-0` is silently overridden — size the brand on `header .navbar-brand` directly. The navbar has **no fixed height** (it sizes to its content), so the larger links' vertical padding is trimmed to `0.625rem` top/bottom to keep the bar at its original ~72px height instead of letting it grow. Below the `lg` (1024px) breakpoint the nav links collapse into the hamburger, so the brand eases back to `1.2rem` to avoid crowding the search/theme icons. (Serif font-family is still applied via `header .order-0, header .nav-link`; only the *size* must live on `.navbar-brand`.)
- **Hero density.** The clay hero (`resume-biography-3`) shipped tall enough to push the last "About Me" paragraph below the fold on shorter laptops. Its section padding is cut from the site-wide `4rem` default via a **per-block `design.spacing.padding: ['1.25rem','0','1.75rem','0']` in `content/_index.md`** — the theme renders `design.spacing.padding` as an **inline** `padding:` style (see `parse_block_v2.html`), so a CSS class cannot override it; set it there, not in custom.css. The DESIGN POLISH block additionally trims the stacked top offsets (`#profile` padding, `.avatar-wrapper` margin-top, and the bio column's `md:mt-12`). No body text is compressed.
- **Block gutters.** Several vendor blocks ship with no horizontal padding, so on narrow viewports their content touches the screen edge. Custom rules add a gutter: `.blox-markdown .max-w-prose.mx-auto` and `.blox-resume-skills .max-w-prose` get `0.75rem` (matching the bio's `px-3`); `.blox-collection .container.max-w-3xl` (the **citation** view on the Publications page) gets `2rem` to match the `px-8` the article-grid views already have. The `max-w-3xl` selector scopes the citation fix away from the card grids (`max-w-screen-lg`).
- **Skills multi-column layout.** The `resume-skills.html` override adds hook classes (`cv-skills-grid`, `cv-skills-col`, `cv-skills-coltitle`); `custom.css` then: **widens** the block past `max-w-prose` to `56rem` (both header and grid share `max-w-prose`, so this keeps the title's left edge aligned with the first column) so the four columns aren't crammed (~150px each); switches the grid from the vendor's `items-center` to **`align-items: flex-start`** so the unequal columns' **tops** line up; and, at **`lg`+ only**, centres the column titles with a reserved `min-height: 3.5rem` so a one-line title ("Languages") occupies the same space as the wrapping ones and every column's first item starts at the same height. Below `lg` the columns stack and titles left-align (centring a title over a left-aligned list looked disconnected).
- **Search dropdown (Pagefind).** The vendor `#search` panel shipped with only `p-3` and no background, so opened results showed the page through them. `#search:not(.hidden)` gets an opaque background (white / dark `#152028`), a soft shadow, and a `max-height` + `overflow-y:auto` scroll cap. (Search indexing scope is documented in the **Search (Pagefind)** section above.)
- **AUDIT FIXES block (2026-07-20), at the very end of `custom.css`.** Four rule groups from the accessibility/SEO pass: (1) `.cv-subtitle` — CV-header subtitle colour with a WCAG-AA dark variant (replaces the old inline `#6b7280`); (2) `.article-title` — the serif display font on publication/talk detail-page `<h1>`s (hook class added in `single.html`), so content pages match the site heading system; (3) `.skip-link` / `#main-content:focus` — the skip-to-content link (added in the `baseof.html` override) is offscreen until keyboard focus, then a small accent pill above the sticky navbar (z-50 > navbar z-30), and the `#main-content` target never shows a focus ring; (4) `.footer-links` / `.footer-nav` / `.footer-contact` — the footer nav + contact rows (markup in `site_footer.html`), serif nav row echoing the masthead, accent on hover.
- **Layout-polish rules (2026-07-23).** (1) `.collection-archive` — trims the vendor `mt-10` (2.5rem) above the homepage "See all" archive links to `1rem` so the button isn't detached from its card grid (hook class added in the `collection.html` override). (2) `.pub-abstract` — justifies the full-width abstract block (see Featured images above); the short-metadata grid selector keeps its own `text-align: justify`. (3) **Mobile hero gap** — a `@media (max-width: 767px)` rule cuts the `.resume-biography` container `gap` (3rem→1.5rem) and the bio's first-heading top margin, halving the empty clay gap between the social icons and "About Me" (the prose first-child reset doesn't reach that heading, since it's nested in `.bio-text`). Desktop untouched.

### Landing-page h1s (`title_level`)

Landing pages (`type: landing`) render only Hugo Blox blocks and historically had **no `<h1>`** (an a11y + SEO gap; block titles were `<h2>`s). The **`markdown.html` and `collection.html` overrides** now accept **`content.title_level: 1`** on a block, which renders that block's title as an `<h1>` (default stays `<h2>`; classes identical in both branches so the serif + accent-bar styling is unchanged). Applied so each landing page has exactly one h1: `content/publications.md` (a title-only markdown block "Publications"), `content/talks.md` (the collection title), and `content/experience.md` (the "Curriculum Vitae" markdown title). The homepage already had its `<h1>` (the hero name).

## CV Page Header (`content/experience.md`)

The CV page leads with a `markdown` block (not `cta-button-list`): centered "Curriculum Vitae" title (rendered as the page **`<h1>`** via `title_level: 1` — see the h1 note in Custom CSS), a **two-line** subtitle `<p>` ("PhD candidate in Economics, King's College London" then `<br>` "Economic Consultant, OECD" — same font/size on both lines; the academic role leads and the OECD role reads as secondary by position alone; note lowercase "candidate", kept consistent with the homepage role), and an **outlined** (not solid) Download PDF button — all wrapped in `<div style="text-align:center;">`. The subtitle colour lives in `custom.css` as `.cv-subtitle` (light `#4B5563` / dark `#9CA3AF`); the previous inline `#6b7280` failed WCAG AA (~3.9:1) in dark mode. Requires goldmark unsafe renderer (`markup.goldmark.renderer.unsafe: true` in `hugo.yaml`) for raw HTML in markdown blocks. The button no longer hardcodes a colour: it uses `border:1.5px solid currentColor` and inherits the warm prose-link accent (see Custom CSS), so it adapts to light/dark.

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

**⚠️ `layouts/_default/baseof.html` is a near-verbatim copy of the vendor `baseof.html`** (with only the skip-to-content link + `#main-content` target added). Because it's a full copy, it does **not** track vendor changes: on any deliberate theme upgrade, re-copy the new vendor `baseof.html` and re-apply the two skip-link edits, or the rest of the base template silently goes stale. (Same caution applies in spirit to `markdown.html`/`collection.html`/`single.html`, but those are smaller, more clearly diverged overrides.)

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

### Follow-up tweaks (same session)

- **Navbar sizing:** enlarged the nav links (16→18px) and the brand wordmark (20→22px), holding the bar at its original height by trimming the nav-link padding. Surfaced the `.navbar-brand` font-size pin gotcha (see Custom CSS notes).
- **Hero density:** tightened the clay hero so the full "About Me" fits the initial viewport on common laptops, via `design.spacing.padding` in `content/_index.md` plus offset trims in custom.css (see Custom CSS notes). No copy changes.

---

## Recent changes (2026-06-21 session)

A CV-page pass: timeline polish, two new education entries with built logos, a search fix, and a skills-section cleanup. Details live in the sections above; this is the index.

**CV timeline & header**
- **Subtitle → two lines** (CV page): adds the OECD role under the academic one (see CV Page Header).
- **Logo frames enlarged 3rem → 4.25rem** and aligned to the three-line entry header; `inset-inline-start` → `-2.125rem`; fallback SVGs sized to match; frames carry `cv-logo-frame` (see Custom Resume Template).
- **Dark-mode logo rings/fill neutralised** to the page background `rgb(23,24,28)` (were cool navy `gray-900`/`gray-800`); light mode unchanged.

**Education content** (explicitly approved edits)
- Added **IE University** (Erasmus exchange, 2016–17, between Master's and Bachelor's) and **Lycée français Saint-Joseph d'Istanbul** (high school, 2009–14) entries.
- New logos `ie.png` (Wikimedia "ie" mark) and `sj.png` (built full-colour Saint-Joseph crest — see Company/Institution Logos for the build recipe). `oecd.png` re-exported with transparent padding so the circular frame stops clipping it.

**Search (Pagefind)** — see the dedicated Search section
- Fixed the **transparent results panel** (opaque background + shadow on `#search`).
- **Scoped the index** with `data-pagefind-body` on `single.html`'s `<main>` (new override): only single content pages are indexed, killing ~19 noisy hits and the near-duplicate "Working-Paper"/"Working-Papers" results. **Homepage & CV are intentionally excluded** — a known, revisitable decision.

**Skills & Interests section**
- Widened past `max-w-prose`, aligned column tops, and centred column titles with a reserved height so items line up (desktop). Hooks via the `resume-skills.html` override (see Custom CSS → Skills multi-column layout).

**Lessons learned / gotchas surfaced**
- **Pagefind only indexes at deploy** (CI runs `npx pagefind`); the local `hugo server` has no index, so search can't be tested locally — verify the indexed-page set in the built HTML (`grep data-pagefind-body public`) instead.
- **Node isn't on this machine's non-interactive PATH** (CI has it) — `npx pagefind` can't be run from the Bash tool here.
- A detailed crest is illegible at ~68px as a two-colour raster; a **single-colour silhouette** (or a clean flat-colour rebuild with the mark's own background for contrast) reads far better. Recolouring a one-colour logo into two colours needs the colour regions transferred from a colour reference (see `sj.png` recipe).

---

## Recent changes (2026-06-22 session)

A homepage copy pass: rewrote the two prose sections and fixed the page title. All edits were drafted line-by-line with the user and explicitly approved (no unilateral copy changes).

**Where the homepage prose actually lives (non-obvious):**
- **"About Me"** is the markdown body at the **bottom of `content/authors/admin/_index.md`** (after the front matter), surfaced by the `resume-biography-3` block. It is NOT in `content/_index.md`.
- **"My Research"** is the `block: markdown` `text:` field in **`content/_index.md`**.

**Copy rewrites**
- **Division of labour** agreed and applied: *About Me* = who he is + a plain-language through-line (non-specialist readable); *My Research* = the technical detail. This removed the prior duplication between the two (both had previously repeated "firm-to-firm", production networks, etc.).
- **About Me** rewritten and then **expanded** (user felt the first pass read too short): four paragraphs — what he studies (firm-level data, shock propagation), a motivation/credentials paragraph (academic+policy experience, OECD across several departments, INSEAD, Sciences Po degrees), and a human closing line (sailing). Sailing detail kept to one line so it doesn't repeat the Skills "Beyond Research" block.
- **My Research** rewritten: production-networks framing **de-Turkey'd** (was Turkey-only); now three thesis essays by shock *type* (tax policy / labour market / industrial policy) and a separate "ongoing work" line naming the concrete country projects — **Uganda (VAT), Turkey (mass layoffs), Georgia (environmental)**. Dropped the old "monthly firm-level input-output matrix" claim (he no longer builds it) in favour of "administrative data recording firm-to-firm transactions". Tone de-buzzworded ("unprecedented", "comprehensive", "key innovation" removed).
  - **Deliberate framing choice:** Para 2 lists shock *types*, Para 4 lists *countries*; they don't map 1:1 (the industrial-policy essay has no country, Georgia has no essay). Kept as "two different cuts" (thesis vs current work). A careful reader may notice; revisiting is optional.

**Homepage `<title>`**
- Was `"Economics PhD, King's College London"` (an earlier SEO change that dropped the name). Restored the name to the front: **`"Gürcan Zeren Gülersoy · Economics PhD, King's College London"`** (middot separator, not an em dash). Set in `content/_index.md` front matter; drives the browser tab, the Google result headline, and `og:title`.

**Cleanup**
- Removed the **dead `interests:` block** from `content/authors/admin/_index.md`. It rendered nowhere: the only template that reads `$person.interests` is the *vendor* `resume-biography-3.html`, which is **shadowed by the custom override** (the override renders `organizations` but not `interests`). The visible "Research Interests" comes solely from the `skills:` block (CV page). So it was orphaned config that merely mirrored the skills list, not a visible duplication.

**Still open (tabled by the user)**
- **Expand About Me further** is *done* this session, but the user may want another polish pass later (add texture, not research detail — don't recreate the My Research overlap).
- **Regroup pre-doctoral / student writing** out of "Authored Publications" — postponed.
- **Research-interests terminology** — revisit only when asked.

---

## UX / design backlog (from 2026-06-23 full-site review)

A thorough walkthrough of the live site (desktop + mobile, light + dark, all nav pages) surfaced the items below. Verified findings (computed styles / built HTML), not screenshot guesses; two screenshot "issues" (faint light-mode text, half-width hero) were confirmed to be preview-tool scaling artifacts, NOT real bugs. **Items 1, 2, 6 were applied 2026-06-23 (see ✅ below); the rest remain open.**

**Also done (not backlog):** tightened the CV-page header gap between the "Curriculum Vitae" title and its subtitle — `custom.css` rule scoped via `.blox-markdown:has(+ .blox-resume-experience)` so only the CV header is affected, not the homepage "My Research" markdown block (which is followed by a collection). Title `margin-bottom` 1.5→0.6rem, flex `gap` 0.75→0.5rem; accent-bar padding kept.

**High value / clear wins**
1. ✅ **DONE (2026-06-23) — Homepage `<title>` no longer doubles the name.** The theme auto-appends `| {site title}` (the name) to a page's `.Title`, which doubled it on the homepage. Fixed by moving the homepage title to the theme's **`seo.title`** front-matter path (with the `{brand}` token expanding to the site title): `seo.title: "{brand} · Economics PhD, King's College London"` in `content/_index.md`. The `seo.title` branch in `site_head.html` (line 177) uses the value verbatim with **no** suffix, and og:title reads the same computed `$title`. `title:` was set to just the bare name so `.Title` stays sensible.
2. ✅ **DONE (2026-06-23) — Dates no longer show the weekday.** `date_format` in `config/_default/params.yaml` changed `'Monday, 02 Jan 2006'` → `'2 January 2006'` (e.g. "15 February 2026"). The CV timeline uses its own per-block `'January 2006'` (in `experience.md`), so it's unaffected.
3. **Publications page vs Talks page are visually inconsistent.** The nav "Publications" → `/publications/` (`content/publications.md`) shows **three citation-view groups** (Authored Publications / Working Papers / Research Contributions); `/talks/` uses the polished **card grid** (article-grid). Inconsistency is *citation list vs cards*. Align them — cards for both, or give the citation groups more visual structure. (Tabled by the user — revisit later.)
4. ✅ **DONE (2026-07-23) — Working-paper thumbnail replaced** with the paper's headline event-study figure (cropped to 16:9), replacing the unreadable page screenshot. See the 2026-07-23 session notes + "Detail-page featured images".

**Medium**
5. **Decided — KEEP justified text everywhere** (incl. mobile), per user preference, even though it causes word-gap "rivers" in the narrow mobile column. No change. (Documented so it isn't "fixed" later.)
6. ✅ **DONE (2026-06-23) — Talks heading "All Talks" → "Talks"** (collection block title in `content/talks.md`). Also fixed a latent bug there: the page's front-matter `title` was a copy-paste leftover `"Publications"`, so the Talks browser tab/SEO title read "Publications" — now `"Talks"`.
7. ✅ **DONE (2026-07-20) — "See all" links** under both homepage featured sections, via the collection block's built-in `content.archive` (`enable`/`text`/`link`) in `content/_index.md`, styled by the existing accent archive-button rule.
8. ✅ **DONE (2026-07-20) — Featured Talks orphan fixed.** Işık talk set `featured: false`, so the homepage grid now shows two cards (MNB, NGFS-WWF).

**Low / polish**
9. **Mobile hero has a large empty clay gap** between the social icons and "About Me" — could tighten hero vertical spacing at mobile widths.
10. ✅ **DONE (2026-07-20) — Footer nav + contact rows.** `site_footer.html` override adds a `.footer-links` block: a nav row (Publications/Talks/CV, from `site.Menus.main`) and a contact row (Email/X/LinkedIn/Google Scholar, from the admin author's `profiles`). Data-driven, so new menu items / profiles appear automatically. Styled in the Custom CSS AUDIT FIXES block. (A "back to top" was not added.)
11. **Pre-doctoral / student writing is mixed into the "Publications" list** — already tracked separately (the regrouping task); restated here because it surfaced again in the review.

---

## Recent changes (2026-07-20 session)

An accessibility / SEO / navigation pass driven by a full-site audit (the audit's "top 20" list). The user picked a subset to implement; details live in the sections above, this is the index. Deployed in commit `98a9710`.

**Accessibility**
- **Landing-page h1s.** `markdown.html` / `collection.html` gained a `title_level: 1` opt-in; applied so Publications, Talks, and CV each have exactly one `<h1>` (were h2-only). See Custom CSS → Landing-page h1s.
- **Skip-to-content link.** New `layouts/_default/baseof.html` override (near-verbatim vendor copy + the link and a `#main-content` target). Styled `.skip-link` in the AUDIT FIXES CSS block. See the baseof gotcha.
- **CV subtitle contrast.** Moved off the inline `#6b7280` (failed WCAG AA in dark mode, ~3.9:1) to `.cv-subtitle` with light/dark variants.

**SEO**
- **Meta descriptions** added via `summary:` front matter on the three landing pages (`publications.md`, `talks.md`, `experience.md`) — the theme's `site_head.html` reads `summary` first.
- **Author name standardized** to "Gürcan Zeren Gülersoy" across all publication front matter and `cite.bib` (was inconsistently "Gürcan Gülersoy").

**Navigation / UX**
- **Footer** nav + contact rows (backlog #10). **"See all" links** on the homepage featured sections (backlog #7). **Featured Talks** trimmed to two cards (backlog #8). See those backlog entries.
- **Detail-page titles** (publications/talks) now use the serif display font (`.article-title` in `single.html`), matching the site heading system.

**Content / branding**
- **Contact email** swapped from gmail to the institutional `gurcan.gulersoy@kcl.ac.uk` (hero icon, hero CTA, footer). Note: the downloadable `resume.pdf` is built from the separate LaTeX repo and may still carry the old address — update it there too.

**From the audit's top-20 but NOT done (user deferred):** homepage prose → paper links; Google Scholar / Highwire citation meta tags; ORCID; publications regrouping (pre-doctoral split); working-paper thumbnail replacement; talk-page enrichment; news section; per-page descriptions beyond the three landing pages. These remain open if revisited.

---

## Recent changes (2026-07-23 session)

A polish pass on featured images, the abstract layout, and a few small UX/a11y/perf items. All committed in `a7cdb42` and pushed. Details live in the sections above (this is the index).

**Featured images** (see "Detail-page featured images" under Publication Types)
- **Working-paper thumbnail** replaced: the unreadable page-screenshot → the paper's headline event-study figure, cropped to 16:9 (backlog #4). `featured.png` (was `featured.jpg`).
- **OECD Hungarian cover** replaced with a high-resolution render from a user-supplied PDF. First render was horizontally stretched by `pdftoppm -scale-to-x` (see the cover-render gotcha); re-rendered with `-r 194` for the correct A4 portrait aspect.
- **Resolution** raised: detail-page featured images fit to 1600px (was 720px) for retina sharpness. **Encoding** set to webp q85 (was the global q100) so full-size photo covers aren't ~1.5MB. Both in the `single.html` override.

**Abstract layout** — pulled out of the `[200px label | value]` metadata grid into a full-width `.pub-abstract` block above it (was cramped to ~44ch and ran very tall); "Abstract" is now an `<h2>`. `single.html` + `.pub-abstract` in custom.css.

**Small UX / a11y / perf**
- **Alt text** on all featured images (event photos + publication covers); **meta description** added to the 2017 customs brief (`summary:`).
- **"See all" spacing** tightened (`.collection-archive`, `collection.html` hook). **Mobile hero gap** between social icons and "About Me" halved (custom.css, mobile-only).
- **Social sharing image** optimised: `assets/media/sharing.png` (368KB) → `sharing.jpg` (104KB). The theme resolves `media/sharing.*` by extension, so no reference change was needed.

**Still open (user's standing calls):** card-image quality (homepage/Talks cards still at global q100 via the un-overridden `article-grid.html`); global `imaging.quality` (100 → ~85?); publications regrouping / pre-doctoral split; Publications-vs-Talks visual consistency; homepage-prose → paper link; talk-page enrichment; Teaching CV section. Dropped for good: Scholar meta tags, ORCID, News.

---

## Recent changes (2026-07-27 session)

Two small fixes the user spotted on the live site, a page-order change, and the first (cleanup) pass of talk-page enrichment. Commits `e637e5f` and `0be39a5`, both pushed.

**Byline comma spacing (`single.html`)** — detail-page bylines rendered "Name , Name". Each author sits in a `<div>` that carried `mx-1`, so the *previous* name's right margin fell between the name and the comma `<span>` that precedes the next author. Fixed by giving the author wrapper a **leading margin only** (inline `margin-inline-start: 0.25rem`, per the Tailwind caveat) and dropping the comma span's `mr-1`. The Publications page's citation view uses the *vendor* `page_metadata_authors.html`, which already emits `", "` correctly — not affected.

**Landing-page title alignment** — the Talks page title sat 32px lower than Publications and CV. Cause was page-level **`design.spacing`** in `content/talks.md` (`4rem`, vs `2rem` on the other two); the theme renders that as an **inline `padding:`** on each section. Set to `2rem`; all three landing-page `<h1>`s now start at the same offset (verified: 84.8px).

**Publications page order** — `content/publications.md` now lists **Working Papers above Authored Publications** (then Research Contributions), so current work leads.

**Homepage → paper link** (backlog item, partially done) — "My Research" now links *"value-added tax shocks in Uganda"* to `/working-paper/gulersoy-tax-2026/`. Uses a **raw `<a>`** because markdown links silently vanish in `markdown` blocks (see Gotchas). **Turkey (mass layoffs) and Georgia (environmental) have no content pages**, so they stay plain text; linking them needs stub pages first (user's copy).

### Talk-page enrichment — Tier A done, B and C waiting on the user

Audit finding: the theme's `page_links.html` **already** renders PDF / Slides / Video / Code / Poster / Dataset / DOI / custom `links` as buttons, plus the Date / Event / Location grid and the abstract. So enrichment is a **front-matter job, not a layout job**. Done this session (Tier A, no new copy needed):

- **Removed a stale Zoom "Join" button** from `event/isik` — a live CTA into a dead meeting room for a November 2024 seminar.
- **Deleted wrong-talk leftovers**: `isik`'s commented `url_slides`/`url_video` pointed at the *NGFS webinar's* assets (copy-paste trap).
- **Stripped theme-starter boilerplate** from all four `content/event/*/index.md` (placeholder Stanford address, `slides:`/`projects:` examples, stub `links:`, NGFS's empty `url_pdf: ''`); each file keeps a one-line comment saying where slides/video go.
- **Removed the empty `event/mnb/gallery/`** (unused; this vendored theme has no gallery shortcode).
- Current attachment state: **MNB** PDF+Code+Slides+Agenda (richest), **NGFS–WWF** Slides + inline YouTube embed, **Işık** none, **BoE** none.

**Still open on talks (needs the user):** *Tier B* — `url_slides`/`url_video` for BoE and Işık (local PDFs work too, the theme picks up page-folder resources). *Tier C* — **an abstract for the BoE talk** (the only one without one, which is why that page reads thin; a `# TODO:` marks the spot in its front matter) and a short `.Content` body for MNB and Işık, which render empty below the metadata.

**Note:** `assets/media/icons/custom/zoom-svgrepo-com.svg` is now unreferenced (kept deliberately, in case a future live event needs it).
