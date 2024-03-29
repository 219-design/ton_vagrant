# -*- mode: ruby -*-
# vi: set ft=ruby :

module VirtualBox
    def self.config_defaults(override)
        override.vagrant.plugins = [
          'vagrant-disksize',
          'vagrant-reload',
          'vagrant-vbguest',
        ]
        override.disksize.size = "1024GB"
        override.ssh.forward_x11 = true
    end

    def self.vm_defaults(override, box="ubuntu/jammy64")
        # override.vm.box = "ubuntu/xenial64" # 16.04 LTS Xenial Xerus supported until 2021 April.
        # override.vm.box = "ubuntu/bionic64" # 18.04 LTS Bionic Beaver supported until 2023 April.
        # override.vm.box = "ubuntu/focal64" # 20.04 LTS Focal Fossa supported until 2025 April.
        # override.vm.box = "ubuntu/jammy64" # 22.04 LTS Focal Fossa supported until 2027 April.
        override.vm.box = box
    end

    def self.graphical(provider)
        provider.gui = true # This has to be set for the clipboard to be shared.
        provider.customize [ "modifyvm", :id, "--mouse", "usb" ]
        provider.customize [ "modifyvm", :id, "--vram", "256" ]
        provider.customize [ "modifyvm", :id, "--clipboard", "bidirectional" ]
    end
end

module VMware
    def self.config_defaults(override)
        override.vagrant.plugins = [
          'vagrant-disksize',
          'vagrant-reload',
        ]
        override.disksize.size = "1024GB"
        override.ssh.forward_x11 = true
    end

    def self.vm_defaults(override, box="bento/ubuntu-22.04")
        # override.vm.box = "bento/ubuntu-20.04" # 20.04 LTS Focal Fossa supported until 2025 April.
        # override.vm.box = "bento/ubuntu-22.04" # 22.04 LTS Focal Fossa supported until 2027 April.
        override.vm.box = box
    end

    def self.graphical(provider)
        provider.gui = true
        provider.vmx["usb.present"] = "TRUE"
    end
end

def find_provisioners(definition, search_dir)
    strap_path = File.join(search_dir, "strap")
    straps = Dir[File.join(strap_path, "*")].sort
    straps.each do |strap|
        strap_prefix = Regexp.new(File.join(Regexp.escape(strap_path), "\\d+_*"))
        strap_name = strap.sub(strap_prefix, "")
        strap_suffix = strap.sub(search_dir, "")

        definition.vm.provision strap_name, type: "shell", path: File.join(search_dir, "strap.sh"), args: [strap_suffix]
        definition.vm.provision strap_name+"_reload", type: "reload"
    end
end

def configure()
    # Load user-defined list of vagrants.
    # For example in user.vagrants.rb:
    # def get_vagrants()
    #     return [
    #         "aptcache",
    #         "my_machine",
    #     ]
    # end
    user_vagrants = File.join(__dir__, "user.vagrants.rb")
    if !File.exists?(user_vagrants)
        abort("Failed to find #{user_vagrants} for loading. See the main Vagrantfile for an example.")
    end
    load(user_vagrants)

    # Load Vagrantfiles.
    vagrants = get_vagrants()
    vagrants.each do |vagrant|
        vagrantfile = File.join(vagrant, "Vagrantfile")
        if !File.exists?(vagrantfile)
            abort("Failed to find #{vagrantfile} for loading.")
        end
        load(vagrantfile)
    end
end

configure()