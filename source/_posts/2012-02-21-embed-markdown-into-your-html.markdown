---
layout: post
title: "Embed markdown into your HTML"
date: 2012-02-21 01:23
comments: true
categories: 
---
Sometime when you write documentation or HTML5 presentation (using
slide.js for instance), you want to write a part of the document an
easier way.

What about embeding directly some markdown code into you HTML and
convert it on the flight on the browser when displaying ?

What I'd like to write
-
<html>
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><table><tr><td><pre style="margin: 0; line-height: 125%"> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #6ab825; font-weight: bold">&lt;html&gt;</span>
  <span style="color: #6ab825; font-weight: bold">&lt;body&gt;</span>
      <span style="color: #6ab825; font-weight: bold">&lt;article&gt;</span>
         <span style="color: #6ab825; font-weight: bold">&lt;script </span><span style="color: #bbbbbb">type=</span><span style="color: #ed9d13">&quot;text/markdown&quot;</span><span style="color: #6ab825; font-weight: bold">&gt;</span>
<span style="color: #d0d0d0">The</span> <span style="color: #d0d0d0">nice</span> <span style="color: #d0d0d0">title</span> <span style="color: #d0d0d0">here</span>
<span style="color: #d0d0d0">=</span>
<span style="color: #d0d0d0">Lorem</span> <span style="color: #d0d0d0">ipsum</span> <span style="color: #d0d0d0">dolor</span> <span style="color: #d0d0d0">sit</span> <span style="color: #d0d0d0">amet,</span> <span style="color: #d0d0d0">consectetur</span> <span style="color: #d0d0d0">adipiscing</span> <span style="color: #d0d0d0">elit.</span> <span style="color: #d0d0d0">Phasellus</span> <span style="color: #d0d0d0">diam</span> <span style="color: #d0d0d0">sem.</span>

<span style="color: #d0d0d0">then</span> <span style="color: #d0d0d0">a</span> <span style="color: #d0d0d0">little</span> <span style="color: #d0d0d0">list</span>
<span style="color: #d0d0d0">.</span> <span style="color: #d0d0d0">item</span> <span style="color: #3677a9">1</span>
<span style="color: #d0d0d0">.</span> <span style="color: #d0d0d0">item2</span>
        <span style="color: #6ab825; font-weight: bold">&lt;/script&gt;</span>
      <span style="color: #6ab825; font-weight: bold">&lt;/article&gt;</span>
  <span style="color: #6ab825; font-weight: bold">&lt;/body&gt;</span>
<span style="color: #6ab825; font-weight: bold">&lt;/html&gt;</span>
</pre></td></tr></table></div>

</html>

The on the flight conversion
-

[showdown](http://www.showdown.im) is the javascript markdown converter I will use.

At the end of my HTML file and before presentation runtime do its
magick, I do the conversion on all markdown I can find in the file :

<html>
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><table><tr><td><pre style="margin: 0; line-height: 125%"> 1
 2
 3
 4
 5
 6
 7
 8
 9
10</pre></td><td><pre style="margin: 0; line-height: 125%">  <span style="color: #6ab825; font-weight: bold">&lt;script </span><span style="color: #bbbbbb">src=</span><span style="color: #ed9d13">&quot;http://www.showdown.im/showdown/example/showdown.js&quot;</span><span style="color: #6ab825; font-weight: bold">&gt;</span> <span style="color: #6ab825; font-weight: bold">&lt;/script&gt;</span>
  <span style="color: #6ab825; font-weight: bold">&lt;script&gt;</span>
    <span style="color: #d0d0d0">$(</span><span style="color: #6ab825; font-weight: bold">function</span><span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">{</span>
      <span style="color: #999999; font-style: italic">// replace markdown text </span>
      <span style="color: #d0d0d0">$(</span><span style="color: #ed9d13">&#39;script[type=&quot;text/markdown&quot;]&#39;</span><span style="color: #d0d0d0">).after(</span><span style="color: #6ab825; font-weight: bold">function</span><span style="color: #d0d0d0">(index){</span>
        <span style="color: #d0d0d0">converter</span> <span style="color: #d0d0d0">=</span> <span style="color: #6ab825; font-weight: bold">new</span> <span style="color: #d0d0d0">Showdown.converter();</span>
        <span style="color: #6ab825; font-weight: bold">return</span> <span style="color: #d0d0d0">converter.makeHtml(</span><span style="color: #6ab825; font-weight: bold">this</span><span style="color: #d0d0d0">.textContent);</span>
      <span style="color: #d0d0d0">});</span>
    <span style="color: #d0d0d0">});</span>
 <span style="color: #6ab825; font-weight: bold">&lt;/script&gt;</span>
</pre></td></tr></table></div>

</html>
