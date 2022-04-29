---
layout: page
title: 数据库 
titlebar: database
subtitle: 数据库基础知识 &nbsp;&nbsp; DB选型 &nbsp;&nbsp; 数据库设计规范  &nbsp;&nbsp; Mysql &nbsp;&nbsp; 执行计划 &nbsp;&nbsp; sql优化 &nbsp;&nbsp; 分库分表 &nbsp;&nbsp; 事务 &nbsp;&nbsp; 锁机制 ...
menu: database
css: ['blog-page.css']
permalink: /database
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='database' or post.keywords contains 'database' %}
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