---
title: 'My Self Hosted Note Syncing Journey Once Switching to Iphone'
date: 2020-02-25
description: 'Recently moving to the iPhone meant that I had to re-think and re-design my existing note-taking solution, this is a quick writeup of my journey.'
cover:
  image: 'https://images.unsplash.com/photo-1483546416237-76fd26bbcdd1?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ'
tags:
  - 'Ramblings'
---

<p>Recently moving to the iPhone meant that I had to re-think and re-design my existing note-taking solution, this is a quick writeup of my journey.</p>
<h2 id="myexistingsolutionwithsyncthingandvscode">My existing solution with Syncthing and VS Code</h2>
<p>I <strong>loved</strong> my original solution. I had recently discovered <a href="https://syncthing.net/">Syncthing</a> and got it set up on my self hosted solution in my loft. I had a central sync server and then a node on each of my devices, including my Huawei P20 Pro.</p>
<p>I could keep my extensive markdown nodes on every device and sync them without even thinking about it. It just worked, I wasn't tied into a service as the nodes are written in Markdown.</p>
<p>Everything just <strong>worked</strong>.</p>
<h2 id="switchingtoiphone">Switching to iPhone</h2>
<p>I made the switch to the iPhone due to privacy concerns with Android. I have recently boycotted all my Google services and switched over to a self-hosted solution. Part of this meant getting rid of my Android.</p>
<p>Everything started great with the iPhone, the interface is great, everything just works.</p>
<p>Apart from Syncthing.</p>
<p>There's currently no iOS client for Syncthing.</p>
<p>So what now? I started looking at other app alternatives, Bear, Markdown Notes, etc.. but all of them locked down where the files were saved on the iPhone, I couldn't access them from a file explorer on the iPhone. Something that could easily be done with Android.</p>
<p>Bumped into <a href="https://www.resilio.com/">Resilio</a>, not self-hosted, but achieves the whole decentralized syncing that I loved about Syncthing. Cool, I managed to get my notes syncing to a Resilio folder on my iPhone. Bingo!</p>
<p>But wait, what happens when I try and edit a note? It opens a <em>copy</em> of the note in a markdown app. Not ideal, I just want to view and edit notes, how hard can it be!</p>
<p>I give up.</p>
<h2 id="standardnotes">Standard Notes</h2>
<p>I <strong>had</strong> a love-hate relationship with <a href="https://standardnotes.org/">Standard Notes</a>. I loved the UI, the syncing was pretty good, it has an iOS app! and it can be self-hosted, amazing.</p>
<p>But a lot of the features are paid. So if I wanted to write in Markdown, which I do, I had to pay for the extensions. I had to think to myself, how badly do I want notes on my iPhone?</p>
<p>I went without notes for a week, it nearly killed me. So I bit the bullet and settled for one month at £9.99, I thought I'd give it a try.</p>
<p>Loved it, I heavily use Markdown Basic, Simple Task Editor and Secure Spreadsheets extensions. But can I justify £10 a month?</p>
<h2 id="standardnotesextensions">Standard Notes Extensions</h2>
<p>Standard Notes being open-source meant that I could have a poke around in its git repository. I did a global search on Github for &quot;Standard Notes&quot; and I bumped into a repository by Github User <a href="https://github.com/jonhadfield/awesome-standard-notes">jonhadfield</a> who has collected some awesome Standard Notes extensions. Browsing his <code>README.md</code> I spotted the repository <a href="https://github.com/iganeshk/standardnotes-extensions">Extensions Repository Builder</a>. This would allow me to self-host my extensions rather than paying the monthly fee!</p>
<p>I set up Extensions Repository Builder on my internal host and bingo, it just worked, amazing.</p>
<h2 id="fulle2esync">Full E2E Sync</h2>
<p>So after a very long journey of pulling my hair out and re-evaluating how much these notes meant to me. I finally had an end-to-end solution for storing my notes and syncthing between all of my devices.</p>
<h2 id="whereimgoingnext">Where I'm going next</h2>
<p>I have a <a href="https://theptrk.com/2018/07/11/did-txt-file/">did.txt</a> file which I log to every day with what I did that day, it helps with our daily standups so that I can remember what I did the previous day. On <a href="https://github.com/jonhadfield/awesome-standard-notes">jonhadfield's</a> Github, there was also a link for <a href="https://github.com/tannercollin/standardnotes-fs">standardnotes-fs</a> which allows you to mount your Standard Notes as files using fuse. This means I can set up my did.txt command to edit my Did note.</p>
<p>I'd also like to hook up iOS reminders to a reminders section in Standard Notes. I own an Apple Watch and I tend to use Siri on it quite often. So I need to work out some way to sync these with my standard notes.</p>