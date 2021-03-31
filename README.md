# ansible-vagrant-lab

A Vagrant lab environment for Ansible.

## Overview

## Requirements

- VirtualBox
- Vagrant
- git

## Installation

Clone repository.
```bash
$ git clone https://github.com/jagardaniel/ansible-vagrant-lab.git
$ cd ansible-vagrant-lab/
```

Run vagrant to set up the environment.
```bash
$ vagrant up
```

You should now be able to SSH into the ansible VM and run Ansible. It is the primary VM so you don't have to specify the name when running the `vagrant ssh` command.
```bash
$ vagrant ssh
```

 The local `ansible/` directory is synced with `/home/vagrant/ansible` so this is where you want to be located when running Ansible commands, otherwise Ansible will not find the inventory or configuration file.
```bash
vagrant@ansible:~$ cd ansible/

vagrant@ansible:~/ansible$ ansible -m ping lab01
lab01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Unfortunately the sync between the local `ansible/` and the `/home/vagrant/ansible` directory on the ansible VM only happens when the VMs are being set up. The easiest way to "solve" this is to run `vagrant rsync-auto` in another window. Vagrant will then watch the local directory and auto-sync when changes are detected.
```bash
$ vagrant rsync-auto
```

You can also manually run `vagrant rsync` after you have done your changes to sync.
