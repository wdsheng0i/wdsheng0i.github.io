---
layout: page
title: ACIDE 
titlebar: acide
subtitle: AI &nbsp;&nbsp; 机器学习 &nbsp;&nbsp; 云计算 &nbsp;&nbsp;物联网 &nbsp;&nbsp;大数据 &nbsp;&nbsp;边缘计算 &nbsp;&nbsp;区块链 &nbsp;&nbsp;以太坊 ... 
menu: acide
css: ['blog-page.css']
permalink: /acide
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='ai' or post.keywords contains 'ai' %}
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