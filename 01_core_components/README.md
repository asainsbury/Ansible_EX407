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
Official documents [here](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html) and a list of Network specific modules [here](https://docs.ansible.com/ansible/latest/modules/list_of_network_modules.html)

If there are infinite places to put a variable (see next section) there are a gazillion modules to reference from. 

This might be a good spot to go read what idempotent [means](https://shadow-soft.com/ansible-idempotency-configuration-drift/), and you millage will vary with how that gets implemented within each module.

Basically a module will run specific functions relating to a task, like copy files from location A to location B.

#### ansible-doc -h
Use the web links, above for the HTML version (or Google) or...

```bash
ansible-doc --h
Usage: ansible-doc [-l|-F|-s] [options] [-t <plugin type> ] [plugin]

plugin documentation tool

Options:
  -a, --all             **For internal testing only** Show documentation for
                        all plugins.
  -h, --help            show this help message and exit
  -j, --json            **For internal testing only** Dump json metadata for
                        all plugins.
  -l, --list            List available plugins
  -F, --list_files      Show plugin names and their source files without
                        summaries (implies --list)
  -M MODULE_PATH, --module-path=MODULE_PATH
                        prepend colon-separated path(s) to module library (def
                        ault=['/Users/asainsbury/.ansible/plugins/modules',
                        '/usr/share/ansible/plugins/modules'])
  -s, --snippet         Show playbook snippet for specified plugin(s)
  -t TYPE, --type=TYPE  Choose which plugin type (defaults to "module")
  -v, --verbose         verbose mode (-vvv for more, -vvvv to enable
                        connection debugging)
  --version             show program's version number and exit

See man pages for Ansible CLI options or website for tutorials
https://docs.ansible.com
```

This is an absolute gem, built into the version of Ansible you are running on your control system. Google is great for finding out information, but most times, you need to pin that to a specific release to get the right answer.

It's like a Linux [MAN](https://en.wikipedia.org/wiki/Man_page) page.

Ansible-docs even give you the examples to work from.  This is one thing Ansible get right.

#### Ansible-doc -s 
This is a snippet, and shows a more condensed output.
```bash
ansible-doc -s file
- name: Sets attributes of files
  file:
      access_time:           # This parameter indicates the time the file's access time should be set to Should be `preserve' when no modification is required, `YYYYMMDDHHMM.SS' when using default time
                               format, or `now' Default is None meaning that `preserve' is the default for `state=[file,directory,link,hard]' and `now' is default for
                               `state=touch'
      access_time_format:    # When used with `access_time', indicates the time format that must be used. Based on default Python format (see time.strftime doc)
      attributes:            # Attributes the file or directory should have. To get supported flags look at the man page for `chattr' on the target system. This string should contain the attributes in
                               the same order as the one displayed by `lsattr'. `=' operator is assumed as default, otherwise `+' or `-' operators need to be included in
                               the string.
      follow:                # This flag indicates that filesystem links, if they exist, should be followed. Previous to Ansible 2.5, this was `no' by default.
      force:                 # force the creation of the symlinks in two cases: the source file does not exist (but will appear later); the destination exists and is a file (so, we need to unlink the
                               "path" file and create symlink to the "src" file in place of it).
      group:                 # Name of the group that should own the file/directory, as would be fed to `chown'.
      mode:                  # Mode the file or directory should be. For those used to `/usr/bin/chmod' remember that modes are actually octal numbers. You must either add a leading zero so that
                               Ansible's YAML parser knows it is an octal number (like `0644' or `01777') or quote it (like `'644'' or `'1777'') so Ansible receives a
                               string and can do its own conversion from string into number.  Giving Ansible a number without following one of these rules will end up with
                               a decimal number which will have unexpected results. As of version 1.8, the mode may be specified as a symbolic mode (for example, `u+rwx' or
                               `u=rw,g=r,o=r').
      modification_time:     # This parameter indicates the time the file's modification time should be set to Should be `preserve' when no modification is required, `YYYYMMDDHHMM.SS' when using default
                               time format, or `now' Default is None meaning that `preserve' is the default for `state=[file,directory,link,hard]' and `now' is default for
                               `state=touch'
      modification_time_format:   # When used with `modification_time', indicates the time format that must be used. Based on default Python format (see time.strftime doc)
      owner:                 # Name of the user that should own the file/directory, as would be fed to `chown'.
      path:                  # (required) Path to the file being managed.
      recurse:               # recursively set the specified file attributes (applies only to directories)
      selevel:               # Level part of the SELinux file context. This is the MLS/MCS attribute, sometimes known as the `range'. `_default' feature works as for `seuser'.
      serole:                # Role part of SELinux file context, `_default' feature works as for `seuser'.
      setype:                # Type part of SELinux file context, `_default' feature works as for `seuser'.
      seuser:                # User part of SELinux file context. Will default to system policy, if applicable. If set to `_default', it will use the `user' portion of the policy if available.
      src:                   # path of the file to link to (applies only to `state=link' and `state=hard'). Will accept absolute, relative and nonexisting paths. Relative paths are relative to the file
                               being created (`path') which is how the UNIX command `ln -s SRC DEST' treats relative paths.
      state:                 # If `directory', all intermediate subdirectories will be created if they do not exist. Since Ansible 1.7 they will be created with the supplied permissions. If `file', the
                               file will NOT be created if it does not exist; see the `touch' value or the [copy] or [template] module if you want that behavior.  If
                               `link', the symbolic link will be created or changed. Use `hard' for hardlinks. If `absent', directories will be recursively deleted, and
                               files or symlinks will be unlinked. Note that `absent' will not cause `file' to fail if the `path' does not exist as the state did not
                               change. If `touch' (new in 1.4), an empty file will be created if the `path' does not exist, while an existing file or directory will receive
                               updated file access and modification times (similar to the way `touch` works from the command line).
      unsafe_writes:         # By default this module uses atomic operations to prevent data corruption or inconsistent reads from the target files, but sometimes systems are configured or just broken in
                               ways that prevent this. One example is docker mounted files, which cannot be updated atomically from inside the container and can only be
                               written in an unsafe manner. This option allows Ansible to fall back to unsafe methods of updating files when atomic operations fail
                               (however, it doesn't force Ansible to perform unsafe writes). IMPORTANT! Unsafe writes are subject to race conditions and can lead to data
                               corruption.
...skipping...
- name: Sets attributes of files
  file:
      access_time:           # This parameter indicates the time the file's access time should be set to Should be `preserve' when no modification is required, `YYYYMMDDHHMM.SS' when using default time
                               format, or `now' Default is None meaning that `preserve' is the default for `state=[file,directory,link,hard]' and `now' is default for
                               `state=touch'
      access_time_format:    # When used with `access_time', indicates the time format that must be used. Based on default Python format (see time.strftime doc)
      attributes:            # Attributes the file or directory should have. To get supported flags look at the man page for `chattr' on the target system. This string should contain the attributes in
                               the same order as the one displayed by `lsattr'. `=' operator is assumed as default, otherwise `+' or `-' operators need to be included in
                               the string.
      follow:                # This flag indicates that filesystem links, if they exist, should be followed. Previous to Ansible 2.5, this was `no' by default.
      force:                 # force the creation of the symlinks in two cases: the source file does not exist (but will appear later); the destination exists and is a file (so, we need to unlink the
                               "path" file and create symlink to the "src" file in place of it).
      group:                 # Name of the group that should own the file/directory, as would be fed to `chown'.
      mode:                  # Mode the file or directory should be. For those used to `/usr/bin/chmod' remember that modes are actually octal numbers. You must either add a leading zero so that
                               Ansible's YAML parser knows it is an octal number (like `0644' or `01777') or quote it (like `'644'' or `'1777'') so Ansible receives a
                               string and can do its own conversion from string into number.  Giving Ansible a number without following one of these rules will end up with
                               a decimal number which will have unexpected results. As of version 1.8, the mode may be specified as a symbolic mode (for example, `u+rwx' or
                               `u=rw,g=r,o=r').
      modification_time:     # This parameter indicates the time the file's modification time should be set to Should be `preserve' when no modification is required, `YYYYMMDDHHMM.SS' when using default
                               time format, or `now' Default is None meaning that `preserve' is the default for `state=[file,directory,link,hard]' and `now' is default for
                               `state=touch'
      modification_time_format:   # When used with `modification_time', indicates the time format that must be used. Based on default Python format (see time.strftime doc)
      owner:                 # Name of the user that should own the file/directory, as would be fed to `chown'.
      path:                  # (required) Path to the file being managed.
      recurse:               # recursively set the specified file attributes (applies only to directories)
      selevel:               # Level part of the SELinux file context. This is the MLS/MCS attribute, sometimes known as the `range'. `_default' feature works as for `seuser'.
      serole:                # Role part of SELinux file context, `_default' feature works as for `seuser'.
      setype:                # Type part of SELinux file context, `_default' feature works as for `seuser'.
      seuser:                # User part of SELinux file context. Will default to system policy, if applicable. If set to `_default', it will use the `user' portion of the policy if available.
      src:                   # path of the file to link to (applies only to `state=link' and `state=hard'). Will accept absolute, relative and nonexisting paths. Relative paths are relative to the file
                               being created (`path') which is how the UNIX command `ln -s SRC DEST' treats relative paths.
      state:                 # If `directory', all intermediate subdirectories will be created if they do not exist. Since Ansible 1.7 they will be created with the supplied permissions. If `file', the
                               file will NOT be created if it does not exist; see the `touch' value or the [copy] or [template] module if you want that behavior.  If
                               `link', the symbolic link will be created or changed. Use `hard' for hardlinks. If `absent', directories will be recursively deleted, and
                               files or symlinks will be unlinked. Note that `absent' will not cause `file' to fail if the `path' does not exist as the state did not
                               change. If `touch' (new in 1.4), an empty file will be created if the `path' does not exist, while an existing file or directory will receive
                               updated file access and modification times (similar to the way `touch` works from the command line).
      unsafe_writes:         # By default this module uses atomic operations to prevent data corruption or inconsistent reads from the target files, but sometimes systems are configured or just broken in
                               ways that prevent this. One example is docker mounted files, which cannot be updated atomically from inside the container and can only be
                               written in an unsafe manner. This option allows Ansible to fall back to unsafe methods of updating files when atomic operations fail
                               (however, it doesn't force Ansible to perform unsafe writes). IMPORTANT! Unsafe writes are subject to race conditions and can lead to data
                               corruption.
```

#### ansible-doc example
If you just run the ansible-doc file and scroll to the end, you will see a working examples of how the module is supposed to behave.

```bash
ansible-doc file
EXAMPLES:

# change file ownership, group and mode
- file:
    path: /etc/foo.conf
    owner: foo
    group: foo
    # when specifying mode using octal numbers, add a leading 0
    mode: 0644
- file:
    path: /work
    owner: root
    group: root
    mode: 01777
- file:
    src: /file/to/link/to
    dest: /path/to/symlink
    owner: foo
    group: foo
    state: link
- file:
    src: '/tmp/{{ item.src }}'
    dest: '{{ item.dest }}'
    state: link
  with_items:
    - { src: 'x', dest: 'y' }
    - { src: 'z', dest: 'k' }

# touch a file, using symbolic modes to set the permissions (equivalent to 0644)
- file:
    path: /etc/foo.conf
    state: touch
    mode: "u=rw,g=r,o=r"

# touch the same file, but add/remove some permissions
- file:
    path: /etc/foo.conf
    state: touch
    mode: "u+rw,g-wx,o-rwx"

# touch again the same file, but dont change times
# this makes the task idempotents
- file:
    path: /etc/foo.conf
    state: touch
    mode: "u+rw,g-wx,o-rwx"
    modification_time: "preserve"
    access_time: "preserve"

# create a directory if it doesn't exist
- file:
    path: /etc/some_directory
    state: directory
    mode: 0755

# updates modification and access time of given file
- file:
    path: /etc/some_file
    state: file
    mode: 0755
    modification_time: now
    access_time: now


RETURN VALUES:

```

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

#### Reference Variables
The file looks like this
```bash
---
some_random_var: "Just some test variable"
ANOTHER_VAR: "Variables are case sensitive!!"
```

Reference a variable like this:
When you want to call a variable in a playbook, it is best to double quote the curly brackets, which are specified from Jinja2 (more on that later).
```bash
"{{some_random_var}}"

```

#### Special Variables
There are a heap of special variables, and the list can be found [here](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html#special-variables).

You can use them in playbooks to great effect.

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
