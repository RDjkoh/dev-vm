# BUSI Vagrant Dev VM

## Installation
### Prerequisites 

Install [Vagrant](https://www.vagrantup.com/docs/installation/) on your host machine.

Install the [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) Vagrant plugin.

[SSH keys](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html) and access to the Bitbucket repos. 

### Steps

1. Name your public SSH key to id\_rsa.pub and your private SSH key id\_rsa.
2. Do a git pull for this repo. 
3. Copy the SSH key files into git folder. 
4. Run the command "vagrant up". It takes about 10-15 minutes for everything to download and install.
5. Once everything finishes, run "vagrant ssh" to SSH into the machine. You can access stuff at these locations:

Chrome is the only confirmed browser for now. Firefox is currently unsupported. 

Marvel at http://localhost:5601/app/marvel

Sense at http://localhost:5601/app/sense

Elastic at http://localhost:9200/_plugin/hq/#cluster

## Issues
Stopping/starting the VM rapidly may cause Vagrant to be confused about the state of forwarded ports. If this happens, shutdown the VM, wait (5 minutes) and try again. 

It may get stuck at "Building the VirtualBox Guest Additions kernel modules". If it's unresponsive, break out, run "vagrant destroy" and re-initialize the VM. 

When you run "vagrant up" the first time, you may run into an error message saying <"rsync" could not be found on your PATH. Make sure that rsync is properly installed on your system and available on the PATH.> This is because the base CentOS 7 box doesn't have the right provider type. For Windows, go to [user directory]/.vagrant.d/boxes/centos-VAGRANTSLASH-7/[whatever box version you're on]/virtualbox/ and open the Vagrantfile. On the 3rd line, change the [type: "rsync"] to [type: "virtualbox"] and try "vagrant up" on the dev VM again. 
