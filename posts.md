---
layout: page
title: "Posts"
permalink: /posts/
main_nav: true
---

<a class="anchor" id="top"></a>

<div class="categories-box">
  {% for category in site.categories %}
    <div class="category-box"><a href="#{{ category | first }}" class="category-content">{{ category | first }}</a></div>
  {% endfor %}
</div>

{% for category in site.categories %}
{% capture cat %}{{ category | first }}{% endcapture %}

  <h2>{{ cat | capitalize }}</h2>
  <div id="{{cat}}" class="anchor"></div>
  {% for desc in site.descriptions %}
    {% if desc.cat == cat %}
      <p class="desc"><em>{{ desc.desc }}</em></p>
    {% endif %}
  {% endfor %}
  <ul class="posts-list">
  {% for post in site.categories[cat] %}
    <li>
      <strong>
        <a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
      </strong>
      <span class="post-date">- {{ post.date | date_to_long_string }}</span>
    </li>
  {% endfor %}
  </ul>
  {% if forloop.last == false %}<hr>{% endif %}
{% endfor %}
<br>
