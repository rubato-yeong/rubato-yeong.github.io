---
title: "[CS285] Deep Reinforcement Learning"
layout: archive
permalink: categories/CS285
author_profile: true
sidebar_main: true
---

---

💡 이 시리즈는 『2024 PseudoLab 전반기 강화학습팀』으로 진행되었으며, <a href="https://rail.eecs.berkeley.edu/deeprlcourse/" target="_blank">CS285 Fall 2023</a>를 따라 정리했습니다.
{: .notice--info}

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign posts = site.categories.CS285 %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}