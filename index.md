# James' Website

# My interests
I'm interested in learning new It technologies for fun and for work.

# My blog
Here I blog about my journey with gitHub.
<ul>
    {% for post in site.posts %}
        <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
        </li>
    {% endfor %}
</ul>

# Repositories
<ul>
    <li><a href="https://james-hunter.github.io/test1/">Test Repository 1</a></li>        
</ul>

# Get in touch
<ul>
<li><a href="https://github.com/{{ site.github_username}}">GitHub</a></li>
</ul>

