---
author: ed
title: 'Setting up Authelia to work with Nginx'
date: 2021-08-25
draft: true
description: 'What is Authelia? Have you ever thought to yourself, "I want to make something publicly available, but I don''t want everyone to be able to see it. Be cool if I could have a login screen"? Well, if so,'
cover:
  image: 'https://images.unsplash.com/photo-1603899122634-f086ca5f5ddd?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDZ8fGxvY2t8ZW58MHx8fHwxNjI5OTE5MzUw&ixlib=rb-1.2.1&q=80&w=2000'
  relative: false
tag:
  - 'Guides'
---

<h1 id="what-is-authelia">What is Authelia?</h1><p>Have you ever thought to yourself, "I want to make something publicly available, but I don't want everyone to be able to see it. Be cool if I could have a login screen"?</p><p>Well, if so, Authelia is the answer.</p><p>Authelia allows you to authenticate yourself via a login screen ( + 2 Factor Auth). Which will then unlock any service behind it.</p><p>Authelia acts as a proxy middleware, which only allows authenticated traffic. This tutorial will be to configure Authelia with Nginx.</p><h1 id="setting-up-authelia">Setting up Authelia</h1><p>You'll need to either purchase something like a <a href="https://www.amazon.co.uk/gp/search?ie=UTF8&amp;tag=bowlerdesign-21&amp;linkCode=ur2&amp;linkId=838d86d54be7e521a789421988ebe7d1&amp;camp=1634&amp;creative=6738&amp;index=computers&amp;keywords=Raspberry%20Pi"><strong><strong>Raspberry Pi</strong></strong></a> with Ubuntu Server installed or use an existing server at your disposal.</p><p>I use a <a href="https://www.proxmox.com/en/">Proxmox</a> instance running on a server in my loft.</p><p>You could also use something like <a href="https://www.digitalocean.com/"><strong><strong>Digital Ocean</strong></strong></a> to run your Docker server, Using the following link will give you $100 worth of credits for 60 days to play around with, just sign up using <a href="https://m.do.co/c/d2a3afe52625"><strong><strong>this link</strong></strong></a>.</p><h3 id="step-1-updating-your-server-to-the-latest-version">Step 1: Updating your server to the latest version</h3><p>All of the following instructions are based on the Debian distro, so if you're running a server with Ubuntu, these instructions will be perfect for you. If not, you may have to adjust the commands below to suit your distro.</p><p>The first step is to just make sure our server is up to date. Run the following commands to pull down the latest updates from our distro repositories.</p><pre><code class="language-bash">sudo apt-get update &amp;&amp; sudo apt-get upgrade</code></pre><p>You should see an output like the following</p><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/images/2021/07/image-5.png" class="kg-image" alt loading="lazy" width="596" height="231"><figcaption>Console output for running the update and upgrade commands</figcaption></figure><h3 id="step-2-installing-docker-on-your-server">Step 2: Installing Docker on your server</h3><p>We need to install Docker. To install Docker on your instance, you need to run the following command.</p><p>The following script is a convenience script <a href="https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script"><strong><strong>provided by the Docker team</strong></strong></a>. It's highly recommended to always check what you're going to execute, before executing it.</p><figure class="kg-card kg-code-card"><pre><code class="language-bash">curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh</code></pre><figcaption>Installing Docker using the convenience script</figcaption></figure><p>Once you have executed the Docker install script. You should see an output like the following.</p><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/images/2021/07/image-6.png" class="kg-image" alt loading="lazy" width="856" height="878" srcset="/images/size/w600/2021/07/image-6.png 600w, /images/2021/07/image-6.png 856w" sizes="(min-width: 720px) 720px"><figcaption>Docker convenience script install output</figcaption></figure><p>As you can see in the output, the command was executed successfully. You may also notice that there is a console message specifying how to use Docker as a non-root user.</p><p>This means that whenever you are executing the Docker command, you'll no longer need to type in your sudo password.</p><p>If this sounds good to you, you can simply run the provided command, substituting <code>your-user</code> for your server user. In my case, my user is <code>ubuntu</code>. My command would look like this.</p><pre><code class="language-bash">sudo usermod -aG docker ubuntu</code></pre><p>We also need to install Docker Compose. This can be done by running the following commands.</p><pre><code class="language-bash">sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose</code></pre><h3 id="step-3-spinning-up-authelia">Step 3: Spinning up Authelia</h3><p>To get Authelia running, we just need to first clone down the necessary repository.</p><pre><code class="language-bash">cd
git clone https://github.com/authelia/authelia.git
cd authelia/examples/compose/lite
git checkout $(git describe --tags `git rev-list --tags --max-count=1`)</code></pre><p>Cool, you should now be in the context of the <code>lite</code> mode of Authelia. You can see other modes <a href="https://www.authelia.com/docs/deployment/">here</a>, but for this tutorial we'll be using lite.</p><p>If you run <code>ls</code> you'll see that there's already a <code>docker-compose.yml</code> file. We can actually delete/override this file. As the example file contains everything needed for Traefik, rather than Nginx.</p><pre><code class="language-bash">rm docker-compose.yml
nano docker-compose.yml</code></pre><p>Once we're in the nano context, we can paste in the following code.</p><pre><code class="language-yaml">---
version: '3.3'

