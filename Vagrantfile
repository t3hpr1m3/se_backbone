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
  config.ssh.forward_x11 = true

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.auto_detect = true
  end

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
  end

  config.vm.boot_timeout = 120

  config.omnibus.chef_version = :latest

  config.berkshelf.enabled = true

  ipAddrPrefix = '10.11.12.'
  config.vm.define :app do |node|
    node.vm.box = 'precise64'
    node.vm.hostname = hostname('app') 
    node.vm.network :private_network, ip: ipAddrPrefix + '10'
    node.vm.provision :chef_solo do |chef|
      chef.run_list = [
        'recipe[smartengine::base]',
        'recipe[smartengine::app]',
        'recipe[smartengine::db_proxy]',
        'recipe[smartengine::search_proxy]'
      ]
    end
  end

  couchNodes = 2
  1.upto(couchNodes) do |n|
    nodeName = ("couch" + n.to_s).to_sym
    config.vm.define nodeName do |node|
      node.vm.box = 'precise64'
      node.vm.hostname = hostname(nodeName) 
      node.vm.network :private_network, ip: ipAddrPrefix + '2' + n.to_s
      node.vm.provider :virtualbox do |vb|
        vb.memory = 1024
      end
      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[smartengine::base]',
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
      node.vm.network :private_network, ip: ipAddrPrefix + '3' + n.to_s
      node.vm.provider :virtualbox do |vb|
        vb.memory = 1536
      end
      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[smartengine::base]',
          'recipe[smartengine::search]'
        ]
        chef.json = {
          'elasticsearch' => {
            'network' => {
              'publish_host' => ipAddrPrefix + '3' + n.to_s
            }
          } 
        }
      end
    end
  end
end

