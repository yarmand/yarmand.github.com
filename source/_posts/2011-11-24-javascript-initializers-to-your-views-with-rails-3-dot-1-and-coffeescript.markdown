---
layout: post
title: "Javascript initializers to your views with Rails 3.1 and CoffeeScript"
date: 2011-09-05 08:56
comments: true
categories: 
---
The Problem
========
With the asset pipeline included in Rails 3.1, we have a great big javascript file including all at once our code.

But what about code isolation through all our sources files ?

[CofeeScript](http://jashkenas.github.com/coffee-script) respond to that perfectly with creation of one function object per file compiled. To add a global variable, you simply define it for the window object :

<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #24909d">window</span><span style="color: #d0d0d0">.foo=</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span>
  <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;foo&#39;</span><span style="color: #d0d0d0">)</span>
</pre></td></tr></table></div>


So all our javascript code is executed when it loads with good code isolation, but what if we want part of the code executed only for one view or all views of a controller ?

call initializers in your layout
==================
I first wrote the gem [Epyce](https://github.com/yarmand/epyce) to limit code loaded to only current controller and view, but this had 2 main issues :

* loose benefit of one big js file to be put on a CDN
* shared code between views must go to controller .js file 

Then I rollback to the big application.js file and add initializers registration to my layout.

Like this for erb:

    application.html.erb
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2
3
4
5
6</pre></td><td><pre style="margin: 0; line-height: 125%">    <span style="color: #6ab825; font-weight: bold">&lt;script </span><span style="color: #bbbbbb">type=</span><span style="color: #ed9d13">&#39;text/javascript&#39;</span><span style="color: #6ab825; font-weight: bold">&gt;</span>
      <span style="color: #d0d0d0">$(</span><span style="color: #24909d">document</span><span style="color: #d0d0d0">).ready(</span> <span style="color: #6ab825; font-weight: bold">function</span><span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">{</span>
        <span style="color: #d0d0d0">Rails.init(</span><span style="color: #ed9d13">&#39;&lt;%=&quot;#{controller.controller_name}&quot;%&gt;&#39;</span><span style="color: #d0d0d0">);</span>
        <span style="color: #d0d0d0">Rails.init(</span><span style="color: #ed9d13">&#39;&lt;%=&quot;#{controller.controller_name}_#{controller.action_name}&quot;%&gt;&#39;</span><span style="color: #d0d0d0">);</span>
      <span style="color: #d0d0d0">});</span>
    <span style="color: #6ab825; font-weight: bold">&lt;/script&gt;</span>
</pre></td></tr></table></div>


or if like me you are a haml:

    application.html.haml
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><pre style="margin: 0; line-height: 125%"><span style="color: #6ab825; font-weight: bold">%script</span><span style="color: #d0d0d0">{</span><span style="color: #24909d">type</span><span style="color: #d0d0d0">:</span> <span style="color: #ed9d13">&#39;text/javascript&#39;</span><span style="color: #d0d0d0">}</span>
  $(document).ready( function() { Rails.init(&quot;<span style="color: #ed9d13">#{</span><span style="color: #d0d0d0">controller.controller_name</span><span style="color: #ed9d13">}</span>&quot;); Rails.init(&quot;<span style="color: #ed9d13">#{</span><span style="color: #d0d0d0">controller.controller_name</span><span style="color: #ed9d13">}</span>_<span style="color: #ed9d13">#{</span><span style="color: #d0d0d0">controller.action_name</span><span style="color: #ed9d13">}</span>&quot;);});
</pre></div>

__ __
Register initializers
----------------- 
Each initializer function can be binded to severals names.

By convention views are named => controllerName_viewName

here is an example of binding a initializer to the Person controller

    persons.js.coffee
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #d0d0d0">Rails.register_init</span> <span style="color: #ed9d13">&#39;persons&#39;</span><span style="color: #d0d0d0">,</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span> 
  <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;hello from Person controller&#39;</span><span style="color: #d0d0d0">)</span>
</pre></td></tr></table></div>

Here is an example of binding several views to one initializer

    persons.js.coffee
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #d0d0d0">Rails.register_init</span> <span style="color: #d0d0d0">[</span><span style="color: #ed9d13">&#39;persons_new&#39;</span><span style="color: #d0d0d0">,</span><span style="color: #ed9d13">&#39;persons_edit&#39;</span><span style="color: #d0d0d0">],</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span>
  <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;editing a Person&#39;</span><span style="color: #d0d0d0">)</span>
</pre></td></tr></table></div>
__ __

initializers are cumulative
----------------------
Calling `register_init()` several times on the same name cumulate initializers

for example:
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2
3
4
5</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #d0d0d0">Rails.register_init</span> <span style="color: #ed9d13">&#39;mytest&#39;</span><span style="color: #d0d0d0">,</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span> <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;foo&#39;</span><span style="color: #d0d0d0">)</span>
<span style="color: #d0d0d0">Rails.register_init</span> <span style="color: #ed9d13">&#39;mytest&#39;</span><span style="color: #d0d0d0">,</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span> <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;bar&#39;</span><span style="color: #d0d0d0">)</span>

<span style="color: #999999; font-style: italic"># ... later in code ...</span>
<span style="color: #d0d0d0">Rails.init(</span><span style="color: #ed9d13">&#39;mytest&#39;</span><span style="color: #d0d0d0">)</span>
</pre></td></tr></table></div>

will produce in console:
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><pre style="margin: 0; line-height: 125%"><span style="color: #d0d0d0">foo</span>
<span style="color: #d0d0d0">bar</span>
</pre></div>
__ __

The registering code
-----------------
To make all the previous code work, we need to define the Rails object.

    application.js.coffee
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%"> 1
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
15
16
17
18
19
20
21
22
23
24
25
26
27
28</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #999999; font-style: italic">#= require_self</span>
<span style="color: #999999; font-style: italic">#= require_tree ./application</span>

<span style="color: #999999; font-style: italic"># From http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/</span>
<span style="color: #d0d0d0">fromPrototype</span> <span style="color: #d0d0d0">=</span> <span style="color: #d0d0d0">(prototype,</span> <span style="color: #d0d0d0">object)</span> <span style="color: #d0d0d0">-&gt;</span>
  <span style="color: #d0d0d0">newObject</span> <span style="color: #d0d0d0">=</span> <span style="color: #24909d">Object</span><span style="color: #d0d0d0">.create(prototype)</span>
  <span style="color: #d0d0d0">`</span><span style="color: #6ab825; font-weight: bold">for</span><span style="color: #d0d0d0">(prop</span> <span style="color: #6ab825; font-weight: bold">in</span> <span style="color: #d0d0d0">object)</span>
    <span style="color: #d0d0d0">{</span>
      <span style="color: #6ab825; font-weight: bold">if</span><span style="color: #d0d0d0">(object.hasOwnProperty(prop))</span>
        <span style="color: #d0d0d0">newObject[prop]</span> <span style="color: #d0d0d0">=</span> <span style="color: #d0d0d0">object[prop];</span>
    <span style="color: #d0d0d0">}`</span>
  <span style="color: #6ab825; font-weight: bold">return</span> <span style="color: #d0d0d0">newObject</span>


<span style="color: #24909d">window</span><span style="color: #d0d0d0">.Rails=fromPrototype</span> <span style="color: #24909d">Array</span><span style="color: #d0d0d0">,</span>
  <span style="color: #40ffff">register_init: </span><span style="color: #d0d0d0">(names,</span> <span style="color: #d0d0d0">fun)</span> <span style="color: #d0d0d0">-&gt;</span>
    <span style="color: #6ab825; font-weight: bold">if</span><span style="color: #d0d0d0">(</span><span style="color: #6ab825; font-weight: bold">typeof</span><span style="color: #d0d0d0">(names.forEach)</span> <span style="color: #d0d0d0">==</span> <span style="color: #ed9d13">&#39;undefined&#39;</span><span style="color: #d0d0d0">)</span>
      <span style="color: #d0d0d0">n=names</span>
      <span style="color: #d0d0d0">names=[n]</span>
    <span style="color: #6ab825; font-weight: bold">for</span> <span style="color: #d0d0d0">n</span> <span style="color: #6ab825; font-weight: bold">in</span> <span style="color: #d0d0d0">names</span>
      <span style="color: #d0d0d0">previously_registered=</span><span style="color: #6ab825; font-weight: bold">this</span><span style="color: #d0d0d0">[n]</span>
      <span style="color: #6ab825; font-weight: bold">this</span><span style="color: #d0d0d0">[n]=()</span> <span style="color: #d0d0d0">-&gt;</span>
        <span style="color: #6ab825; font-weight: bold">if</span><span style="color: #d0d0d0">(</span><span style="color: #6ab825; font-weight: bold">typeof</span><span style="color: #d0d0d0">(previously_registered)</span> <span style="color: #d0d0d0">!=</span> <span style="color: #ed9d13">&#39;undefined&#39;</span><span style="color: #d0d0d0">)</span>
          <span style="color: #d0d0d0">previously_registered.call()</span>
        <span style="color: #d0d0d0">fun.call()</span>
  <span style="color: #40ffff">init: </span><span style="color: #d0d0d0">(name)</span> <span style="color: #d0d0d0">-&gt;</span>
    <span style="color: #6ab825; font-weight: bold">if</span><span style="color: #d0d0d0">(</span><span style="color: #6ab825; font-weight: bold">typeof</span><span style="color: #d0d0d0">(</span><span style="color: #6ab825; font-weight: bold">this</span><span style="color: #d0d0d0">[name])</span> <span style="color: #d0d0d0">!=</span> <span style="color: #ed9d13">&#39;undefined&#39;</span><span style="color: #d0d0d0">)</span>
      <span style="color: #6ab825; font-weight: bold">this</span><span style="color: #d0d0d0">[name]()</span>
</pre></td></tr></table></div>

__Note the usage of `#= require_self` to ensure your Rails object will be defined before including all our others files__
__ __

One more thing
-------------
When registered, initializers can be called using init() and there name as string or directly on the Rails object.
<!-- HTML generated using hilite.me --><div style="background: #202020; overflow:auto;width:auto;color:white;background:black;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;font-size:130%;"><table><tr><td><pre style="margin: 0; line-height: 125%">1
2
3
4
5
6</pre></td><td><pre style="margin: 0; line-height: 125%"><span style="color: #d0d0d0">Rails.register_init</span> <span style="color: #ed9d13">&#39;mytest&#39;</span><span style="color: #d0d0d0">,</span> <span style="color: #d0d0d0">()</span> <span style="color: #d0d0d0">-&gt;</span> <span style="color: #d0d0d0">console.log(</span><span style="color: #ed9d13">&#39;foo&#39;</span><span style="color: #d0d0d0">)</span>

<span style="color: #999999; font-style: italic"># ... later in code ...</span>
<span style="color: #d0d0d0">Rails.init(</span><span style="color: #ed9d13">&#39;mytest&#39;</span><span style="color: #d0d0d0">)</span>
<span style="color: #999999; font-style: italic"># or directly</span>
<span style="color: #d0d0d0">Rails.mytest()</span>
</pre></td></tr></table></div>
__ __


Biblio and links
-------------
I will finish with a great thanks to **Yehuda Katz** for his 2 essential posts about Javascript :

- [Understanding “Prototypes” in JavaScript](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)
- [Understanding JavaScript Function Invocation and “this”](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)

links:

- [CoffeeScript](http://jashkenas.github.com/coffee-script/)
- [Asset Pipeline guide](http://edgeguides.rubyonrails.org/asset_pipeline.html)
- [sprockets (the engine under asset pipeline)](https://github.com/sstephenson/sprockets)

