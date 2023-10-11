---
layout: post
title:  "head | tail: Bash Challenge"
date:   2023-10-11 11:00:00 +1100
categories:
- linux
excerpt_separator: <!--more-->
---
***EDIT***: *Incorporate a better solution*

I broke my own bash limits this week. 

We were given **the following challenge in a forum**:

```
Given a file (ASCII text) called input.txt, which contains 
the following string IN A SINGLE LINE:

DirticaVduURPjDwfEkzjTAmPbDRelukkuraPhedgdnrKSXlIJgMrKayBq
PTTFdngQXoCxJZMImwMrhFidkixjLSAwnqkhdYgWlkbwjEEtwBdZaJhakS
bHQYVBknit

 - Extract the first 4 characters of the string,
 - followed by the 2 characters at positions 56 and 57 
   (counting from 1),
 - and then the last 2 characters of the string. 
 
For a total of 8 characters to be represented in the 
standard output: DirtyBit
 
Do this in one line strictly, using only the 'head' and 
'tail' commands. Pipes are allowed. The following are not:
'if', 'while', 'for', ';', '&', '&&', '${}', '||', 
'$(command)'.
```

**Can you solve it?** I think I did!, but not with the best solution.
<!--more-->

I came up with a solution that was sending only one sentence to `bash`, but it was using multi-line editing mode and thus was rejected.

My second solution, I am pretty sure 100% valid, uses temporary files to be able to construct the output.

<button class="collapsible" id="solution">Click here to reveal the code.</button>
<div class="content" id="solutiondata" markdown="1">
```bash
head -c 4 input.txt > /tmp/dirt.txt | tail -c +56 input.txt | head -c 2 > /tmp/yB.txt | tail -c 3 input.txt > /tmp/it.txt | tail -q /tmp/dir.txt /tmp/yB.txt /tmp/it.txt
```

You can see it working:
```terminal
[gvisoc@vao Downloads]$ head -c 4 input.txt > /tmp/dirt.txt | tail -c +56 input.txt | head -c 2 > /tmp/yB.txt | tail -c 3 input.txt > /tmp/it.txt | tail -q /tmp/dir.txt /tmp/yB.txt /tmp/it.txt
DirtyBit
[gvisoc@vao Downloads]$
```
</div>
<br/>
As the explanation can be spoiling the fun, it's also hidden.

<button class="collapsible" id="rationale">Click here to reveal the rationale.</button>
<div class="content" id="rationaledata" markdown="1">
So the main issue is that the solution to the problem needs to do parallel processing of the same starting string, hence you need to *fork* your process into two or three streams, and then put together everything: I did that with temporary files. 

Aside from that, a bit of checking the manual pages and an understanding on how pipes work for those commands when there are parameters and arguments in the invocations did the trick.
</div>
<br/>
You can check out my answer to the challenge in the forum [here][ANSWER], and also you can consider signing up for that forum at [https://www.linux.org][FORUM] --it's a great site.

**EDIT**: A day after posting my solution and the post you're reading, there were [better answers][BEST] posted in the forum thread. 

It's based on the use of a dash to tell a program to get the rest of the command line arguments from the standard input, that is, the output of the command that was piping onto it. I didn't knew about these hyphens, but I will surely use them from now!

<button class="collapsible" id="bestsolution">Click here to reveal the code.</button>
<div class="content" id="bestsolutiondata" markdown="1">
```bash
tail -c 3 input.txt | head -qc 57 input.txt - | tail -c 5 | head -qc 4 input.txt -
```

You can see it working:
```terminal
[gvisoc@vao Downloads]$ tail -c 3 input.txt | head -qc 57 input.txt - | tail -c 5 | head -qc 4 input.txt -
DirtyBit[gvisoc@vao Downloads]$
```
</div>
<br/>
There's always something new to learn from someone with more experience.
<br/>
<br/>
<center> - </center>
<br/>
<br/>
*Comment [on this post](https://fedi.gvisoc.com/@gabriel/111213465681343718) on Mastodon, or write to the email at the bottom of the page*
<center><iframe src="https://fedi.gvisoc.com/@gabriel/111213465681343718/embed" class="mastodon-embed" style="max-width: 100%; border: 0" width="400" allowfullscreen="allowfullscreen"></iframe><script src="https://fedi.gvisoc.com/embed.js" async="async"></script></center>

[ANSWER]: https://www.linux.org/threads/head-and-tail-trick.47042/post-207400 "My answer to the challenge in the forum"
[FORUM]: https://www.linux.org "The site linux.org"
[BEST]: https://www.linux.org/threads/head-and-tail-trick.47042/post-207467 "Best answer in my opinion"