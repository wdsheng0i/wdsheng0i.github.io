---
layout: page
title: 算法 
titlebar: algorithm
subtitle:  搜索 &nbsp;&nbsp; 排序 &nbsp;&nbsp; 递归 &nbsp;&nbsp; 递推 &nbsp;&nbsp; 暴力求解(穷举法) &nbsp;&nbsp; 分治 &nbsp;&nbsp; 动态规划 &nbsp;&nbsp; 贪心 &nbsp;&nbsp; 分支界定 &nbsp;&nbsp; 回溯法
menu: algorithm
css: ['blog-page.css']
permalink: /algorithm
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='algorithm' or post.keywords contains 'algorithm' %}
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