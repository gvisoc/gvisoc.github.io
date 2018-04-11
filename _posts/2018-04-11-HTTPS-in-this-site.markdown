---
layout: post
title:  "HTTPS in This Site"
date:   2018-04-11 17:29:00 +1000
comments: "true"
categories:
  - "this site"
tags:
  - SSL
  - HTTPS
excerpt_separator: <!--more-->
---
The HTTPs setup in this blog has been made through [Cloudflare](https://www.cloudflare.com) and has some caveats.
<!--more-->

The hosting for these pages is [GitHub Pages](https://pages.github.com/) in [GitHub](https://github.com), and the HTTPs setup has been made following the advice of [this blog post from Cloudflare](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/). As I posted in their comments (pending approval at the time of writing), the post doesn't work as it is written. That is because they state that it would work setting the HTTPS mode to "Full", but it won't do unless the HTTPS mode is set to "Flexible".

The problem: as soon as you push the `CNAME` file into your GitHub Pages repository, GitHub will read the content and switch off HTTPS on their side, as shown in this picture:

![GitHub has detected the CNAME content and auto-disabled SSL](/assets/githubpages-customdomain.png){:class="img-responsive"}

This is not an "HTTPS Full" valid scenario, so you will receive an unavoidable and ugly **SSL Version / Protocol Mismatch** error that will cause your content to be inaccessible for security reasons. The only way that I know to enable HTTPS in your GitHub Pages (with custom domain) is to set the HTTPS mode to "Flexible", **embracing the fact that the traffic between Cloudflare and GitHub will be unencrypted**.

Nonetheless, my hosting knowledge is rather basic, so if you know a workaround to avoid setting the CNAME file in GitHub Pages and get the same all-redirecting result, please leave your feedback in the comments section.

{% comment %}
{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
{% endcomment %}
