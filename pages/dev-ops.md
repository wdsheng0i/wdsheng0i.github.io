---
layout: page
title: Dev&Ops
titlebar: dev-ops
subtitle: JDK &nbsp;&nbsp;Maven &nbsp;&nbsp; Git &nbsp;&nbsp; Docker &nbsp;&nbsp; K8s &nbsp;&nbsp; Jenkins &nbsp;&nbsp; Yapi &nbsp;&nbsp; JIRA &nbsp;&nbsp; Sonar  ...
menu: dev-ops
css: ['blog-page.css']
permalink: /dev-ops
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='dev-ops' or post.keywords contains 'dev-ops' %}
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