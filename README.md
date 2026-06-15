# gurcangulersoy.com

Source for the personal academic website of Gürcan Zeren Gülersoy,
served at <https://www.gurcangulersoy.com>.

## Stack

- [Hugo](https://gohugo.io/) static site generator with the
  [Hugo Blox](https://hugoblox.com) theme (`blox-tailwind`), vendored under
  `_vendor/`.
- Tailwind CSS, processed through Hugo's asset pipeline.
- Deployed to **GitHub Pages** via GitHub Actions
  (`.github/workflows/publish.yaml`) on every push to `main`.

## Local development

```bash
hugo server --port 1314    # live preview at http://localhost:1314
hugo --minify              # production build into public/
```

The `public/` and `resources/` directories are build output and are not
committed; CI regenerates them on deploy.

## Content

Most of the CV data (work, education, skills, languages, profiles) lives in
[`content/authors/admin/_index.md`](content/authors/admin/_index.md).
Publications, working papers, talks, and contributions are individual pages
under `content/`.

## License

Site content is licensed under
[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).
