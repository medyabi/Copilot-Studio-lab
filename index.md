---
title: Online Hosted Instructions
permalink: index.html
layout: home
---

# From simple agents to autonomous agents

Microsoft 365 Copilot **Agent Builder** and **Microsoft Copilot Studio**, hands-on, one step at a time.

Each lab builds on the previous one. You start with an agent that only follows instructions, and you finish with an autonomous agent that wakes up on its own, triages work, asks a human when it should, and reports what it did.

## Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Module | Lab |
| --- | --- |
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
