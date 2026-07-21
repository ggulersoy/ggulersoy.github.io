---
# Leave the homepage title empty to use the site title
title: "Publications"
date: 2022-10-24
type: landing

# Meta description for search engines / link previews (the theme's site_head
# reads `summary` first when building <meta name="description">).
summary: "Publications, working papers, and research contributions by Gürcan Zeren Gülersoy, PhD candidate in Economics at King's College London."

design:
  # Default section spacing
  spacing: "2rem"

sections:
  # Page title (rendered as the page's single <h1> — landing pages otherwise
  # have no h1; see the markdown block override).
  - block: markdown
    content:
      title: Publications
      title_level: 1
      text: ''
    design:
      columns: '1'
  - block: collection
    content:
      title: Authored Publications
      text: ""
      filters:
        folders:
          - publication
        exclude_featured: false
    design:
      view: citation

  - block: collection
    content:
      title: Working Papers
      text: ""
      filters:
        folders:
          - working-paper
        exclude_featured: false
    design:
      view: citation

  - block: collection
    content:
      title: Research Contributions
      text: ""
      filters:
        folders:
          - contribution
        exclude_featured: false
    design:
      view: citation
---