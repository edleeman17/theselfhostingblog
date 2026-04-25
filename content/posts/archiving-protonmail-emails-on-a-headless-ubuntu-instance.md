---
author: ed
title: 'Archiving ProtonMail Emails on a headless Ubuntu instance'
date: 2020-11-16
description: 'I wanted to be able to store all of my ProtonMail emails locally for archival purposes, with the intention of running a local content search whenever I needed something retrieving.'
cover:
  image: 'https://images.unsplash.com/photo-1590247813693-5541d1c609fd?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ'
  relative: false
tag:
  - 'Guides'
---

<h2 id="introduction">Introduction</h2><p>I wanted to be able to store all of my <a href="https://protonmail.com/">ProtonMail</a> emails locally for archival purposes, with the intention of running a local content search whenever I needed something retrieving.</p><figure class="kg-card kg-bookmark-card"><a class="kg-bookmark-container" href="https://protonmail.com/"><div class="kg-bookmark-content"><div class="kg-bookmark-title">Secure email: ProtonMail is free encrypted email.</div><div class="kg-bookmark-description">ProtonMail is the world’s largest secure email service, developed by CERN and MIT scientists. We are open source and protected by Swiss privacy law</div><div class="kg-bookmark-metadata"><img class="kg-bookmark-icon" src="https://protonmail.com/apple-touch-icon.png"><span class="kg-bookmark-author">ProtonMail</span></div></div><div class="kg-bookmark-thumbnail"><img src="https://protonmail.com/images/facebook_logo.jpg"></div></a></figure><p>I'm going to talk through my steps. There's plenty of tutorials out there for email services such as; Gmail, Hotmail etc. But not much around ProtonMail, especially using a headless instance.</p><h2 id="protonmail-bridge">ProtonMail bridge</h2><p><a href="https://protonmail.com/bridge/">ProtonMail Bridge</a> is an open-source piece of software built by the Proton Team to create a dummy IMAP server locally, with a sole purpose of decrypting your emails locally to enable you to use a desktop email client such as <a href="https://www.thunderbird.net/en-US/">ThunderBird</a>. </p><figure class="kg-card kg-bookmark-card"><a class="kg-bookmark-container" href="https://protonmail.com/bridge/"><div class="kg-bookmark-content"><div class="kg-bookmark-title">Configure ProtonMail secure email with your favorite IMAP/SMTP email client by using the ProtonMail Bridge application. IMAP instructions here.</div><div class="kg-bookmark-description">Configure ProtonMail secure email with your favorite IMAP/SMTP email client by using the ProtonMail Bridge application. IMAP instructions here.</div><div class="kg-bookmark-metadata"><img class="kg-bookmark-icon" src="https://protonmail.com/images/favicon.ico"><span class="kg-bookmark-author">ProtonMail</span></div></div><div class="kg-bookmark-thumbnail"><img src="https://protonmail.com/images/facebook_logo.jpg"></div></a></figure><p>ProtonMail Bridge is a paid feature though, with a minimum requirement of the <a href="https://protonmail.com/pricing">ProtonMail Plus</a> plan ($5/month). ProtonMail Bridge is not available on the ProtonMail Free plan.</p><h2 id="overview">Overview</h2><p>We're going to set up  a Cron job on a headless Ubuntu instance to periodically pull down all our emails. This instance will be running ProtonMail Bridge and a tool named <a href="http://www.offlineimap.org/">Offlineimap</a>, which will be used to store all your emails in a plaintext, searchable format. </p><figure class="kg-card kg-bookmark-card"><a class="kg-bookmark-container" href="http://www.offlineimap.org/"><div class="kg-bookmark-content"><div class="kg-bookmark-title">OfflineIMAP community’s website</div><div class="kg-bookmark-description">OfflineIMAP community’s website.</div><div class="kg-bookmark-metadata"></div></div><div class="kg-bookmark-thumbnail"><img src="http://www.offlineimap.org/assets/logo/offlineimap-scalable.svg"></div></a></figure><h2 id="getting-started">Getting Started</h2><h3 id="step-1-setting-up-your-linux-server">Step 1: Setting up your Linux server</h3><p>You'll need to either have an existing server instance or create one. I use a Proxmox instance running on a server in my loft. You could also use something like <a href="https://www.digitalocean.com/">Digital Ocean</a> to run your local archive. Using the following link will give you $100 worth of credits for 60 days to play around with, just sign up using <a href="https://m.do.co/c/d2a3afe52625">this link</a>.</p><p>You could also use a cheap <a href="https://amzn.to/3cWTlno">Raspberry PI</a> to set up your own Linux server.</p><p>Once you have the server set up, or have logged in. You'll need to do some updates and run some prerequisite installs.</p><pre><code class="language-bash">sudo apt-get update
sudo apt-get upgrade</code></pre><h3 id="step-1-gpg-and-pass">Step 1: gpg and pass</h3><p>To use ProtonMail Bridge, you need to first set up the dependencies. These dependencies are <code>pass</code> (A password management system), which has a dependency of <code>gpg</code> (A key management system).</p><h4 id="installing-gpg">Installing GPG</h4><p>GPG should be installed on your system as part of the <code>update and upgrade</code> commands that you initially ran after provisioning your server. If it's not, you can install GPG by running the following command.</p><pre><code class="language-bash">sudo apt-get install gpg</code></pre><p>Once GPG is installed, you need to generate a GPG key.</p><pre><code class="language-bash">gpg --gen-key</code></pre><p>This will run you through a wizard of adding your full name and email address. Also requiring you to create a passphrase for your gpg key. Add a password to satisfy the wizard, but we'll have to remove this later.</p><p>Unfortunately, ProtonMail Bridge will not work with a password protected GPG key in headless mode. This is a serious security risk, so make up your own opinion on this, do some research on what implications this has.</p><p>Once GPG has finished, you'll have a public key output. It'll look something like the following</p><pre><code>7BDD29402175BC627671356BE8AC4A1C3C5J6357</code></pre><p>We now need to remove the passphrase associated with this key.</p><p>Run the following</p><pre><code class="language-bash">gpg --edit-key 7BDD29402175BC627671356BE8AC4A1C3C5J6357</code></pre><p>Once you have executed that command, you'll be in the context of gpg</p><p>Run then next command</p><pre><code class="language-bash">passwd</code></pre><p>You'll now need to enter the passphrase you created earlier, which will unlock the GPG key.</p><p>There will now be another console prompt to enter a new passphrase.</p><p>Leave this blank.</p><p>You'll be asked to confirm that you want a blank passphrase, it'll warn you about the security implications.</p><p>Hit 'Y' or Yes to confirm this implication.</p><p>Finally, quit the GPG context by typing <code>q</code> and hitting Enter.</p><h4 id="installing-pass">Installing Pass</h4><p>Now it's time to install Pass, which is a ProtonMail Bridge dependency.</p><pre><code class="language-bash">sudo apt-get install pass</code></pre><p>We'll then need to initialise a new <code>pass</code> instance with your previously generated gpg key id.</p><pre><code class="language-bash">pass init 7BDD29402175BC627671356BE8AC4A1C3C5J6357</code></pre><p>That's, all the setup required for initialising <code>pass</code>.</p><h3 id="step-2-installing-protonmail-bridge">Step 2: Installing ProtonMail Bridge</h3><p>Now it's time to install ProtonMail Bridge.</p><p>Run the following command to install the latest version of ProtonMail Bridge at time of writing this post.</p><pre><code class="language-bash">wget https://protonmail.com/download/beta/protonmail-bridge_1.5.0-1_amd64.deb</code></pre><p>Then install</p><pre><code class="language-bash">dpkg -i protonmail-bridge_1.5.0-1_amd64.deb</code></pre><p>There may be some more missing dependencies when installing. To fix this, run the following</p><pre><code class="language-bash">sudo apt --fix-broken install</code></pre><h3 id="step-3-initialising-protonmail-bridge">Step 3: Initialising ProtonMail Bridge</h3><p>We now need to log in to ProtonMail Bridge, to do this, run the following command</p><pre><code class="language-bash">protonmail-bridge --cli</code></pre><p>ProtonMail Bridge will start up and ask you to log in with an email and a password.</p><p>This will be your standard ProtonMail username and password.</p><h3 id="step-4-getting-your-protonmail-imap-username-and-password">Step 4: Getting your ProtonMail IMAP Username and Password</h3><p>Whilst in the context of the ProtonMail Bridge session, run the following command.</p><pre><code class="language-bash">info</code></pre><p>You'll see an output like the following.</p><pre><code>            Welcome to ProtonMail Bridge interactive shell
                              ___....___
    ^^                __..-:'':__:..:__:'':-..__
                  _.-:__:.-:'':  :  :  :'':-.:__:-._
                .':.-:  :  :  :  :  :  :  :  :  :._:'.
             _ :.':  :  :  :  :  :  :  :  :  :  :  :'.: _
            [ ]:  :  :  :  :  :  :  :  :  :  :  :  :  :[ ]
            [ ]:  :  :  :  :  :  :  :  :  :  :  :  :  :[ ]
   :::::::::[ ]:__:__:__:__:__:__:__:__:__:__:__:__:__:[ ]:::::::::::
   !!!!!!!!![ ]!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!![ ]!!!!!!!!!!!
   ^^^^^^^^^[ ]^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^[ ]^^^^^^^^^^^
            [ ]                                        [ ]
            [ ]                                        [ ]
      jgs   [ ]                                        [ ]
    ~~^_~^~/   \~^-~^~ _~^-~_^~-^~_^~~-^~_~^~-~_~-^~_^/   \~^ ~~_ ^
