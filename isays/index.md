---
layout: page
title: e̶s̶s̶a̶y̶s̶  &nbsp;isays - opinionated opinions
excerpt: "An archive of random thoughts sorted by date."
search_omit: true
---

<ul class="post-list">
{% for post in site.categories.isays %}
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span>{% if post.excerpt %} <span class="excerpt">{{ post.excerpt | remove: '\[ ... \]' | remove: '\( ... \)' | markdownify | strip_html | strip_newlines | escape_once }}</span>{% endif %}</a></article></li>
{% endfor %}
</ul>
