---
title: "[MIT 18.065] Linear Algebra"
layout: archive
permalink: categories/la
author_profile: true
sidebar_main: true
---

---

💡 이 글은 MIT의 선형대수학 강의 <a href="https://ocw.mit.edu/courses/18-065-matrix-methods-in-data-analysis-signal-processing-and-machine-learning-spring-2018/" target="_blank">MIT 18.065 Spring 2018</a> 및 Gilbert Strang 교수님의 책 『Linear Algebra and Learning from Data』을 따라 정리했습니다. 강의 내용과 일부 연습문제 풀이를 포함합니다.
{: .notice--info}

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign posts = site.categories['MIT 18.065'] %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}