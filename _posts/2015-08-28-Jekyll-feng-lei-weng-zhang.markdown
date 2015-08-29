---
layout: news_item
title: "Jekyll搭建blog-文章分类"
date: "2015-08-28 15:56:52 +0200"
categories: [jekyll,blog]
codeUrl: "Jekyll-feng-lei-weng-zhang"
author: fei361
---

1.首先在文章头部设定文章的分类。下面设定分类为categories: [blog]

{% highlight yaml %}
---
layout: news_item
title: "Jekyll blog 文章分类"	
date: "2015-08-28 15:56:52 +0200"
categories: [blog]
---
{% endhighlight yaml %}

2.对分类后的文章进行输出。

{% highlight html %}
<body>
{% raw %}
	{% for post in site.categories.versions limit:5 %}
	       <a href="{{ post.url }}">Version {{ post.version }}</a>
	{% endfor %}
{% endraw %}
</body>
 
{% endhighlight %}