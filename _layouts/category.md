---
layout default
---

div class=blog list
    h1Filed Under small#{{ page.tag }}smallh1

    {% for post in site.categories[page.tag] %}
        {% include post_preview.html %}
    {% endfor %}
div