---
permalink: /
title: ""
excerpt: ""
author_profile: true
redirect_from:
  - /about/
  - /about.html
sections:
  - id: about
    title: "关于我"
    type: about
  - id: news
    title: "最新动态"
    type: news
  - id: publications
    title: "论文与著作"
    type: publications
  - id: projects
    title: "项目经历"
    type: list
  - id: experience
    title: "工作与实习"
    type: timeline
  - id: education
    title: "教育经历"
    type: timeline
  - id: awards
    title: "荣誉奖项"
    type: list
  - id: talks
    title: "讲座报告"
    type: list
  - id: service
    title: "学术服务"
    type: list
  - id: teaching
    title: "教学经历"
    type: list
  - id: skills
    title: "技能特长"
    type: skills
about:
  summary: "" # 简要介绍自己，支持 Markdown
  highlights: [] # 关键点列表，例如 ["研究方向：XXX", "当前职位：YYY"]
  links: [] # 重要链接列表，每项包含 label、url、description
news: [] # 每项包含 date 与 text，可选 link、icon 字段
publications:
  featured: [] # 每项包含 title、authors、venue、year、links（列表）与 description
  others: [] # 简单字符串或对象（同上字段），展示于“更多成果”部分
projects: [] # 每项包含 name、role、period、description、link
experience: [] # 每项包含 role、org、period、location、description、link
education: [] # 每项包含 degree、school、period、location、description
awards: [] # 每项包含 date、title、description
talks: [] # 每项包含 date、title、event、link
service: [] # 每项包含 role、org、period、description、link
teaching: [] # 每项包含 course、role、period、description、link
skills: [] # 每项包含 name、level、description
---

{% comment %}
在上方 Front Matter 中填写你的信息：
- 将不需要的区块直接从 sections 列表中删除或注释；
- 如果某个区块对应的数据为空（如 [] 或 ""），页面会自动忽略；
- 支持使用 Markdown（如 summary、description 字段）。
{% endcomment %}

{% assign configured_sections = page.sections | default: [] %}
{% if configured_sections != empty %}
{% for section in configured_sections %}
{% assign section_id = section.id %}
{% assign section_title = section.title %}
{% assign section_type = section.type | default: 'list' %}
{% assign should_render = false %}

{% case section_type %}
{% when 'about' %}
  {% assign about_data = page.about %}
  {% if about_data %}
    {% if about_data.summary != blank or (about_data.highlights and about_data.highlights != empty) or (about_data.links and about_data.links != empty) %}
      {% assign should_render = true %}
    {% endif %}
  {% endif %}
{% when 'news' %}
  {% if page.news and page.news != empty %}
    {% assign should_render = true %}
  {% endif %}
{% when 'publications' %}
  {% assign featured = page.publications.featured | default: [] %}
  {% assign others = page.publications.others | default: [] %}
  {% if featured != empty or others != empty %}
    {% assign should_render = true %}
  {% endif %}
{% when 'skills' %}
  {% assign data_items = page.skills | default: [] %}
  {% if data_items != empty %}
    {% assign should_render = true %}
  {% endif %}
{% when 'timeline' %}
  {% assign data_items = page[section_id] | default: [] %}
  {% if data_items != empty %}
    {% assign should_render = true %}
  {% endif %}
{% else %}
  {% assign data_items = page[section_id] | default: [] %}
  {% if data_items != empty %}
    {% assign should_render = true %}
  {% endif %}
{% endcase %}

{% if should_render %}
<span class='anchor' id='{{ section_id }}'></span>
{% if section_id == 'about' %}<span class='anchor' id='about-me'></span>{% endif %}
{% if section_title %}
## {{ section_title }}
{% endif %}

{% case section_type %}
{% when 'about' %}
  {% if about_data.summary != blank %}
{{ about_data.summary | markdownify }}
  {% endif %}

  {% if about_data.highlights and about_data.highlights != empty %}
  {% for highlight in about_data.highlights %}
    {% if highlight != blank %}
- {{ highlight }}
    {% endif %}
  {% endfor %}
  {% endif %}

  {% if about_data.links and about_data.links != empty %}
  {% for link in about_data.links %}
    {% if link.label != blank and link.url != blank %}
- [{{ link.label }}]({{ link.url }}){% if link.description != blank %} — {{ link.description }}{% endif %}
    {% endif %}
  {% endfor %}
  {% endif %}

{% when 'news' %}
  {% for item in page.news %}
    {% assign text = item.text | default: '' %}
    {% assign date = item.date | default: '' %}
    {% assign link = item.link | default: '' %}
    {% assign icon = item.icon | default: '' %}
    {% if text != blank or link != blank %}
- {% if date != blank %}*{{ date }}*{% endif %}{% if icon != blank %} {{ icon }}{% endif %}{% if text != blank %}{% if date != blank %}: {% endif %}{{ text }}{% endif %}{% if link != blank %} [详情]({{ link }}){% endif %}
    {% endif %}
  {% endfor %}

