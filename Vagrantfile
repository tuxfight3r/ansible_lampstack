# -*- mode: ruby -*-
# # vi: set ft=ruby :

# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"

# Require YAML module
require 'yaml'

unless Vagrant.has_plugin?('vagrant-hosts')
  puts "vagrant-hosts plugin is missing. Installing..."
  system("vagrant plugin install vagrant-hosts")
  puts("plugin installed lets try again...")
  exit
end


# Read YAML file with box details
servers = YAML.load_file('servers.yml')

# Create boxes
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
 
  # Iterate through entries in YAML file
  servers.each do |servers|
    puts "Building node: #{servers['name']}"
    config.vm.define servers['name'] do |srv|
      srv.vm.box      = servers['box']
      srv.vm.hostname = servers['name']
      srv.vm.network 'private_network', ip: servers['ip']
      srv.vm.provider :virtualbox do |vb|
        vb.name   = servers['name']
        vb.memory = servers['ram']
      end
      srv.vm.provision :hosts do |provisioner|
        puts "Adding Host Entry #{servers['ip']}  #{servers['hosts_entries']}"
        provisioner.autoconfigure = true
        provisioner.sync_hosts    = true
        provisioner.add_host servers['ip'], servers['hosts_entries']
      end
    end
  end

  # Ansible provisioner.
  config.vm.provision 'ansible' do |ansible|
    ansible.playbook       = 'playbook.yml'
    ansible.inventory_path = 'inventory/test'
    ansible.sudo           = true
    ansible.extra_vars     = { env_var: 'test' }
    ansible.verbose        = 'v'
    ansible.raw_arguments  = '--vault-password-file=test_passwd_file'
    #ansible.tags          ='data'
    #ansible.raw_arguments = '--ask-vault-pass'
  end

end
