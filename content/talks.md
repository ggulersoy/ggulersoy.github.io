---
# Leave the homepage title empty to use the site title
title: "Publications"
date: 2022-10-24
type: landing

design:
  # Default section spacing
  spacing: "4rem"

sections:
  - block: collection
    id: talks
    content:
      title: All Talks
      filters:
        folders:
          - event
        featured_only: false
    design:
      view: article-grid
      columns: 2
---