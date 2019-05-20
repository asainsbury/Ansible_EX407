# Part 01 - Understand core components of Ansible
A summary of each of the following parts.

Insert TOC

### Inventories
Official Information:
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

- Static vs Dynamic inventories are covered in [Section 05](../05/)
- Variables can be located inside inventories.
- Group vars can also be contained inside inventories.

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../../../">home</a>   |   <a href="../02">next</a> </b>
</div>
<br/>


#### Hosts and Groups
Ansible needs a host file, like a duck needs water, and it contains groups, and host members. As usual the inventory file is very flexible, and can contain child groups.

This [example](hosts) can be used with our test environment.
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

This playbook [show_members.yml](show_members.yml): 
```yaml
---
- hosts: all
  gather_facts: no
  tasks:
  - name: show the groups the host(s) are in
    debug:
      msg: "{{group_names}}"
```


Can be run like this: ```ansible-playbook -i hosts show_members.yml```

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
<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../">home</a>   |   <a href="../02">next</a> </b>
</div>
<br/>

#### Paths
- The default inventory is /etc/ansible/hosts
- Use a custom host file like: ```ansible-playbook -i {path_to_hosts} {playbook_name}```
- Add a path in the ansible.cfg file, like: ```inventory = inventory_for_this_play```
- Put the CFG file in the same path as your playbooks for it to take precedent over the defaults

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../../../">home</a>   |   <a href="../02">next</a> </b>
</div>
<br/>

### Modules
### Variables
### Facts
### Plays
### Playbooks
### Configuration files

# Next Section [02](../02/)
OK, we've mastered the fundamentals, time to move on!
