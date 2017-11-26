# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_COMMAND = ARGV[0]

Vagrant.configure(2) do |config|
  
  config.env.enable # enable the vagrant-env plugin

  # VirtualBox based deployment target
  config.vm.define "vbox-ubuntu-16.04", primary: true do |vboxubuntu|
    vboxubuntu.vm.box = "bento/ubuntu-16.04"
    vboxubuntu.vm.hostname = "nanomsg-role-ubuntu-xenial64"

    vboxubuntu.ssh.username = "vagrant"
    vboxubuntu.ssh.insert_key = false
  
    vboxubuntu.vm.provider :virtualbox do |vb, override|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
  
      # Provide machine name
      vb.name = "nanomsg-role"
  
      # Customize the amount of memory on the VM:
      vb.memory = ENV["VM_RAM"] ||= "1024"
  
      # Customize the amount of cpus on the VM:
      vb.cpus = 2
    end
  
  end

  # Azure based deployment target
  config.vm.define "azure-ubuntu-16.04" do |ubuntu1604|
    ubuntu1604.vm.box = "azure"

    ubuntu1604.vm.hostname = "nanomsg-role-ubuntu-xenial64"
    ubuntu1604.ssh.username = "vagrant"
    ubuntu1604.ssh.private_key_path = ENV['RSA_PUB_KEY']
    ubuntu1604.ssh.insert_key = true

    ubuntu1604.vm.provider :azure do |azure, override|
      azure.tenant_id = ENV['ARM_TENANT_ID']
      azure.client_id = ENV['ARM_CLIENT_ID']
      azure.client_secret = ENV['ARM_CLIENT_SECRET']
      azure.subscription_id = ENV['ARM_SUBSCRIPTION_ID']
      azure.location = ENV['ARM_LOCATION']
      azure.vm_password = ENV['ARM_VM_PASSWORD']
      azure.vm_image_urn = "Canonical:UbuntuServer:16.04-LTS:latest"
      azure.vm_size = ENV['ARM_VM_SIZE']
      azure.instance_ready_timeout = 600
    end

    # Install git - Required by ansible_local if ansible roles are required. ansible-galaxy with require git to clone roles from scm. 
    ubuntu1604.vm.provision "shell", name: "Install Dependencies", privileged: true, inline: <<-SHELL
      apt-get install -y git
    SHELL
  end

  # Package Role as gzipped tarball - output referenced in vagrant_requirements.yml
  config.vm.provision "ansible_local" do |ansible|
    ansible.verbose = "vvvv"
    ansible.install_mode = "pip" 
    ansible.version = "2.4.1.0"
    ansible.config_file = "ansible.cfg"
    ansible.playbook = "build.yml"
    ansible.extra_vars = {
      nanomsg_role_nanomsg_version: "1.0.0"
    }
  end

  # Run Ansible Role from the Vagrant VM
  config.vm.provision "ansible_local" do |ansible|
    ansible.verbose = "vvvv"
    ansible.install_mode = "pip" 
    ansible.version = "2.4.1.0"
    ansible.config_file = "ansible.cfg"
    ansible.playbook = "vagrant.yml"
    ansible.galaxy_role_file = "vagrant_requirements.yml"
    ansible.galaxy_roles_path = "roles/:."
    ansible.extra_vars = {
      nanomsg_role_nanomsg_version: "1.0.0"
    }
  end

end
