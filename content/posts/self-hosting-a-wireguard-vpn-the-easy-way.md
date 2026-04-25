---
author: ed
title: 'Self-hosting a Wireguard VPN, the easy way'
date: 2020-09-13
description: 'We''re going to cover setting up a Wireguard VPN on your home server or cloud service. For secure remote access to your internal network, or a cheap, secure connection to a cloud service for some incre'
cover:
  image: 'https://images.unsplash.com/photo-1542577195-d562c6698ff3?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ'
  relative: false
tag:
  - 'Guides'
---

<h2 id="introduction">Introduction</h2><p>We're going to cover setting up a Wireguard VPN on your home server or cloud service. For secure remote access to your internal network, or a cheap, secure connection to a cloud service for <em>some</em> increased privacy when browsing online.<br>VPN's don't make you anonymous, there's a lot of stigma around this. Here's some <a href="https://www.privacytools.io/providers/vpn/">helpful information</a> if you want to read into this some more.</p><h2 id="setting-up-wireguard-the-easy-way">Setting up Wireguard, the easy way</h2><p>I initially found setting up Wireguard confusing. Keys kept getting mixed up, I had no way of sending public keys between devices so that I could set up a client on my mobile device etc..</p><p>After around 30 seconds of lazy Duck Duck Go'ing (Not quite the same ring to it). I found a script on Github provided by <a href="https://github.com/angristan">angristan</a>. Here's <a href="https://github.com/angristan/wireguard-install">the repo</a>.</p><p>It's just a bash script which does all of the config for you, but still providing user prompts for entering the public server IP and choosing a preferred DNS address for the server.</p><h3 id="step-1-clone-and-execute-the-wireguard-installer">Step 1: Clone and execute the Wireguard Installer</h3><p>After ssh'ing to your server, whether it's local, or cloud-hosted. If you're thinking of cloud hosting your Wireguard VPN for some privacy, I'd highly recommend using <a href="https://www.digitalocean.com/">Digital Ocean</a>. Using the following link will give you $100 worth of credits for 60 days to play around with, just sign up using <a href="https://m.do.co/c/d2a3afe52625">this link</a>.</p><p>You could also use a cheap <a href="https://amzn.to/3cWTlno">Raspberry PI</a> to set up your own Linux server.</p><p>Anyway, after you have accessed your machine, we need to pull down the Wireguard installer code from Github. We're just going to <code>curl</code> it.</p><pre><code class="language-bash">curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh</code></pre><p>Now we just need to change the file permissions to allow execution of the new <code>.sh</code> scipt we've just downloaded.</p><pre><code class="language-bash">sudo chmod +x wireguard-install.sh</code></pre><p>Finally, execute the Wireguard Installer</p><pre><code class="language-bash">sudo ./wireguard-install.sh</code></pre><h3 id="step-2-configuring-wireguard">Step 2: Configuring Wireguard</h3><p>This is the easy part.</p><p>You'll see below the process of setting up Wireguard using the Wireguard Installer. All of the values below were picked for me, I just had to hit <code>return</code> a few times.</p><pre><code class="language-bash">➜  Ed sudo ./wireguard-install.sh
Welcome to the WireGuard installer!
The git repository is available at: https://github.com/angristan/wireguard-install

I need to ask you a few questions before starting the setup.
You can leave the default options and just press enter if you are ok with them.

IPv4 or IPv6 public address: 37.120.198.182
Public interface: eth2
WireGuard interface name: wg0
Server's WireGuard IPv4: 10.66.66.1
Server's WireGuard IPv6: fd42:42:42::1
Server's WireGuard port [1-65535]: 57281
First DNS resolver to use for the clients: 176.103.130.130
Second DNS resolver to use for the clients (optional): 176.103.130.131

Okay, that was all I needed. We are ready to setup your WireGuard server now.
You will be able to generate a client at the end of the installation.
Press any key to continue...</code></pre><p>After running through those steps above, the Wireguard Installer will do its thing and set up Wireguard for you. You'll eventually be left with a prompt to set up a new client.</p><pre><code class="language-bash">Tell me a name for the client.
The name must consist of alphanumeric character. It may also include an underscore or a dash.
Client name: Phone
Client's WireGuard IPv4: 10.66.66.2
Client's WireGuard IPv6: fd42:42:42::2</code></pre><p>Here's my config, I just entered a name and the rest was generated for me.</p><p>What's also really cool, is that a QR code gets generated in the console window, which you can scan with your new device.</p><p>You'll also have a <code>.conf</code> file generated for you to copy to your device.</p><h3 id="portforwarding">Portforwarding</h3><p>Remember we had to specify a port? We'll need to forward that. There are a million different tutorials on the web for how to port-forward for your router. </p><p>Here's a <a href="https://portforward.com/">handy guide.</a></p><h3 id="final-steps">Final steps</h3><p>All that's now left to do is to set up Wireguard on your device. Simply download the required app/program onto your machine and either scan the provided QR code or import that <code>.conf</code> file into your client.</p><p>Then enable your VPN. Let me know how it goes.</p>