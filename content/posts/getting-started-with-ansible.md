---
author: ed
title: 'Getting started with Ansible'
date: 2021-09-18
draft: true
description: 'As you may have seen from my last post, which was admittedly some time ago! I run a small Raspberry Pi cluster as my home lab. Having a cluster has its benefits, however, when I write my posts I often'
cover:
  image: '/images/2021/09/alina-grubnyak-ZiQkhI7417A-unsplash.jpg'
  relative: false
---

<p>As you may have seen from my last post, which was admittedly some time ago! I run a small Raspberry Pi cluster as my home lab. Having a cluster has its benefits, however, when I write my posts I often completely wipe the OS on all hosts and start from scratch to ensure that the steps are all working. As you can imagine this becomes quite a chore after some time when you have multiple hosts to work with. For this reason, I started automating with <a href="https://www.ansible.com/" rel="noopener noreferrer">Ansible</a>. It's been a huge benefit to me, so in this post, I wanted to go over some of the basics, document my findings and also give out some useful small tasks. As always, if there's anything I've missed that you think should be here please don't be afraid to reach out in the comments, on our Discord, or through Reddit. So without further ado, let's get started!</p><h2 id="what-is-ansible">What is Ansible?</h2><p>Ansible, at its core, is an automation tool. From their homepage:</p><blockquote>Ansible is a radically simple IT automation engine that automates cloud provisioning, configuration management, application deployment, intra-service orchestration, and many other IT needs.</blockquote>