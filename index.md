# My interests
I'm interested in learning new It technologies for fun and for work.

# My blog posts
Here I blog about my journey with GitHub.
<ul>
    {% for post in site.posts %}
        <li>
            <a href="{{ post.url }}">{{ post.title }} - {{ post.date | date_to_string }}</a>
        </li>
    {% endfor %}
</ul>

My blog posts, by categories

{%- assign categories = site.categories | sort -%}
{%- for category in categories -%}

{%- assign categoryName = category[0] -%}
{%- assign categoryNumPosts = category[1] | size -%}
<h2 id="{{categoryName | uri_escape | downcase }}">{{ categoryName }} ({{ categoryNumPosts }})</h2>

<ul>
    {% assign sorted_posts = category[1] | reversed %}
    {% for post in sorted_posts %}
    <li>
        <a href="{{ post.url }}"></a> -
        <time datetime="{{ post.date | date_to_xmlschema }}"
              itemprop="datePublished">{{ post.date | date: "%b %-d, %Y" }}</time>
    </li>
    {% endfor %}
</ul>

{%- endfor -%}

# Repositories
<ul>
    <li><a href="https://james-hunter.github.io/test1/">Test Repository 1</a></li>        
</ul>

# Get in touch
<ul>
<li><a href="https://github.com/{{ site.github_username}}">GitHub</a></li>
<li><a href="https://www.linkedin.com/in/james-hunter-ba3721114/">Linkedin</a></li>
</ul>

