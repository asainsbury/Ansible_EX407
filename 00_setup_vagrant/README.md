# In the beginning..
Well you made it this far, well done!

---

- [In the beginning..](#in-the-beginning-)
  	- [Introduction](#introduction)
- [Dependencies](#dependencies)
  	- [Setup the Stack](#setup-the-stack)
  	- [Git clone - [Vagrant-Maker]](#git-clone-vagrant-maker)
- [Test It Works](#test-it-works)
- [Next Section - Core Components](#next-section-core-components)

## Introduction
As we mentioned back on the main page, we need to setup a few things before we can follow along with the examples.  As I've already spent a good amount of time working with Vagrant we can just use a repo I already created to auto generate our test VM's.

<strong>IF</strong> you don't want to use Vagrant but would prefer to use some cloud provider, no problemo! Just make sure you have the following:

- 3 Centos nodes
- SSH Keys working between all nodes and your admin node (I use a mac).
- Tested that you can SSH without a password into all 3 nodes.
- Make sure each node has access to the Internet, so we can download packages and other stuff.
- I like to call my nodes DEV1, DEV2 so it might help if you stick to that (slightly boring) standard.

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../01_core_components/">next</a> </b>
</div>
<br/>

# Dependencies
<strong>IF</strong> you do want to follow along, using Vagrant, then make sure you do this:

- Install Virtual box
- Install Vagrant
- Probably a good idea to sign up to Github (its pretty good)
	- Then setup SSH Keys, to get away from https authentication.
- Run a Python virtual environment with PyENV on your laptop.
- You will need a working version of Ansible 2.7. 
- I would suggest running 3.7x of Python also.

I'll have to write up how to setup PyEnv for the MAC, but for now, you have to run away and have a go at getting that lot going before we start, otherwise you can just read along, but this isn't meant to be a spectator sport!


#### Setup the Stack
Summary of steps:

0. Complete all the dependencies as outlined above!! 
1. Git clone vagrant maker repo to your pc or laptop.
2. Generate some new dummy rsa keys to overwrite the ones provided.
3. Run the playbook to provision the environment.
4. Run ```vagrant up``` to spin up the 3 VM's
	- Run vagrant up --provision to restart the bootstrap file at any time.



#### Git clone - [Vagrant-Maker](https://github.com/asainsbury/vagrant-maker)
Vagrant-maker does this:
- Stands up 3 Centos VM's
- Runs a bootstrap process to base line each node.
- All resources are sourced from a file, so you can tweak them up or down.
- Creates a custom SSH config file local to your machine with alias to each machine.
- Copies your Public key to all nodes.
- Copies dummy insecure keys to all nodes for connectivity.

Another thing, before we start, lets talk about root paths for these repos!

I sort of started using <strong>```~/projects/scripts/```</strong> for my root path of all repositories, you don't have to follow that standard but it might help when following along with these examples.


```
cd ~/projects/scripts
git clone git@github.com:asainsbury/vagrant-maker.git

cd ~/projects/scripts/vagrant-maker/
ansible-playbook vagrant-maker
vagrant up
```

I might include the STDOUT produced by the above commands, but for now lets just hope you managed to get it working! Just check connectivity between your laptop and the nodes, by establishing a new SSH session to all. Then in a separate terminal window you can carry on with the rest of the repo.

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../01_core_components/">next</a> </b>
</div>
<br/>

# Test It Works
Using this adhoc command, we can test connectivity after you have stood up the VM's:

```bash
ansible dev -m ping -i ../01_core_components/hosts -u user
```

__Breaking That Down:__

- *ansible*
	- Define and run a single task 'playbook' against a set of hosts 
- *dev*
	- The group we want to use
- *-m ping*
	- is the ad hoc module, which is ping in this case
- *-i Path_to_inventory_file*
	- just using the inventory file from the next chapter
- *-u user*
	- We need to run the commands with a remote user variable, and we setup the hosts with "user"



__And product the following output:__

```bash
dev1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
dev3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
dev2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

<strong>NOTE: </strong> Make sure you accept the host keys, when prompted, then after a couple of runs you will be able to run the command.  This is because of the SSH Config file we setup earlier, whilst trying to make life easier..

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../01_core_components/">next</a> </b>
</div>
<br/>

# Next Section - [Core Components](../01_core_components//)
OK, ground zero is completed, lets move onto the concepts!

<div align="right">
    <b><a href="#top">↥ top</a>   |   <a href="../01_core_components/">next</a> </b>
</div>
<br/>



