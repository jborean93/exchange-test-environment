# -*- mode: ruby -*-
# vi: set ft=ruby :

# Require YAML module
require 'yaml'
 
# Read YAML file with box details
inventory = YAML.load_file('inventory.yml')

Vagrant.configure("2") do |config|
  inventory['all']['children']['controller']['hosts'].each do |server,details|
    config.vm.define server do |dc|
      dc.vm.box = details['vagrant_box']
      dc.vm.hostname = server
      dc.vm.network :private_network, ip: details['ansible_host']

      dc.vm.provider :libvirt do |v|
        v.memory = 8196
        v.cpus = 4
      end
    end
  end

  inventory['all']['children']['domain_children']['hosts'].each do |server,details|
    config.vm.define server do |srv|
      srv.vm.box = details['vagrant_box']
      srv.vm.hostname = server
      srv.vm.network :private_network, ip: details['ansible_host']

      srv.vm.provider :libvirt do |v|
        v.memory = 8196
        v.cpus = 4
        v.storage :file, :device => :cdrom, :bus => :sata, :path => ENV[server + '_ISO_PATH']
      end
    end
  end
end
