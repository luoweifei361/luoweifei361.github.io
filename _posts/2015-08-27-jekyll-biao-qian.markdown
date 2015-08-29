---
layout: news_item
title: 'Jekyll搭建blog-标签示例'
author: nil
categories: [jekyll,blog]
---

使用md的code标签
<code>
  var java = "hello world"
</code>



[history]: /docs/history/#v1-0-0
[Upgrading]: /docs/upgrading/
One change deserves special note. In [#2633][]


bash代码高亮
{% highlight bash %}
$ gem install jekyll 
{% endhighlight %}



高亮ruby代码
{% highlight ruby %}
> sanitized_path("/tmp/foobar/jail", "..c:/..c:/..c:/etc/passwd")
=> "/tmp/foobar/jail/..c:/..c:/..c:/etc/passwd"
{% endhighlight %}

代码高亮

```
String str ="hello";
```

文本高亮`文本高亮`


url超链接[@baidu.com](http://www.baidu.com)



区块引用

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.
>

无序列表 所有使用特殊符号之前必需要有空行

*   Red
*   Green
*   Blue

有序列表

1. Bird
2. Red
3. High


*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
Suspendisse id sem consectetuer libero luctus adipiscing.


如果要在列表项目内放进引用，那 > 就需要缩进

*	A list item with a blockquote:

	> This is a blockquote
	> inside a list item.


分隔线

---

<div class="note unreleased">
  <h5>注意标题</h5>
  <p>
    相关的内容。
  </p>
</div>

图片
![Jekyll Sticker](/img/jekyll-sticker.jpg)