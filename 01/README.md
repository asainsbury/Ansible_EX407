# Part 01 - Understand core components of Ansible
A summary of each of the following parts.

Insert TOC

### Inventories
Official Information:
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

#### Hosts and Groups
Ansible needs a host file, like a duck needs water

This [example](hosts) can be used with our test environment
```bash
[local]
localhost ansible_connection=local

[dev]
dev1 ansible_ssh_host=1.1.1.10
dev2 ansible_ssh_host=1.1.1.11
dev3 ansible_ssh_host=1.1.1.12

[control]
dev1 ansible_ssh_host=1.1.1.10

[test_nodes]
dev2 ansible_ssh_host=1.1.1.11
dev3 ansible_ssh_host=1.1.1.12

[dev_group:children]
control
test_nodes
```


### Modules
### Variables
### Facts
### Plays
### Playbooks
### Configuration files
