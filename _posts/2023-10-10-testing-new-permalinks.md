---
layout: post
title:  "Testing New Permalinks"
date:   2023-10-10 7:50:00 +1100
categories:
- internet
- this site
- personal
- jekyll
excerpt_separator: <!--more-->
---
When you begin a new [Jekyll][JEKYLL] site with the only intention of writing, you usually begin with the default theme and configuration. With those, the permanent links (*permalinks*) of the posts have the following structure:

```yaml
/:categories/:year/:month/:day/:title:output_ext
```
<!--more-->
This creates a post URL with the following structure: 

```text
https://gvisoc.com/privacy/this%20site/2021/05/12/No-Cookies.html
```

As many of my posts belong in more than one category, I don't really link those permalinks. The URL often gets too long, and once I publish my post I can't re-categorise my posts without breaking any incoming links. 

The solution for this has two parts.

First of all, I included the following configuration line in `_config.yml`, to change the default permalink structure:
```yaml
permalink: /:year/:month/:day/:title
```
This way, the categories are no longer present in the permalinks, and we would have the permalink line the one this post (`/2023/10/10/testing-new-permalinks`). This fixes all future posts, preventing the categories from appearing in the URL. And, in case you wonder, the categories still work as normal: you can see see this post correctly filed under all its categories in [https://gvisoc.com/categories][CAT].

Second, I needed to lock all previous permalinks into their ~~current~~ previous form in order to prevent breaking the (very few?) existing incoming links. For this, each post can include its own specific permalink in the *YAML preamble*, so I spent a bit of my time this morning copying and pasting the current permalinks in each post.

For example, this:

```yaml
---
layout: post
title: This Site Has Zero Cookies
date: 2021-05-12 9:00
categories:
- privacy
- this site
hidden: true
permalink: /privacy/this site/2021/05/12/No-Cookies.html
excerpt_separator: <!--more-->
---
```

is the preamble of the post "[This Site Has Zero Cookies][NOTRACK]".

Hopefully it all went well!

<br/>
<br/>
<center> - </center>
<br/>
<br/>
*Comment [on this post](https://fedi.gvisoc.com/@gabriel/111207044481014188) on Mastodon, or write to the email at the bottom of the page*
<center><iframe src="https://fedi.gvisoc.com/@gabriel/111207044481014188/embed" class="mastodon-embed" style="max-width: 100%; border: 0" width="400" allowfullscreen="allowfullscreen"></iframe><script src="https://fedi.gvisoc.com/embed.js" async="async"></script></center>

[JEKYLL]: https://jekyllrb.com "Jekyll Site"
[NOTRACK]: /privacy/this site/2021/05/12/No-Cookies.html "This Site Has Zero Cookies"
[CAT]: /categories "Browse All Categories"