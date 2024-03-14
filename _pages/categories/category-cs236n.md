---
title: "[CS236n] Deep Generative Models"
layout: archive
permalink: categories/cs236n
author_profile: true
sidebar_main: true
---

---

💡 이 시리즈는 『2024 YAI 봄전반기 생성모델팀』으로 진행되었으며, <a href="https://deepgenerativemodels.github.io/syllabus.html" target="_blank">CS236n Fall 2023</a>를 따라 정리했습니다.
{: .notice--info}

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign posts = site.categories.CS236n %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}