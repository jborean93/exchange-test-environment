# Exchange

This can set up a test Exchange environment in a domain. Currently supports the following scenarios:

* Exchange 2013 on Server 2012 R2
* Exchange 2016 on Server 2016
* Exchange 2019 on Server 2019

The following is required to run any of these steps;

* [ansible](https://pypi.org/project/ansible/)
* [pypsrp](https://pypi.org/project/pypsrp/)
* [vagrant](https://www.vagrantup.com/)
* [libvirt](https://libvirt.org/) or [virtualbox](https://www.virtualbox.org/)
* The Exchange install ISO already downloaded from Microsoft, this is not publicly available and cannot be shared here

These examples use existing Windows Vagrant images for VirtualBox that have been created with the
[packer-windoze](https://github.com/jborean93/packer-windoze) repository. As of writting this, the following boxes are
available to be used and have been uploaded to the Vagrant Cloud service;

* jborean93/WindowsServer2012R2 - for Exchange 2013
* jborean93/WindowsServer2016 - for Exchange 2016
* jborean93/WindowsServer2019 - for Exchange 2019
* jborean93/WindowsServer2022 - for Domain Controllers

See the repo url or [this blog post](http://www.bloggingforlogging.com/2017/11/23/using-packer-to-create-windows-images/)
for more details.


## How to setup

To setup an Exchange environment you need to first download the Exchange install ISO for the version you wish to setup.
From there you can run the following to set up the environment.

```bash
# Set up 2013 environment
export EXCHANGE2013_ISO_PATH=/var/lib/libvirt/filesystems/fs/mu_exchange_server_2013_with_sp1_x64_dvd_4059293.iso
vagrant up DC2013 EXCHANGE2013
ansible-playbook main.yml --limit env2013 -vv

# Set up 2016 environment
export EXCHANGE2016_ISO_PATH=/var/lib/libvirt/filesystems/fs/mu_exchange_server_2016_cumulative_update_16_x64_dvd_fc6f65ae.iso
vagrant up DC2016 EXCHANGE2016
ansible-playbook main.yml --limit env2016 -vv

# Set up 2019 environment
export EXCHANGE2019_ISO_PATH=/var/lib/libvirt/filesystems/fs/mu_exchange_server_2019_cumulative_update_5_x64_dvd_36df3aaf.iso
vagrant up DC2019 EXCHANGE2019
ansible-playbook main.yml --limit env2019 -vv

# Set up all environments - requires each 'EXCHANGE{VERSION}_ISO_PATH' env var to be set
vagrant up
ansible-playbook main.yml -vv
```
