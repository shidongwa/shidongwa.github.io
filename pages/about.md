---
layout: page
title: About
description: IT老兵日记
keywords: Shi Donghua, 石东华
comments: true
menu: 关于
permalink: /about/
---

混迹魔都的程序员老兵。工作多年，越来越觉得知易行难（刚工作时一位前领导的感悟）。程序员如同体育，娱乐行业，人才辈出，就算IT老炮也一日不敢懈怠。

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
