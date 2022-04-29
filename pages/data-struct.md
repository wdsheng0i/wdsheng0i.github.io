---
layout: page
title: 数据结构 
titlebar: data-struct
subtitle: 数组 &nbsp;&nbsp; 线性表 &nbsp;&nbsp; 哈希 &nbsp;&nbsp; 堆 &nbsp;&nbsp; 栈 &nbsp;&nbsp; 队列 &nbsp;&nbsp; 树 &nbsp;&nbsp; 图
menu: data-struct
css: ['blog-page.css']
permalink: /data-struct
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='data-struct' or post.keywords contains 'data-struct' %}
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