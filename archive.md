---
layout: page
title: Archive
permalink: /archive/
---
<article>


{% for post in site.posts %}
{% capture month %}{{ post.date | date: '%m%Y' }}{% endcapture %}
{% capture nmonth %}{{ post.next.date | date: '%m%Y' }}{% endcapture %}
{% if month != nmonth %}
{% if forloop.index != 1 %}</ul>{% endif %}
<h3>{{ post.date | date: '%Y년 %m월' }}</h3><ul>
{% endif %}
<div><img src="{{ site.baseurl }}/images/tree.png" width="25" height="25">&nbsp;<time datetime="{{ post.date | date:"%Y-%m-%d" }}" style="font-family:Arial">{{ post.date | date:"%Y-%m-%d" }}</time>&nbsp; <img src="{{ site.baseurl }}/images/ic_arrow1.gif" width="10" height="10"></img>&nbsp;&nbsp;<a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></img></div>
 
{% endfor %}


</article>