&gt;&gt;&gt; info
Configuration for {your email}
IMAP Settings
Address:   127.0.0.1
IMAP port: 1143
Username:  {your email}
Password:  {your password}
Security:  STARTTLS

SMTP Settings
Address:   127.0.0.1
IMAP port: 1025
Username:  {your email}
Password:  {your password}
Security:  STARTTLS</code></pre><p>Take a note of the values <code>{your email}</code> and <code>{your password}</code>. You'll need these later.</p><h3 id="step-5-setting-up-offlineimap">Step 5: Setting up Offlineimap</h3><p>I'm going to be using an open-source tool I found on Github. Written by <code><a href="https://github.com/peterrus/protonmail-export-linux">peterrus</a></code>.</p><figure class="kg-card kg-bookmark-card"><a class="kg-bookmark-container" href="https://github.com/peterrus/protonmail-export-linux"><div class="kg-bookmark-content"><div class="kg-bookmark-title">peterrus/protonmail-export-linux</div><div class="kg-bookmark-description">Simple example on how you can incrementally export (backup) all your email from Protonmail’s servers to a local Maildir archive. - peterrus/protonmail-export-linux</div><div class="kg-bookmark-metadata"><img class="kg-bookmark-icon" src="https://github.githubassets.com/favicons/favicon.svg"><span class="kg-bookmark-author">GitHub</span><span class="kg-bookmark-publisher">peterrus</span></div></div><div class="kg-bookmark-thumbnail"><img src="https://avatars.githubusercontent.com/u/1117858?s&#x3D;400&amp;v&#x3D;4"></div></a></figure><p>Run the following command to pull down the repo from Github.</p><pre><code class="language-bash">git clone https://github.com/peterrus/protonmail-export-linux.git</code></pre><p>The <code>cd</code> into the cloned repo</p><pre><code class="language-bash">cd protonmail-export-linux</code></pre><p>Next, you need to install <code>offlineimap</code></p><pre><code class="language-bash">sudo apt-get install offlineimap</code></pre><p>Once installed, in the <code>protonmail-export-linux</code> directory, there will be a <code>offlineimaprc</code> file. Open this with your favorite text editor. I'll be using <code>vim</code> in this example.</p><pre><code class="language-bash">vim offlineimparc</code></pre><p>You'll see a file open that looks something like the following</p><pre><code class="language-plaintext"># Feel free to modify this file to your needs
# See https://github.com/OfflineIMAP/offlineimap/blob/master/offlineimap.conf for a full reference

