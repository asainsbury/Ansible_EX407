# Ansible_EX407
Some notes to help prepare for the EX407 Exam

---

- [Ansible_EX407](#ansible-ex407)
  	- [Introduction](#introduction)
- [Chapters in this repo](#chapters-in-this-repo)
	- [0. Setup Vagrant](#0-setup-vagrant)
    - [1. Core Components](#1-core-components)
    - [99. Further Reading](#99-further-reading)
- [Dependencies](#dependencies)
  	- [Packages](#packages)
  	- [Basic Experience](#basic-experience)
  	- [Environment](#environment)
- [START HERE](#start-here)


## Introduction
My motivation was generated after vising this site, so nod to linuxbuff: [site](https://linuxbuff.wordpress.com/tag/ex407/), I'm going to breakdown the sections of the blueprint and create working examples which can be used to follow along with.


As linuxbuff states every so clearly, I'm <strong>not</strong> providing brain-dumps of any papers, or breaking any NDA's. This is just my attempt at recording my journey whilst trying to complete the exam.

The main aim, is to obviously pass the exam!

<div align="right">
    <b><a href="#top">↥ top</a>    |   <a href="/00_setup_vagrant/">next</a> </b>
</div>
<br/>



# Chapters in this repo
As I slowly make my way through the RedHat blueprint for this module, more chapters will appear with working snippets to run against our sandpit environment.

This is the official blueprint, outlining the topics for the [exam](https://www.redhat.com/en/services/training/ex407-red-hat-certified-specialist-in-ansible-automation-exam).


#### [0. Setup Vagrant](00_setup_vagrant/)
- Start Here! 
- A lot of good examples are held in this section, but this is how we can setup our test env.
- 3 Centos nodes are going to be built using vagrant, but another repo will help with that.


#### [1. Core Components](01_core_components/)
In this section we will be covering the following sections:

- Inventories
- Modules
- Variables
- Facts
- Plays
- Playbooks
- Configuration files
 

#### [99. Further Reading](99_further_reading/)
This is a collection of excellent resources I have stumbled upon whilst writing this up. Obviously the main Ansible site has a lot of information and is a great resource, but check out these blogs for more info.


<div align="right">
    <b><a href="#top">↥ top</a>    |   <a href="/00_setup_vagrant/">next</a> </b>
</div>
<br/>



# Dependencies
This is operated from a MAC, running brew as my standard development environment.
I created a specially Vagrantfile years ago, so am kind of stuck on using that to build Centos images, but you could use anything.
The Repo just creates a whole bunch of stuff in Ansible, and is a great source of information on how certain modules work, but it just sets up the Dev environment super quick and easy.

The major dependency is the willingness to learn!

#### Packages
- Virtual box
- Terminal client of choice
- A text editor of choice


#### Basic Experience
- Running vagrant 
- Running Ansible
- Using Python(3.x) and pip 
- Centos basic experience
- Some Vim foo would probably help


#### Environment
- Pyenv to make virtual environments
- Vagrant via https://github.com/asainsbury/vagrant-maker
- 3 Centos Nodes, one of which will become a fresh control node.
- SSH Key access to all nodes (which is handled by my previous Repo).

<div align="right">
    <b><a href="#top">↥ top</a>    |   <a href="/00_setup_vagrant/">next</a> </b>
</div>
<br/>


# [START HERE](00_setup_vagrant/)
Which says a bit about how to setup your test VM's, then work your way through the steps which are based on the overview set out by RedHat.

<div align="right">
    <b><a href="#top">↥ top</a>    |   <a href="/00_setup_vagrant/">next</a> </b>
</div>
<br/>
