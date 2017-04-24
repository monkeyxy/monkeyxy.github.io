---
layout: page
title: Category
order: 10
---

<!-- Html Elements for Search -->
<style type="text/css">
      #search-demo-container {
        max-width: 40em;
        padding: 1em;
        margin: 1em auto;
        border: 1px solid lightgrey;
      }
      #search-input {
        display: inline-block;
        padding: .5em;
        width: 100%;
        font-size: 0.8em;
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
      }
    </style>
  <div id="search-container">
    <input type="text" id="search-input" placeholder="search...">
    <ul id="results-container"></ul>
  </div>

<h3>Total :  <U>{{ site.posts | size }}</U> Posts.</h3>

{% for category in site.categories %}
<hr/>
<h5> - {{ category | first }}</h5>
total : <U>{{ category | last | size }}</U> posts.
<ul>
    {% for post in category.last %}
    <li><a href="{{ post.url }}">{{ post.title }}&nbsp;
        {{ post.date | date:"%d/%m/%Y"}}</a></li>
    {% endfor %}
</ul>
<div class="divider"></div>
{% endfor %}
