# Part 01 - Understand core components of Ansible

## Introduction

In this section we are going to cover off the following topics:

- [Inventories](#inventories)
- [Modules](#modules)
- [Variables](#variables)
- [Facts](#facts)
- [Plays](#plays)
- [Playbooks](#playbooks)
- [Configuration files](#configuration-files)


# Inventories
Official Information:
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

- Static vs Dynamic inventories are covered in [Section 05](../05/)
- Variables can be located inside inventories.
- Group vars can also be contained inside inventories.


#### Paths Influencing the inventory file
- The default inventory is /etc/ansible/hosts
- Use a custom host file like: ```ansible-playbook -i {path_to_hosts} {playbook_name}```
- Add a path in the ansible.cfg file, like: ```inventory = inventory_for_this_play```
- Put the CFG file in the same path as your playbooks for it to take precedent over the defaults

#### Hosts and Groups
Ansible needs a host file, like a duck needs water, and it contains groups, and host members. As usual the inventory file is very flexible, and can contain child groups.

This [example](hosts) can be used with our test environment.
```bash
---
# This group is for any plays you might want to run on the Ansible control node
[local]
localhost ansible_connection=local

# This group lists out all the nodes together
[dev]
dev1 ansible_ssh_host=1.1.1.10
dev2 ansible_ssh_host=1.1.1.11
dev3 ansible_ssh_host=1.1.1.12

# We can split out our control node into a separate group, so we can target it specifically
[control] 
dev1 ansible_ssh_host=1.1.1.10

# We can also make a sub group, just for the other 2 nodes, which are more for test related
[test_nodes]
dev2 ansible_ssh_host=1.1.1.11
dev3 ansible_ssh_host=1.1.1.12

# This is part of the Ansible magic, which is really powerful, but can take some time to get your head around it.
# We build a new group name, with the children of the other sub groups:
[dev_group:children]
control
test_nodes
```

This playbook [show_members.yml](show_members.yml), uses a special variable called "group_names". There are a heap of different special variables which are backed into Ansible, and we will list them out later in this section. Here we call this var in a debug message to show the group names.
```yaml
---
- hosts: all
  gather_facts: no
  tasks:
  - name: show the groups the host(s) are in
    debug:
      msg: "{{group_names}}"
```


Can be run like this: 

```bash
ansible-playbook -i hosts show_members.yml
```

And produces the following output, showing the group inheritance:

```bash
PLAY [all] *************************************************************************************************************************************************************************************************

TASK [show the groups the host(s) are in] ******************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "local"
    ]
}
ok: [dev2] => {
    "msg": [
        "dev",
        "dev_group",
        "test_nodes"
    ]
}
ok: [dev3] => {
    "msg": [
        "dev",
        "dev_group",
        "test_nodes"
    ]
}
ok: [dev1] => {
    "msg": [
        "control",
        "dev",
        "dev_group"
    ]
}

PLAY RECAP *************************************************************************************************************************************************************************************************
dev1                       : ok=1    changed=0    unreachable=0    failed=0
dev2                       : ok=1    changed=0    unreachable=0    failed=0
dev3                       : ok=1    changed=0    unreachable=0    failed=0
localhost                  : ok=1    changed=0    unreachable=0    failed=0
```

Run this command to show the hosts which will run in the playbook:

```bash
ansible-playbook -i hosts show_members.yml --list-hosts
```

Which nicely shows you the following:

```bash
playbook: show_members.yml

  play #1 (all): all	TAGS: []
    pattern: ['all']
    hosts (4):
      dev3
      dev2
      dev1
      localhost
```

#### Other Inventory Tricks - Common Variables
You can do a lot with an inventory file, here you can set out lots of control variables.

##### Host and Group Variables

- You can put variables in this file, and I do it all the time.
- Best practices are to place variable into group_vars or host_vars.
- You can use the children inheritance to nest vars

#### ansible_network_os
 This is required when you are using one of the network modules, so Ansible can work out which platform you are working on. Note that F5 isn't on the [list](https://github.com/ansible/ansible/blob/devel/docs/docsite/rst/network/user_guide/platform_index.rst)!

#### Stacking Inventories
You can actually reference multiple inventory file, which is neat:

```bash
ansible-playbook run_me.yml -i dev -i prod
```

#### [The Rest](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters)
There are a lot of items which can be listed in the inventory, which can influence how to connect to a host.

- ansible_ssh_host: list the ip address, when you can't trust DNS.
- ansible_connection: Smart, SSH or Paramiko.
- ansible_port: Non standard port for SSH ie 2222.
- ansible_user: The user which Ansible has to use to access that host.
- ansible_password. The password which Ansible has to use to access that host.


<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>



# Modules


<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>


# Variables
Simplistically put, variables are just ways of passing stored information within a program. We can centrally managed a bit of data, which is then referenced in a playbook, like the version of an operating system or the name of a host.


We can put variables in what appears infinite locations within Ansible, and we have an order of precedence on which location takes place.

- The official word on the street is [here](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
- Python overview of what a variable is [here](https://www.w3schools.com/python/python_variables.asp)

#### Ordering
Did you know there are 22 places where you can place a variable, not quite infinite but that is a lot places to loose yourself in. More info found [here](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

#### Importing a File
This is a neat way of including some files which might sit outside of your repo.
```bash
  vars_files:
    - some_vars.yml
```

#### Special Variables
[here](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html#special-variables)

#### Listing Variables

#### Group Vars

#### Host Vars

#### Inventories

#### Roles

#### Playbooks

#### Extra Vars On The CLI



<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>


# Facts

<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>


# Plays

<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>


# Playbooks

<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>


# Configuration files

<div align="right">
    <b><a href="#top">↥ top</a>  |   <a href="../02_make_install_ansible">next</a> </b>
</div>
<br/>




# Next Section [02](../02_make_install_ansible/)
OK, we've mastered the fundamentals, time to move on!
