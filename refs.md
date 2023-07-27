---
subtitle: 3-2-1 let's jam
permalink: /refs/
---

all subtitle music references for your listening pleasure. \m/

{% assign refs = site.posts | map: "ref" | compact | sort %}

{% for item in refs %}
{{ item }}
{% endfor %}
