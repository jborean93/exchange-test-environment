# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'open3'
require 'yaml'

# Read YAML file with box details
inventory = YAML.load_file('inventory.yml')
memory = 8196
cpus = 4

Vagrant.configure("2") do |config|
  inventory['all']['children']['controller']['hosts'].each do |server,details|
    config.vm.define server do |dc|
      dc.vm.box = details['vagrant_box']
      dc.vm.hostname = server
      dc.vm.network :private_network,
        :ip => details['ansible_host'],
        :libvirt__network_name => 'exchange-test',
        :libvirt__domain_name => inventory['all']['vars']['domain_realm']

      dc.vm.provider :libvirt do |v|
        v.memory = memory
        v.cpus = cpus
      end

      dc.vm.provider :virtualbox do |v|
        v.name = File.basename(File.dirname(__FILE__)) + '_' + server + '_' + Time.now.to_i.to_s
        v.memory = memory
        v.cpus = cpus
      end
    end
  end

  inventory['all']['children']['domain_children']['hosts'].each do |server,details|
    config.vm.define server do |srv|
      srv.vm.box = details['vagrant_box']
      srv.vm.hostname = server
      srv.vm.network :private_network,
        :ip => details['ansible_host'],
        :libvirt__network_name => 'exchange-test',
        :libvirt__domain_name => inventory['all']['vars']['domain_realm']

      srv.vm.provider :libvirt do |v|
        v.memory = memory
        v.cpus = cpus
        v.storage :file, :device => :cdrom, :bus => :sata, :path => ENV[server + '_ISO_PATH']
      end

      srv.vm.provider :virtualbox do |v|
        vm_name = File.basename(File.dirname(__FILE__)) + '_' + server + '_' + Time.now.to_i.to_s
        v.name = vm_name
        v.memory = memory
        v.cpus = cpus

        # The VBoxManager storagectl is not idempotent, we need to check if it's already created or not
        stdout_str, stderr_str, st  = Open3.capture3('VBoxManage', 'showvminfo', vm_name, '--machinereadable')
        storagectl_exists = st.success? || stdout_str =~ /storagecontrollername\d+="ISO"/
        if not storagectl_exists
          v.customize ["storagectl", :id, "--name", "ISO", "--add", "sata"]
          v.customize ["storageattach", :id, "--storagectl", "ISO", "--port", "0", "--device", "0", "--type", "dvddrive", "--medium", ENV[server + '_ISO_PATH']]
        end
      end
    end
  end
end
