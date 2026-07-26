---
layout: page
title: Araçlar
permalink: /araclar
---

<ul>
  {% for p in site.pages %}
    {% if p.dir == '/araclar/' %}
      <li>
        <a href="{{ p.url }}" class="text-black"><strong>{{ p.title }}</strong></a><br>
        {{ p.description }}
      </li>
    {% endif %}
  {% endfor %}
</ul>
