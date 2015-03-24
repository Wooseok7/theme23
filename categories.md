---
layout: default
title: Categories
permalink: /categories/
---

<ul class="cate-box">
{% if site.posts != empty %}
{% for cat in site.categories %}
<img src="{{ site.baseurl }}/images/tree2.png" width="25" height="25"></img>
<a href="#{{ cat[0] }}" title="{{ cat[0] }}" rel="{{ cat[1].size }}">{{ cat[0] | join: "/"}}<span class="size"> ({{ cat[1].size }})</span></a><br><br>
{% endfor %}
</ul>

<ul class="cate-box2">
{% for cat in site.categories %}

<div id="{{ cat[0] }}"><img src="{{ site.baseurl }}/images/tree.png" width="25" height="25"></img>&nbsp;{{ cat[0]}}</div>
{% for post in cat[1] %}
&nbsp; &nbsp; &nbsp; &nbsp;<time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>&nbsp; <img src="{{ site.baseurl }}/images/ic_arrow1.gif" width="10" height="10"></img>&nbsp;
<a href="{{ site.baseurl }}{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a><br/>
{% endfor %}
<br>
{% endfor %}
{% else %}
<span>No posts</span>
{% endif %}
</ul>

