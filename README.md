# Ansible_EX407
Some notes to help prepare for the EX407 Exam

- [Ansible_EX407](#ansible-ex407)
  * [Introduction](#introduction)
  * [Aims](#aims)
  * [Dependencies](#dependencies)
    + [Packages:](#packages-)
    + [Basic Experience:](#basic-experience-)
    + [Environment:](#environment-)
  * [Overview:](#overview-)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


## Introduction
I'm totally going to power summarize the following site, and create working examples:
- https://linuxbuff.wordpress.com/tag/ex407/

As linuxbuff states every so clearly, I'm <strong>not</strong> providing brain-dumps of any papers, or breaking any NDA's. This is just my attempt at recording my journey whilst trying to complete the exam.

## Aims
Pass the exam, init?

## Dependencies
This is operated from a MAC, running brew as my standard development environment.
I created a specially Vagrantfile years ago, so am kind of stuck on using that to build Centos images, but you could use anything.
The Repo just creates a whole bunch of stuff in Ansible, and is a great source of information on how certain modules work, but it just sets up the Dev environment super quick and easy.

### Packages:
- Virtual box
- Terminal client of choice
- A text editor of choice


### Basic Experience:
- Running vagrant 
- Running Ansible
- Using Python(3.x) and pip 
- Centos basic experience
- Some Vim foo would probably help


### Environment:
- Pyenv to make virtual environments
- Vagrant via https://github.com/asainsbury/vagrant-maker
- 3 Centos Nodes, one of which will become a fresh control node.
- SSH Key access to all nodes (which is handled by my previous Repo).

### How this works?
Each overview section will have its own folder with links, and example playbooks to run. 

#### START HERE [00](00/)
Which says a bit about how to setup your test VM's, then work your way through the steps which are based on the overview set out by RedHat.

## Overview:
More here:
https://www.redhat.com/en/services/training/ex407-red-hat-certified-specialist-in-ansible-automation-exam

1. Understand core components of Ansible
	- Inventories
	- Modules
	- Variables
	- Facts
	- Plays
	- Playbooks
	- Configuration files
2. Install and configure an Ansible control node
	- Install required packages
	- Create a static host inventory file
	- Create a configuration file
3. Configure Ansible managed nodes
	- Create and distribute SSH keys to managed nodes
	- Configure privilege escalation on managed nodes
	- Validate a working configuration using ad-hoc Ansible commands
4. Create simple shell scripts that run ad hoc Ansible commands
5. Use both static and dynamic inventories to define groups of hosts
6. Utilize an existing dynamic inventory script
7. Create Ansible plays and playbooks
	- Know how to work with commonly used Ansible modules
	- Use variables to retrieve the results of running commands
	- Use conditionals to control play execution
	- Configure error handling
	- Create playbooks to
8. Use Ansible modules for system administration tasks that work with:
	- Software packages and repositories
	- Services
	- Firewall rules
	- File systems
	- Storage devices
	- File content
	- Archiving
	- Scheduled tasks
	- Security
	- Users and groups
9. Create and use templates to create customized configuration files
10. Work with Ansible variables and facts
11. Create and work with roles
12. Download roles from an Ansible Galaxy and use them
13. Manage parallelism
14. Use Ansible Vault in playbooks to protect sensitive data
15. Use provided documentation to look up specific information about Ansible modules and commands

## Exam Prep
When I get some Intel on how the exam works, I will update this section.

