# Vagrant VM

Because virtual machines are hard to redistribute, more difficult to tweak and even more difficult to version control [Vagrant](https://www.vagrantup.com/) is used for this project to simplify and make the development environment more reproducible. Note that the Vagrant setup isn't hermetic and will pull software from external sources and may use newer versions where not explicitly controlled.

## All Platforms

* Download and install [Vagrant](https://www.vagrantup.com)
* Download and install a supported virtual machine provider
    * [VirtualBox](https://www.virtualbox.org)
    * [VMware](https://www.vmware.com) (note that Workstation Player may be missing `vmrun` in recent Linux releases, check the Initial Set Up section before continuing)
        * [Vagrant VMware Utility](https://www.vagrantup.com/vmware/downloads) with [installation instructions](https://www.vagrantup.com/docs/providers/vmware/vagrant-vmware-utility)
        * [Vagrant VMware plugin](https://www.vagrantup.com/vmware) with [installation instructions](https://www.vagrantup.com/docs/providers/vmware/installation)
            * [License file can be bought here](https://www.vagrantup.com/vmware)

It is strongly recommended that you store any virtual machine disks on the highest speed storage device available on your system.

## Linux

### Initial Set Up

#### VirtualBox

Open a terminal in the host machine and add the current user to the group of vboxusers. This is necessary in order for the guest machine to see USB-connected devices.

```bash
sudo adduser $USER vboxusers
```

Once you've added yourself as a vboxuser reboot the host machine so that the side-effects of being added as a user take effect.

```bash
sudo reboot
```

Go to the General Set Up section to continue.

#### VMware

In Workstation 16 Player the executable for managing virtual machines `vmrun` is missing. So before paying for a broken product you can install an evaluation of the VMware product you want to use. If you're not sure try Workstation Player first and if that doesn't have `vmrun` then Workstation Pro should. You can check for the presence of `vmrun` this way:

```bash
which vmrun
```

If you get a path like `/usr/bin/vmrun` back that should indicate that `vmrun` is available.

You can now continue to install the Vagrant-specific tools in the All Platforms section and then continue to General Set Up

## General Set Up

### Including Machines

You'll need to git clone the machines you want/need to use into this folder. The machine configurations aren't git submodules so that there aren't explicit references to other machines that may exist for clients.

#### Machine Enumeration

Each machine definition will have its own Vagrantfile to describe how to set it up. In order to find these Vagrantfiles you will need to create a user.vagrants.rb in this repository's directory to enumerate each directory containing a Vagrantfile. Here's an example of this file.

```ruby
def get_vagrants()
    return [
        "ton_vagrant_cache",
        "ton_vagrant_interview",
    ]
end
```

Here is what this example looks like after the cache and interview machines are cloned and the user.vagrants.rb file is added.

```
README.md
ton_vagrant_cache
ton_vagrant_interview
user.vagrants.rb
Vagrantfile
```

### Creation

Open a terminal and change directory to the one containing the file named Vagrantfile. To create a machine without specifying the virtual machine provider you can run the following.

```bash
 vagrant up $MACHINE
```

If you want to specify that the VM will be provided by VMware you may specify it at creation time.

```bash
 vagrant up $MACHINE --provider=vmware_desktop
```

This process can take a while. It will bring up a VM window but don't try to use it until the process in the terminal is complete. If the output of the terminal you ran ```vagrant up $MACHINE``` from shows an error or hangs you will have to figure out the problem from the terminal output.

When the machine is not running the same command can be used to boot the machine. But so long as the machine exists the provider does not need to be respecified.

In most cases you shouldn't have to log in but if you do log in as user "vagrant" and password "vagrant". You can also access the command line from the same directory as the `vagrant up` command using Vagrant's ssh.

```bash
vagrant ssh $MACHINE
```

### Shutting Down

You can shut down a machine but keep its disk contents by halting the machine.

```bash
vagrant halt $MACHINE
```

You can also use the normal methods of shutting down the machine from within the VM.

**GOTCHA** It's possible to reboot the VM from within itself or the VirtualBox GUI as well as power up the VM from  the VirtualBox GUI but it isn't guaranteed to mount the project directory shared between both the guest and host machines.

### Destruction

If you want to get rid of the constructed VM and its entire disk simply open a terminal and change directory to the same directory as the Vagrantfile and run Vagrant's destroy command.

```bash
vagrant destroy $MACHINE
```
