---
title: 'Publications'
date: 2024-05-19
type: landing


design:
  # Section spacing
  spacing: '5rem'

# Page sections
sections:
  - block: collection
    content:
      title: Selected Publications
      text: ''
      filters:
        folders:
          - publication
    design:
      date_format: 'January 2006'
      view: card
      fill_image: true
      columns: 2
  - block: collection
    content:
      title: Conference
      text: ''
      filters:
        folders:
          - conference
    design:
      date_format: 'January 2006'
      view: article-grid
      fill_image: true
      columns: 2
  - block: collection
    content:
      title: Work in Progress
      text: ''
      filters:
        folders:
          - workpaper
        #exclude_featured: false
    design:
      view: citation
      columns: 1

---
