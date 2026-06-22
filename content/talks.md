---
# Drives the browser tab + SEO title (was mistakenly "Publications").
title: "Talks"
date: 2022-10-24
type: landing

design:
  # Default section spacing
  spacing: "4rem"

sections:
  - block: collection
    id: talks
    content:
      title: Talks
      filters:
        folders:
          - event
        featured_only: false
    design:
      view: article-grid
      columns: 2
---