# -*- mode: ruby -*-
# vi: set ft=ruby :

###  Configuration parameters  ###
# IP address for private network if configured
HOST = "" # leave as empty string if not configuring private network.

# The port number used on the guest OS that will map to the host OS.
# This option is ignored if using private network.
# Set as empty string if configuring private network
GUEST_PORT = "8081"  


Vagrant.configure("2") do |config|
  config.vm.box = 'geerlingguy/ubuntu1804'
  # Vagrant instance name displayed in console
  config.vm.define 'myNodeApp'

  config.vm.network :forwarded_port, guest: GUEST_PORT, host: 80
  config.vm.hostname = "myNodeApp.dev"
  config.ssh.insert_key = false
  config.vm.synced_folder '~/src/prototypes/js/hello-world', '/var/www/myNodeApp', 
    type: "nfs", mount_options: ['rw', 'vers=3', 'tcp'],
    linux_nfs_options: ['rw', 'no_subtree_check', 'all_squash', 'async']

  config.vm.provider :virtualbox do |v|
    v.name = 'myNodeApp-vm'
    v.memory = 2048
    v.cpus = 1
  end

  # Ansible provisioner.
  config.vm.provision :ansible do |ansible|
    ansible.compatibility_mode="2.0"
    ansible.playbook = 'provisioning/playbook.yml'
    ansible.inventory_path = "provisioning/inventory/dev_inventory"
    ansible.limit = "all"
    ansible.galaxy_role_file = "provisioning/requirements.yml"
    ansible.become = true
    ansible.extra_vars = {
      ansible_ssh_user: "vagrant",
      ansible_ssh_private_key_file: "~/.vagrant.d/insecure_private_key",
      host: HOST,
      port: GUEST_PORT
    }
    # Enables passing of args to Ansible from Vagrant CLI
    # via the A_ARGS environment variable
    if ENV['ANSIBLE_ARGS']
      ansible.raw_arguments = Shellwords.shellsplit(ENV['A_ARGS'])
    end
  end
end
