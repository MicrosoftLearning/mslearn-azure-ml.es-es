---
title: Instrucciones hospedadas en línea
permalink: index.html
layout: home
---

# Microsoft Learn: Ejercicios prácticos

Los ejercicios prácticos siguientes están diseñados para admitir el entrenamiento de [Microsoft Learn](https://docs.microsoft.com/training/).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| |
| --- | --- | 
{% for activity in labs  %}| [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
