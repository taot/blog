---
layout: default
---
<div class="row">
    <div class="large-10 column">
        <ul class="posts">
            {% for post in site.posts %}
            {% include post_item.html post=post %}
            {% endfor %}
        </ul>
    </div>
    <div class="large-2 column">
        {% include taglist.html %}
    </div>
</div>
