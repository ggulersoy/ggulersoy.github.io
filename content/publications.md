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
      title: Research Contributions
      text: ""
      filters:
        folders:
          - contribution
        exclude_featured: false
    design:
      view: citation      
---