services:
  authelia:
    image: authelia/authelia
    container_name: authelia
    volumes:
      - ./authelia:/config
    ports:
      - 9091:9091
    restart: unless-stopped
    healthcheck:
      disable: true
    environment:
      - TZ=Europe/London</code></pre><p>Save and exit nano.</p><p>Now, we need to add some configuration.</p><p><code>cd</code> into the <code>authelia</code> folder in your current context.</p><p>You should see <code>configuration.yml</code> and <code>users_database.yml</code></p><p>Let's deal with <code>configuration.yaml</code> first.</p><pre><code>nano configuration.yml</code></pre><p>You'll see that there's already quite a lot in the file. There are some parts that you can change. Feel free to cross-reference my configuration with your own. I'll label the important parts.</p><pre><code>---
###############################################################
#                   Authelia configuration                    #
###############################################################

jwt_secret: something_secret # I used a password generator to generate something strong
default_redirection_url: https://auth.example.co.uk # This will be the domain name that you'll configure to be your Authelia login form.

theme: dark # You're welcome :)

server:
  host: 0.0.0.0
  port: 9091

log:
  level: debug
# This secret can also be set using the env variables AUTHELIA_JWT_SECRET_FILE

totp:
  issuer: authelia.com # Leave this as is.

authentication_backend:
  file:
    path: /config/users_database.yml

access_control:
  default_policy: deny
  rules: # Here we can add in our domains that we want to lock.
    # Rules applied to everyone
    - domain: example.co.uk # This will be public
      policy: bypass
    - domain: git.example.co.uk # This will require a username and password
      policy: one_factor
    - domain: web.example.co.uk # This will require username, password and a 2FA auth
      policy: two_factor

session:
  name: authelia_session
  # This secret can also be set using the env variables AUTHELIA_SESSION_SECRET_FILE
  secret: something_secret # I used a password generator to generate something strong
  expiration: 3600  # 1 hour
  inactivity: 300  # 5 minutes
  domain: example.co.uk  # Should match whatever your root protected domain is

regulation:
  max_retries: 3
  find_time: 120
  ban_time: 300

storage:
  local:
    path: /config/db.sqlite3

notifier: # Rather than configuring SMTP, I'm just writing to a local file
  filesystem:
    filename: /config/notification.txt</code></pre><p>Save and exit nano.</p><p>Next, we want to add a user.</p><pre><code>nano users_database.yml</code></pre><p>You now need to add your user.</p><pre><code>users:
  ed: # Change this to your username
    # Password is authelia
    password: "$6$rounds=50000$BpLnfgDsc2WD8F2q$Zis.ixdg9s/UOJYrs56b5QEZFiZECu0qZVNsIYxBaNJ7ucIL.nlxVCT5tqh8KHG8X4tlwCFm5r6NTOZZ5qRFN/" # Leave the password for now, we'll change it later
    displayname: Ed # Change this
    email: your@email.com # Change this
    groups:
    - admins
    - dev</code></pre><p>Exit nano.</p><p>That's it for initial Authelia configuration, you can now start the <code>docker-compose.yml</code></p><pre><code class="language-bash">docker-compose up -d</code></pre><p>After a few seconds, you can navigate to <code>http://localhost:9091</code> and you should see something that looks like the following!</p><figure class="kg-card kg-image-card"><img src="/images/2021/08/image-1.png" class="kg-image" alt loading="lazy" width="620" height="543" srcset="/images/size/w600/2021/08/image-1.png 600w, /images/2021/08/image-1.png 620w"></figure><h2 id="exposing-authelia-for-web-access">Exposing Authelia for web access</h2><h3 id="step-1-installing-and-configuring-nginx">Step 1: Installing and configuring Nginx</h3><p>Nginx is a reverse proxy that allows us to point a domain name to our Authelia service. By default, Authelia will be running on port 9091. </p><p>First, we need to install Nginx.</p><pre><code class="language-bash">sudo apt-get install nginx</code></pre><p>Once installed, we need to configure a new site. This is essentially a config file that will allow routing on a certain domain name to our Authelia service.</p><p>Usually, I name my sites by their URL. To create a new site, run the following command, substituting my URL with yours.</p><pre><code class="language-bash">sudo nano /etc/nginx/sites-available/auth.theselfhostingblog.com</code></pre><p>This will open up a new window that allows us to add our Nginx configuration. Now, you're about to paste in a lot of text, but don't worry, I'll walk you through what we need to change.</p><p>Paste in the following code.</p><pre><code class="language-bash">upstream authelia {
   server localhost:9091;
}