{% when 'publications' %}
  {% assign featured = page.publications.featured | default: [] %}
  {% assign others = page.publications.others | default: [] %}

  {% if featured != empty %}
  {% for pub in featured %}
    {% assign title = pub.title | default: '' %}
    {% assign authors = pub.authors | default: '' %}
    {% assign venue = pub.venue | default: '' %}
    {% assign year = pub.year | default: '' %}
    {% assign description = pub.description | default: '' %}
    {% assign links = pub.links | default: [] %}
    {% if title != blank %}
- {% if title != blank and pub.url %}[{{ title }}]({{ pub.url }}){% else %}_{{ title }}_{% endif %}{% if authors != blank %} · {{ authors }}{% endif %}{% if venue != blank or year != blank %} · {% if venue != blank %}{{ venue }}{% endif %}{% if venue != blank and year != blank %}, {% endif %}{% if year != blank %}{{ year }}{% endif %}{% endif %}
      {% assign valid_links = '' %}
      {% for link_item in links %}
        {% if link_item.url != blank %}
          {% capture link_markup %}[{{ link_item.label | default: '链接' }}]({{ link_item.url }}){% endcapture %}
          {% if valid_links == '' %}
            {% assign valid_links = link_markup %}
          {% else %}
            {% assign valid_links = valid_links | append: ' · ' | append: link_markup %}
          {% endif %}
        {% endif %}
      {% endfor %}
      {% if valid_links != '' %} — {{ valid_links }}{% endif %}
      {% if description != blank %}
        {% capture desc_html %}{{ description | markdownify }}{% endcapture %}
        {% assign desc_clean = desc_html | replace: '<p>', '' | replace: '</p>', '' | strip %}
        {% if desc_clean != '' %}。{{ desc_clean }}{% endif %}
      {% endif %}
    {% endif %}
  {% endfor %}
  {% endif %}

  {% if others != empty %}

**更多成果**

  {% for pub in others %}
    {% if pub.title %}
- {{ pub.title }}{% if pub.authors %} · {{ pub.authors }}{% endif %}{% if pub.venue %} · {{ pub.venue }}{% endif %}{% if pub.year %} · {{ pub.year }}{% endif %}
    {% elsif pub != blank %}
- {{ pub }}
    {% endif %}
  {% endfor %}
  {% endif %}

{% when 'timeline' %}
  {% assign items = page[section_id] | default: [] %}
  {% for entry in items %}
    {% if entry.role or entry.degree or entry.title or entry.org or entry.school or entry != blank %}
      {% assign role = entry.role | default: entry.title | default: entry.degree | default: '' %}
      {% assign org = entry.org | default: entry.school | default: '' %}
      {% assign period = entry.period | default: '' %}
      {% assign location = entry.location | default: '' %}
      {% assign description = entry.description | default: '' %}
      {% assign link = entry.link | default: '' %}
      {% if role != blank or org != blank or description != blank %}
- {% if role != blank %}**{{ role }}**{% endif %}{% if org != blank %}{% if role != blank %} · {% endif %}{{ org }}{% endif %}{% if period != blank %} （{{ period }}）{% endif %}{% if location != blank %} · {{ location }}{% endif %}{% if link != blank %} [详情]({{ link }}){% endif %}
        {% if description != blank %}
          {% capture entry_desc_html %}{{ description | markdownify }}{% endcapture %}
          {% assign entry_desc = entry_desc_html | replace: '<p>', '' | replace: '</p>', '' | strip %}
          {% if entry_desc != '' %}
  {{ entry_desc }}
          {% endif %}
        {% endif %}
      {% endif %}
    {% endif %}
  {% endfor %}

{% when 'skills' %}
  {% assign items = page.skills | default: [] %}
  {% for skill in items %}
    {% assign name = skill.name | default: '' %}
    {% assign level = skill.level | default: '' %}
    {% assign description = skill.description | default: '' %}
    {% if name != blank %}
- **{{ name }}{% if level != blank %}（{{ level }}）{% endif %}**{% if description != blank %}：{{ description }}{% endif %}
    {% elsif skill != blank %}
- {{ skill }}
    {% endif %}
  {% endfor %}

{% else %}
  {% assign items = page[section_id] | default: [] %}
  {% for item in items %}
    {% if item.title %}
- **{{ item.title }}**{% if item.subtitle %} · {{ item.subtitle }}{% endif %}{% if item.period %} （{{ item.period }}）{% endif %}{% if item.description %}：{{ item.description }}{% endif %}{% if item.link %} [详情]({{ item.link }}){% endif %}
    {% elsif item != blank %}
- {{ item }}
    {% endif %}
  {% endfor %}
{% endcase %}


{% endif %}

{% endfor %}
{% endif %}