[general]
accounts = Protonmail
metadata = ./offlineimap-metadata

[Account Protonmail]
localrepository = ProtonmailLocal
remoterepository = ProtonmailRemote

# Feel free to change this, or disable it by commenting it out
postsynchook = notify-send 'Protonmail export done'

[Repository ProtonmailLocal]
type = Maildir
localfolders = ./protonmail-export
# If you (accidentally) delete mail locally, it won't get synced to Protonmail
sync_deletes = no

[Repository ProtonmailRemote]
type = IMAP
# Change this to the value provided in the Protonmail bridge
remoteuser = {your email}
remotepass = {your password}
remotehost = localhost
remoteport = 1143
ssl = no
starttls = no
# If you delete mail on Protonmail, this deletion also get's synced to the archive
expunge = yes
# Don't try to sync local changes to Protonmail, we just want a backup
readonly = True</code></pre><p>Leave everything how it is, apart from the section where I have specified, <code>{your email}</code> and <code>{your password}</code>. These will need to be replaced with your ProtonMail Bridge email and password that I got you to take note of earlier.</p><p>Save your changes and exit your text editor.</p><h3 id="step-6-setting-up-screen">Step 6: Setting up screen</h3><p>I use screen to run services in the background. Both <code>protonmail-bridge</code> and <code>offlineimap</code> need to run simultaneously.</p><pre><code class="language-bash">sudo apt-get install screen</code></pre><h3 id="step-7-creating-a-startup-script">Step 7: Creating a startup script</h3><p>I made it easy for myself and created a startup script to execute both <code>protonmail-bridge</code> and <code>offlineimap</code></p><p>Here's my script, simply create a new file named <code>start-sync.sh</code> in your home directory. Giving it execute permissions by running</p><pre><code class="language-bash">sudo chmod +x start-sync.sh</code></pre><p>The <code>start-sync.sh</code> file contents should look like this. </p><pre><code>#! /bin/bash
echo "killing existing screen sessions"
pkill screen
echo "Starting bridge"
screen -d -m protonmail-bridge --cli
echo "Starting sync"
sleep 10
screen -d -m offlineimap -c /home/{your user}/protonmail-export-linux/offlineimaprc</code></pre><p>Change the location of your <code>offlineimaprc</code> file in that shell script.</p><h3 id="step-8-starting-offlineimap">Step 8: Starting Offlineimap</h3><p>Now it's time to execute your <code>start-sync.sh</code></p><pre><code>./start-sync.sh</code></pre><p>Now, both of your services should have started up. To see them, run the following command</p><pre><code class="language-bash">screen -r</code></pre><p>You'll see an output like the following</p><pre><code>ed@mail:~# screen -r
There are several suitable screens on:
        6415..mail      (12/05/20 18:06:26)     (Detached)
        6308..mail      (12/05/20 18:06:16)     (Detached)
