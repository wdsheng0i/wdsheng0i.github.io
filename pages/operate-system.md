---
layout: page
title: 操作系统
titlebar: operate-system
subtitle: Linux &nbsp;&nbsp; windows &nbsp;&nbsp; CPU &nbsp;&nbsp; 内存 &nbsp;&nbsp; 硬盘 &nbsp;&nbsp; 进程 ...
menu: operate-system
css: [ 'blog-page.css' ]
permalink: /operate-system
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='operate-system' or post.keywords contains 'operate-system' %}
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