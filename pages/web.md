---
layout: page
title: 前端 
titlebar: web
subtitle: HTML &nbsp;&nbsp; CSS &nbsp;&nbsp; Javascript &nbsp;&nbsp; Jquery &nbsp;&nbsp; Vue &nbsp;&nbsp; 公众号 &nbsp;&nbsp; 小程序  ...
menu: web
css: ['blog-page.css']
permalink: /web
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='web' or post.keywords contains 'web' %}
                <li class="posts-list-item">
                    <div class="posts-content">
                        <span class="posts-list-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
                        <a class="posts-list-name bubble-float-left" href="{{ post.url }}">{{ post.title }}</a>
                        <span class='circle'></span>
                    </div>
                </li>
                {% endif %}
            {% endfor %}
        </ul> 

        <!-- Pagination -->
        {% include pagination.html %}

        <!-- Comments -->
       <div class="comment">
         {% include comments.html %}
       </div>
    </div>

</div>
<script>
    $(document).ready(function(){

        // Enable bootstrap tooltip
        $("body").tooltip({ selector: '[data-toggle=tooltip]' });

    });
</script>