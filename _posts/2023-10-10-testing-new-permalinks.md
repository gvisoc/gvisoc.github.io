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
This creates a post URL in the following structure: 

```text
https://gvisoc.com/privacy/this%20site/2021/05/12/No-Cookies.html
```

Such structure is cumbersome if your post belong in more than one category, so I decided to include the following configuration line in my `_config.yml`:
```yaml
permalink: /:year/:month/:day/:title
```
This way, the categories are no longer present in the permalinks, and we would have the permalink line the one this post (`/2023/10/10/testing-new-permalinks`). This is better for me, because the URL stays short and with the most meaningful information (the date of posting), while allowing me to re-categorise the posts at any time without breaking existing links.

To prevent breaking the (very few?) existing links to my blog, each post can include its own specific permalink in the **YAML preamble**, so I spent a bit of my time this morning copying and pasting the current permalinks in each post.

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