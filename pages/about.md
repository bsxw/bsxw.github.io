---
layout: page
title: About
description: 工控改变世界
keywords: 灰果果 hui guoguo
comments: true
menu: 关于
permalink: /about/
---

我灰果果，工控人。

坚信熟能生巧，努力改变人生。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
