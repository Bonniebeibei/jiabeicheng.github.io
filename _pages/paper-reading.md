---
layout: archive
title: "Paper Reading Notes"
permalink: /paper_reading/
author_profile: true
reading_page: true
---

{% include base_path %}

## 🤖 AutoResearch
*MLLMs and AI Agents for streamlining labor-intensive research workflows.*

{% for post in site.paper_reading reversed %}
  {% if post.category == 'autoresearch' %}
    {% include archive-single.html %}
  {% endif %}
{% endfor %}

## 🧬 AI4Bio
*Exploring deep learning applications in single-cell perturbation, multi-omics translation, and H&E-to-omics integration.*

{% for post in site.paper_reading reversed %}
  {% if post.category == 'ai4bio' %}
    {% include archive-single.html %}
  {% endif %}
{% endfor %}
