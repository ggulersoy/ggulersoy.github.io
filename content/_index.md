---
# Leave the homepage title empty to use the site title
title: ""
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
      # Show a call-to-action button under your biography? (optional)
      #button:
      #  text: Download CV
      #  url: uploads/resume.pdf
    design:
      css_class: dark
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
        My research lies at the intersection of trade, production networks, and public economics. I study how disruptions — supply chain shocks, fiscal policy changes, and technology adoption — propagate through firm-to-firm transaction networks, with a focus on the amplifying role of large firms.

        A central contribution of my doctoral thesis is the construction of a monthly firm-level input-output matrix for Turkey, derived from novel administrative transaction data. This granular dataset allows me to trace how shocks originate and travel through the economy — generating insights with direct implications for industrial policy design and economic resilience.

        Before joining King's, I worked on production network data as part of the OECD's [LIFT project](https://www.oecd.org/en/about/projects/leveraging-inter-firm-transactions.html), and led econometric work on nature-related financial risks for the Environment Directorate and the National Bank of Georgia — connecting microeconometric rigour with applied policy questions.
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
