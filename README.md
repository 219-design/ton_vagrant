# Vagrant VM

Because virtual machines are hard to redistribute, more difficult to tweak and even more difficult to version control [Vagrant](https://www.vagrantup.com/) is used for this project to simplify and make the development environment more reproducible. Note that the Vagrant setup isn't hermetic and will pull software from external sources and may use newer versions where not explicitly controlled.

## All Platforms

- Download and install [Vagrant](https://www.vagrantup.com)
- Download and install [VirtualBox](https://www.virtualbox.org)

## Ubuntu

### Initial Set Up

Open a terminal in the host machine and add the current user to the group of vboxusers. This is necessary in order for the guest machine to see USB-connected devices.

```bash
sudo adduser $USER vboxusers
```

Once you've added yourself as a vboxuser reboot the host machine so that the side-effects of being added as a user take effect.

```bash
sudo reboot
```

Go to the General Set Up section to continue.

## General Set Up

### Including Machines

You'll need to git clone the machines you want/need to use into this folder. The machine configurations aren't git submodules so that there aren't explicit references to other machines that may exist for clients.

#### Machine Enumeration

In order to enforce a boot order when one machine depends on another (as is the case with the cache and interview machines) creating a file user.vagrants.rb to list the folders where the machine's Vagrantfile is held needs to be created. The below is an example of this file.

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

Open a terminal and change directory to the one containing the file named Vagrantfile and begin the vagrant set up process.

```bash
 vagrant up $MACHINE
```

This process can take a while. It will bring up a VirtualBox VM window but don't try to use it until the process in the terminal is complete. If the output of the terminal you ran ```vagrant up $MACHINE``` from shows an error you will have to figure out the problem from the terminal output.

In most cases you shouldn't have to log in but if you do log in as user "vagrant" and password "vagrant". You can also access the command line from the same directory as the Vagrantfile using Vagrant's ssh.

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
