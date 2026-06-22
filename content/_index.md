---
# Sets the <title> tag + og:title (browser tab and Google result link) only;
# the navbar wordmark is separate (header.navbar.logo.text). Name first for
# name searches, affiliation for topic context.
title: "Gürcan Zeren Gülersoy · Economics PhD, King's College London"
date: 2022-10-24
type: landing

design:
  # Default section spacing
  spacing: "4rem"

sections:
  - block: resume-biography-3
    content:
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin
      text: ""
      # Hero call-to-action buttons (styled in custom.css for the clay hero).
      buttons:
        - text: Download CV
          url: /uploads/resume.pdf
          style: primary
          new_tab: true
        - text: Email me
          url: mailto:gurcangulersoy@gmail.com
          style: secondary
    design:
      css_class: dark
      # Tighter than the site-wide 4rem default so the intro fits the initial
      # viewport (top / right / bottom / left).
      spacing:
        padding: ['1.25rem', '0', '1.75rem', '0']
      background:
        color: "#993623"
        image:
          # Add your image background to `assets/media/`.
          filename: world-map-lived.svg
          filters:
            brightness: 1.0
          size: cover
          position: center
          parallax: false
  - block: markdown
    content:
      title: 'My Research'
      subtitle: ''
      text: |-
        My current research interests lie in the dynamics of shock transmission and spillovers within production networks, with a particular focus on the pivotal role of large firms.

        My doctoral thesis is built around three essays, each tracing a different kind of shock through these networks: shocks from tax policy, from the labour market, and from industrial policy.

        This work draws on administrative data recording firm-to-firm transactions, letting me map how disruptions travel between connected firms across the economy.

        My ongoing work focuses on the transmission of value-added tax shocks in Uganda, mass-layoff shocks in Turkey, and environmental shocks in Georgia.
    design:
      columns: '1'
  - block: collection
    id: papers
    content:
      title: Featured Publications
      filters:
        folders:
          - publication
          - working-paper
        featured_only: true
    design:
      view: article-grid
      columns: 2
  #- block: collection
  #  content:
  #    title: All Publications
  #    text: ""
  #    filters:
  #      folders:
  #        - publication
  #      exclude_featured: false
  #  design:
  #    view: citation
  - block: collection
    id: talks
    content:
      title: Featured Talks
      filters:
        folders:
          - event
        featured_only: true 
    design:
      view: article-grid
      columns: 2
  #- block: collection
  #  id: news
  #  content:
  #    title: Recent News
  #    subtitle: ''
  #    text: ''
  #    # Page type to display. E.g. post, talk, publication...
  #    page_type: post
  #    # Choose how many pages you would like to display (0 = all pages)
  #    count: 5
  #    # Filter on criteria
  #    filters:
  #      author: ""
  #      category: ""
  #      tag: ""
  #      exclude_featured: false
  #      exclude_future: false
  #      exclude_past: false
  #      publication_type: ""
  #    # Choose how many pages you would like to offset by
  #    offset: 0
  #    # Page order: descending (desc) or ascending (asc) date.
  #    order: desc
  #  design:
  #    # Choose a layout view
  #    view: date-title-summary
  #    # Reduce spacing
  #    spacing:
  #      padding: [0, 0, 0, 0]
---
