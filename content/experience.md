---
title: 'CV'
date: 2023-10-24
type: landing

# Meta description for search engines / link previews (the theme's site_head
# reads `summary` first when building <meta name="description">).
summary: "Curriculum vitae of Gürcan Zeren Gülersoy: PhD candidate in Economics at King's College London, with experience at the OECD, INSEAD, and BBVA Research."

design:
  spacing: '2rem'

# Note: `username` refers to the user's folder name in `content/authors/`

# Page sections
sections:
  - block: markdown
    content:
      title: 'Curriculum Vitae'
      # Render as the page's single <h1> (see the markdown block override).
      title_level: 1
      # Subtitle colour lives in custom.css (.cv-subtitle) with light/dark
      # variants — the previous inline #6b7280 failed WCAG AA in dark mode.
      text: |-
        <div style="text-align:center;">
          <p class="cv-subtitle" style="font-size:1.05rem;margin-bottom:1.5rem;">PhD candidate in Economics, King's College London<br>Economic Consultant, OECD</p>
          <a href="/uploads/resume.pdf" style="display:inline-flex;align-items:center;gap:0.5rem;padding:0.55rem 1.4rem;font-size:0.875rem;font-weight:500;background-color:transparent;border:1.5px solid currentColor;border-radius:0.5rem;text-decoration:none;" target="_blank" rel="noopener">
            <svg xmlns="http://www.w3.org/2000/svg" style="width:1rem;height:1rem;flex-shrink:0;" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/></svg>
            Download PDF
          </a>
        </div>
    design:
      columns: '1'
  - block: resume-experience
    content:
      username: admin
    design:
      # Hugo date format
      date_format: 'January 2006'
      # Education or Experience section first?
      is_education_first: false
  - block: resume-skills
    content:
      title: Skills & Interests
      username: admin
    design:
      show_skill_percentage: false
  #- block: resume-awards
  #  content:
  #    title: Awards
  #    username: admin
---
