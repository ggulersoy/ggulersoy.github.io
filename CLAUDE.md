# CLAUDE.md — Site Knowledge Base
## gurcangulersoy.com

---

## Stack & Deployment

- **Static site generator**: Hugo (v0.126.3 per hugoblox.yaml, but locally v0.143.1 is installed)
- **Theme**: [Hugo Blox](https://hugoblox.com) (`blox-tailwind` module), vendored into `_vendor/`
- **CSS**: Tailwind CSS, but processed through Hugo's asset pipeline using the vendor's `tailwind.config.js`
- **Deployment**: Netlify — auto-deploys on every push to `main`. Build command: `hugo --gc --minify -b $URL && npx pagefind --source 'public'`
- **Repo**: `ggulersoy/ggulersoy.github.io` on GitHub, branch `main`
- **Live URL**: https://www.gurcangulersoy.com

### Key build commands
```bash
hugo --minify                        # build site into public/
hugo --minify --cleanDestinationDir  # clean build (removes stale files)
hugo server --port 1314              # local dev server (port 1313 may be in use)
git add <files> && git commit && git push origin main  # deploy
```

> **Do NOT commit the `public/` directory** — Netlify builds it on their end. Only commit source files.

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

## Notes for Common Tasks

**Add a new job entry**: edit `content/authors/admin/_index.md`, add entry under `work:`, add logo to `static/media/icons/companies/` if needed, run `hugo --minify`, commit & push.

**Add a new publication**: create `content/publication/slug/index.md` following existing examples. Use `hugo --minify` to rebuild.

**Change navigation**: edit `config/_default/menus.yaml`.

**Change CV page layout/sections**: edit `content/experience.md`.

**Download CV link**: points to `static/uploads/resume.pdf` — replace that file to update it.

**Dark mode**: fully supported by the theme. Custom template already handles `dark:` variants.
