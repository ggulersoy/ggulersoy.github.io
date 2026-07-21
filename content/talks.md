---
# Drives the browser tab + SEO title (was mistakenly "Publications").
title: "Talks"
date: 2022-10-24
type: landing

# Meta description for search engines / link previews (the theme's site_head
# reads `summary` first when building <meta name="description">).
summary: "Talks and seminars by Gürcan Zeren Gülersoy, including presentations at the Bank of England, the Magyar Nemzeti Bank, and the NGFS-WWF webinar series."

design:
  # Default section spacing
  spacing: "4rem"

sections:
  - block: collection
    id: talks
    content:
      title: Talks
      # Render as the page's single <h1> (see the collection block override).
      title_level: 1
      filters:
        folders:
          - event
        featured_only: false
    design:
      view: article-grid
      columns: 2
---