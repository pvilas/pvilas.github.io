---
layout: page
title: Archivo
permalink: /archive/
---

Veure totes les entrades d'Estribancus ordenades [cronològicament](#crono), per [categories](#cat) o per [etiquetes](#etiq).


## Arxiu cronològic {#crono}

<ul>
  {% for post in site.posts %}

    {% unless post.next %}
      <h3>{{ post.date | date: '%Y' }}</h3>
    {% else %}
      {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
      {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
      {% if year != nyear %}
        <h3>{{ post.date | date: '%Y' }}</h3>
      {% endif %}
    {% endunless %}

        {% capture month %}{{ post.date | date: '%m%Y' }}{% endcapture %}
        {% capture nmonth %}{{ post.next.date | date: '%m%Y' }}{% endcapture %}
        {% if month != nmonth %}
           <h4 >{{ post.date | date: '%B' }}</h4>
        {% endif %}


    <li><a href="{{ post.url }}">{{ post.title }}</a> 
    
    </li>
  {% endfor %}
</ul>


## Arxiu per categories {#cat}


{% for pt in site.categories %}[{{pt[0]}}](#cat-{{pt[0]}}){% if forloop.last %}.{% else %}, {% endif %} {% endfor %}


{% for category in site.categories %}
#### {{ category | first }} {#cat-{{ category | first }}}
<ul>
    {% for posts in category %}
      {% for post in posts %}
        {% if post.title %}
          <li>
            {{post.published}} <a href="{{ post.url }}">{{ post.title }}</a>
          </li>
        {% endif %}
      {% endfor %}
    {% endfor %}
</ul>
{% endfor %}




## Arxiu per etiquetes {#etiq}

{% for pt in site.tags %}[{{pt[0]}}](#tag-{{pt[0]}}){% if forloop.last %}.{% else %}, {% endif %} {% endfor %}

{% for tag in site.tags %}
#### {{ tag | first }} {#tag-{{ tag | first }}}
<ul>
    {% for posts in tag %}
      {% for post in posts %}
        {% if post.title %}
          <li>
            {{post.published}} <a href="{{ post.url }}">{{ post.title }}</a>
          </li>
        {% endif %}
      {% endfor %}
    {% endfor %}
</ul>
{% endfor %}

