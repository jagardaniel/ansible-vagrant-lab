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

You should now be able to SSH into the ansible VM and run Ansible. It is the primary VM so you don't have to specify the name in the command.
```bash
$ vagrant ssh
```

 The local `ansible/` directory is synced with `/ansible` on the VM so this is where you want to be when running Ansible commands. Otherwise Ansible will not be able to find the configuration and inventory file.
```bash
vagrant@ansible:~$ cd /ansible

vagrant@ansible:/ansible$ ansible -m ping lab01*
lab01.lab.internal | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Unfortunately the sync between the local `ansible/` and the `/ansible` directory on the ansible VM only happens when the VMs are being set up. The easiest way to "solve" this is to run `vagrant rsync-auto` in another window. Vagrant will then watch the local directory and auto-sync when changes are detected. You can also manually run `vagrant rsync` after you have done your changes to sync.


## Run playbooks

Community modules are no longer included in the base Ansible installation and has been moved into something called collections. So before using these modules (like postgresql) in a playbook we need to install them. They are specified in `requirements.yml` and we can install them directly from that file.

```bash
vagrant@ansible:/ansible$ ansible-galaxy collection install -r requirements.yml
```

If you want to run the entire thing
```bash
vagrant@ansible:/ansible$ ansible-playbook site.yml
```

Only target hosts in the group `nameservers` and tasks with the tag `nsd`. Keep in mind that limiting by group will not work very well in this environment since we are reusing VMs for multiple groups.
```bash
vagrant@ansible:/ansible$ ansible-playbook site.yml --limit nameservers --tags nsd
```

## Services

```
Zabbix dashboard - http://localhost:8000 (default login is Admin:zabbix)
```
