---
author: ed
title: 'Setting Up UFW on Ubuntu Server'
date: 2019-12-15
description: 'UFW is a program that allows you to internally control ports on your Linux instance. This gives you the ability to forward ports from your machine.'
cover:
  image: 'https://images.unsplash.com/photo-1558494949-ef010cbdcc31?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ'
  relative: false
tag:
  - 'Guides'
---

<p><a href="https://help.ubuntu.com/community/UFW">UFW</a> (Uncomplicated Firewall) is a program that allows you to internally control ports on your Linux instance. This gives you the ability to forward ports from your machine.</p>
<p>The common use of a firewall is to control the ports that have access from the outside world, for instance, running a website would need ports <code>80</code>/<code>443</code> exposed on your network to be able to route your site.</p>
<p>UFW is different, think of port forwarding, but between local instances. You can lock down internal exposure to port <code>22</code> (<code>ssh</code>) for example.</p>
<h2 id="whywhatsthepoint">Why? What's the point?</h2>
<p>Security.</p>
<p>In the coming weeks, I'll be writing blog posts on how I have set up my jumpbox server. UFW plays a key part in my setup. I have used UFW to only open port 22 on my jumpbox server. I can <code>ssh</code> in, but that's it. No other ports can be attacked or sniffed.</p>
<p>From the jumpbox server I can then <strong>only</strong> <code>ssh</code> into my other internal instances. This means that if I wanted to <code>ssh</code> into server <code>B</code> I would have to go via the jumpbox <code>A</code>.</p>
<h2 id="ithinkigetithowcaniinstallit">I think I get it, how can I install it?</h2>
<p>We're running <a href="https://ubuntu.com/download/raspberry-pi">Ubuntu Server on a Raspberry Pi</a>. But these instructions are for all Debian instances, the Raspberry Pi is irrelevant for this tutorial.</p>
<h3 id="letsinstall">Let's install</h3>
<ol>
<li>Install UFW</li>
</ol>
<p><code>sudo apt-get install ufw</code></p>
<ol start="2">
<li>Check the status of UFW</li>
</ol>
<p><code>sudo ufw status</code></p>
<p>You should see that UFW is <strong>disabled</strong></p>
<ol start="3">
<li>Let's allow some ports, it's really important that you allow your <code>ssh</code> port, otherwise you can lose access when we get round to enabling UFW.<br>
The default <code>ssh</code> port is <strong>22</strong> unless you have changed the default port.</li>
</ol>
<p><code>sudo ufw allow 22</code></p>
<p>You can use the above command to allow the necessary ports for your instance. We're just going to stick with port <code>22</code> for this example.</p>
<ol start="4">
<li>Let's enable UFW</li>
</ol>
<p><code>sudo ufw enable</code></p>
<h2 id="thatsit">That's it</h2>
<p>You now have a firewall running on your local instance, locked down to be only accessible by port <code>22</code>.</p>
<p>In the future, if you're running services on this box, you'll need to expose any other ports that you want to have access outside of your machine. Let's say you set up <a href="https://openvpn.net/">OpenVPN</a>, you have to expose port <code>1194</code> on the machine it's running on.</p>
<h2 id="thanksforreading">Thanks for reading</h2>
<p>Thanks for reading, hope I've helped in some way!</p>