server {
   listen 80 default_server;

   server_name auth.theselfhostingblog.com; #Change Me

   location / {
       proxy_pass http://authelia;
       include /etc/nginx/authelia/proxy.conf;
   }
}</code></pre><p>Most of this is configured for you and doesn't need to be changed. Only the URL which Nginx wants to use. You'll see that mine is <code>auth.theselfhostingblog.com</code> make sure that you change this to your URL. Also, don't forget the <code>;</code> at the end of the line. It'll save you a lot of headaches 😉</p><p>We now need to hit <code>Ctrl + x</code> ,<code>y</code> and <code>return</code> to save and exit out of <code>nano</code>.</p><p>Now, we just need to register our new site with Nginx, you can do this by running the following command. Again substituting my domain with yours.</p><pre><code class="language-bash">sudo ln -s /etc/nginx/sites-available/auth.theselfhostingblog.com /etc/nginx/sites-enabled/auth.theselfhostingblog.com</code></pre><p>We just need to remove the default Nginx configuration to prevent a port clash.</p><pre><code class="language-bash">sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default</code></pre><p>We now need to add in some configuration files.</p><p>Ensure that you are in the <code>/etc/nginx</code> context.</p><pre><code>cd /etc/nginx</code></pre><p>Create a new directory</p><p></p><p>GOT TO HERE</p><p></p><p></p><p>Now, we can test the Nginx configuration. Run the following command.</p><pre><code class="language-bash">sudo nginx -t</code></pre><p>Which should give us a successful output.</p><figure class="kg-card kg-image-card"><img src="/images/2021/07/image-9.png" class="kg-image" alt loading="lazy" width="1137" height="90" srcset="/images/size/w600/2021/07/image-9.png 600w, /images/size/w1000/2021/07/image-9.png 1000w, /images/2021/07/image-9.png 1137w" sizes="(min-width: 720px) 720px"></figure><p>Sweet. Let's restart the Nginx service.</p><pre><code class="language-bash">sudo systemctl restart nginx</code></pre><h3 id="step-2-port-forwarding">Step 2: Port-forwarding</h3><p>If you're self-hosting this on your own hardware, you will need to port forward your instance to allow public access to your instance. This will involve googling how to port forward from your router.</p><p>You'll need to <a href="https://portforward.com/"><strong>point port 80 and 443 to your instance</strong></a> where Nginx is set up.</p><p>If you're hosting Stringer in Digital Ocean, you may need to configure UFW.</p><p>I have a tutorial for <a href="https://theselfhostingblog.com/posts/setting-up-ufw-on-ubuntu-server/"><strong>setting up UFW here</strong></a></p><pre><code class="language-bash">sudo ufw app list</code></pre><pre><code class="language-bash">Output
---

Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH</code></pre><p>As you can see, there are three profiles available for Nginx:</p><ul><li><strong>Nginx Full</strong>: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)</li><li><strong>Nginx HTTP</strong>: This profile opens only port 80 (normal, unencrypted web traffic)</li><li><strong>Nginx HTTPS</strong>: This profile opens only port 443 (TLS/SSL encrypted traffic)</li></ul><p>It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Since we will be configuring SSL for our server we will need to temporarily allow port 80 and 443 for Certbot to verify our domain endpoint.</p><p>You can enable this by typing:</p><pre><code class="language-bash">sudo ufw allow 'Nginx Full'</code></pre><h3 id="step-3-configuring-certbot">Step 3: Configuring Certbot</h3><p>Certbot allows us to generate SSL certificates for free with Let's Encrypt. It's simple to install and use. Even hooks in with Nginx, meaning that there's no more manual configuration required.</p><p>To install Certbot, simply run the following commands</p><pre><code class="language-bash">sudo apt-get install certbot python3-certbot-nginx</code></pre><p>Then, to set up your SSL certificate, run. Make sure that you have your domain name pointing to your IP address. This will require some DNS configuration. You'll need an A Record.</p><pre><code class="language-bash">sudo certbot --nginx</code></pre><p>Follow the instructions, select your domain name from the Nginx list.<br>Also, select <code>redirect</code> as this will upgrade any HTTP requests to HTTPS.</p><p>Once done, if you navigate to your domain, you should see the following page. </p><figure class="kg-card kg-image-card"><img src="/images/2021/07/image-10.png" class="kg-image" alt loading="lazy" width="521" height="453"></figure><p>You should also notice that the SSL certificate is causing your domain to be HTTPS!</p><h2 id="thats-all">That's all</h2><p>Let me know in the comments if you get stuck along the way, I'll do everything I can to help you out. Hope this post helped you!</p><p></p><p></p>