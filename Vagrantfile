# -*- mode: ruby -*-
# vi: set ft=ruby :

def hostname(node)
  "#{node.to_s}.smartengine.local"
end

Vagrant.configure("2") do |config|

  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.ssh.forward_agent = true

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.auto_detect = true
  end

  config.vm.boot_timeout = 120

  config.omnibus.chef_version = :latest

  config.berkshelf.enabled = true

  ipAddrPrefix = '10.11.12.'
  config.vm.define :app do |node|
    node.vm.box = 'precise64'
    node.vm.hostname = hostname(nodeName) 
    node.vm.network :private_network, ip: ipAddrPrefix + '10'
    node.vm.provision :chef_solo do |chef|
      chef.run_list = [
        'recipe[smartengine::app]'
      ]
    end
  end

  couchNodes = 2
  1.upto(couchNodes) do |n|
    nodeName = ("couch" + n.to_s).to_sym
    config.vm.define nodeName do |node|
      node.vm.box = 'precise64'
      node.vm.hostname = hostname(nodeName) 
      node.vm.network :private_network, ip: ipAddrPrefix + '1' + n.to_s
      node.vm.provider :virtualbox do |vb|
        vb.memory = 1024
      end
      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[smartengine::db]'
        ]
      end
    end
  end
  elasticNodes = 2
  1.upto(elasticNodes) do |n|
    nodeName = ("elastic" + n.to_s).to_sym
    config.vm.define nodeName do |node|
      node.vm.box = 'precise64'
      node.vm.hostname = hostname(nodeName) 
      node.vm.network :private_network, ip: ipAddrPrefix + '2' + n.to_s
      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[smartengine::search]'
        ]
      end
    end
  end
end

