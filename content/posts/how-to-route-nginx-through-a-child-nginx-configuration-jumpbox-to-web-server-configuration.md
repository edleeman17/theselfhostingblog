---
author: ed
title: 'How to Route Nginx Through a Child Nginx Configuration - Jumpbox to Web Server Configuration'
date: 2020-01-02
description: 'Routing nginx through another nginx instance from a central jumpbox server'
tag:
  - 'Guides'
---

<p>I had a specific use case where I needed to run a docker instance, which had it's own configured nginx instance.</p>
<p>The issue was that I already use nginx on my JumpBox.</p>
<p>The fix was relatively self explanatory, but I wanted somewhere to write down the issues that I had to tackle.</p>
<h2 id="thesolution">The Solution</h2>
<p>So the solution is just to point nginx (JumpBox) to the other nginx instance (docker).</p>
<blockquote>
<p>Just to let you know, in my case, there are separate physical servers. The configuration will not differ, but you may need to internally expose ports between hosts if you have something like UFW installed. <a href="https://blog.bowlerdesign.tech/2019/12/15/setting-up-ufw-on-ubuntu-server/">I have a tutorial here on how to set up UFW.</a></p>
</blockquote>
<p>So on my Host <code>B</code> I have a web instance running on port <code>8080</code>. This web instance is an nginx reverse proxy that is pointing to another docker instance within the same docker network on the machine.</p>
<p>Host <code>A</code> is my existing nginx reverse proxy.</p>
<p>The quick solution is to forward your call from Host <code>A</code> to Host <code>B</code>.</p>
<p>Like so</p>
<pre><code>server {
  server_name blog.bowlerdesign.tech;

  location / {
    proxy_pass http://host_running_blog:8080;
  }
</code></pre>
<p>I ran into issues here, I couldn't get css loading properly when navigating to blog.bowlerdesign.tech.</p>
<p>I was missing headers in the request. Here's an ideal configuration for setting up a website.</p>
<pre><code>server {
  server_name blog.bowlerdesign.tech;

  location / {
    proxy_pass http://host_running_blog:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto scheme;
    proxy_pass_header Content-Type;
  }

</code></pre>
<p>This will forward the necessary headers for your host.</p>