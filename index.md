---
layout: default
---
<div class="row">
    <div id="posts" class="large-8 column">
        <ul>
            {% for post in site.posts %}
            {% include post_item.html post=post %}
            <!--li class="large-12 column">
                <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
            </li-->
            {% endfor %}
        </ul>
    </div>
    <div id="tags" class="large-4 column">
        <ul>
            {% for tag in site.tags %}
            <li class="large-12 column">
                <a href="{{ site.url }}/tag/{{ tag[0] }}/index.html">{{ tag[0] }}</a>
            </li>
            {% endfor %}
        </ul>
    </div>
</div>
