# Vagrant Dev VM

## Installation
### Prerequisites 

Install [Vagrant](https://www.vagrantup.com/docs/installation/) on your host machine.

Install the [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) Vagrant plugin.

[SSH keys](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html) and access to the Bitbucket repos. 

[Git and Git Bash](https://git-scm.com/download/win)

### Steps
These steps are for Windows but should work for OS X and Linux as well. Make sure you can find your .vagrant.d folder. 

1. Open Git Bash. Run the following command:
`vagrant box add centos/7`
You should see the following success message:
`box: Successfully added box 'centos/7' (v1605.01) for 'virtualbox'!`
2. Go to your .vagrant.d folder. It should be under `C:/Users/[your user name]/.vagrant.d/`. Find the centos/7 Vagrantfile by navigating to `C:/Users/[your user name]/.vagrant.d/boxes/centos-VAGRANTSLASH-7/[box version]/virtualbox/Vagrantfile`. In this example, box version is v1605.01, as shown in the success message earlier. 
Edit this Vagrantfile. Find and replace "rsync" to "virtualbox". The edited line should look like this. 
`config.vm.synced_folder ".", /home/vagrant/sync", type: "virtualbox"`
3. Create a folder. This folder will represent this dev VM. Go to this newly made folder location in Git Bash. Initialize a git repo by running:
`git init`
4. Pull from the dev VM git repo by running:  
`git pull https://github.com/johnkohdotca/dev-vm.git` or `git pull git@bitbucket.org:RD-BUSI/dev_vm.git` 
5. Copy your SSH keys into this folder. Name your public SSH key to id\_rsa.pub and your private SSH key id\_rsa.
6. Run the command `vagrant up`. It takes about 10-15 minutes for everything to download and install.
7. Once everything finishes, run `vagrant ssh` to SSH into the machine. You can access stuff at these locations:

Chrome is the only confirmed browser for now. Firefox is currently unsupported. 

Marvel at http://localhost:5601/app/marvel

Sense at http://localhost:5601/app/sense

Elastic at http://localhost:9200/_plugin/hq/#cluster

8. Once you exit out of shell, you can use `vagrant halt` to shut off the VM. `vagrant destroy` deletes any resources used by the VM, including disk drives.

## Issues
Stopping/starting the VM rapidly may cause Vagrant to be confused about the state of forwarded ports. If this happens, shutdown the VM, wait (5 minutes) and try again. 

It may get stuck at "Building the VirtualBox Guest Additions kernel modules". If it's unresponsive, break out, run "vagrant destroy" and re-initialize the VM.