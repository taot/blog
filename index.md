---
layout: default
---
<div class="row">
    <div class="large-8 column">
        <ul class="main posts">
            {% for post in site.posts %}
            {% include post_item.html post=post %}
            <!--li class="large-12 column">
                <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
            </li-->
            {% endfor %}
        </ul>
    </div>
    <div class="large-4 column">
        <div>
            Tags
        </div>
        <ul class="main tags">
            {% for tag in site.tags %}
            <li class="large-12 column">
                <a href="{{ site.url }}/tag/{{ tag[0] }}/index.html">{{ tag[0] }}</a>
            </li>
            {% endfor %}
        </ul>
    </div>
</div>
