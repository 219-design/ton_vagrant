# -*- mode: ruby -*-
# vi: set ft=ruby :

def config_defaults(config)
    config.vagrant.plugins = [
      'vagrant-disksize',
      'vagrant-reload',
      'vagrant-vbguest',
    ]
    config.disksize.size = "1024GB"
    config.ssh.forward_x11 = true
end

def vm_defaults(vm, box="ubuntu/bionic64")
    # vm.box = "ubuntu/xenial64" # 16.04 LTS Xenial Xerus supported until 2021 April.
    # vm.box = "ubuntu/bionic64" # 18.04 LTS Bionic Beaver supported until 2023 April.
    vm.box = box
end

def find_providers(definition, search_dir)
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

def graphical_virtualbox(virtualbox)
    virtualbox.gui = true # This has to be set for the clipboard to be shared.
    virtualbox.customize [ "modifyvm", :id, "--mouse", "usb" ]
    virtualbox.customize [ "modifyvm", :id, "--vram", "256" ]
    virtualbox.customize [ "modifyvm", :id, "--clipboard", "bidirectional" ]
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