Type "screen [-d] -r [pid.]tty.host" to resume one of them.
ed@mail:~#</code></pre><p>Then run</p><pre><code class="language-bash">screen -r 6415 // 6415 being the id of the top process on the screen -r output</code></pre><p>You should see an output of a successful authentication with ProtonMail Bridge, and a list of emails currently being downloaded.</p><pre><code>Copy message UID 2081 (23/3445) ProtonmailRemote:All Mail -&gt; ProtonmailLocal
Folder INBOX [acc: Protonmail]:
etc...</code></pre><p>Detach from this screen by hitting <code>Ctrl + a</code> and <code>Ctrl + d</code>.</p><h3 id="step-9-where-are-my-emails">Step 9: Where are my emails?</h3><p>Whilst your emails are downloading, they will be saved to <code>~/protonmail-export</code>. You should see a list of your email folders in this directory, under the <code>cur</code> subdirectory.</p><h3 id="step-10-setting-up-a-cron-job-to-pull-new-emails">Step 10: Setting up a cron job to pull new emails</h3><p>Now it's time to set this job up to run each day, or each hour, whatever you'd like to pick.</p><p>Run the following</p><pre><code>sudo crontab -e</code></pre><p>At the very bottom of your <code>crontab</code> file, paste in the following command.</p><pre><code>0 0 * * * /home/{your user}/start-sync.sh</code></pre><p>I use a tool called <a href="https://crontab.guru/">crontab.guru</a> to assist with picking a schedule to run my command.</p><p>My command will run at midnight each day.</p><hr><h3 id="that-s-all">That's all</h3><p>And hopefully, everything should be up and running successfully. Let me know if you receive any issues in the comments, I'll be happy to help you out.</p><p>You could now sync all your emails to Dropbox, or something like Syncthing, which <a href="https://theselfhostingblog.com/posts/how-to-set-up-a-headless-syncthing-network/">I have recently written about here</a>.</p><p></p>