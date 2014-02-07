# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.ssh.forward_agent = true

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
  end

  config.vm.boot_timeout = 120

  config.omnibus.chef_version = :latest

  config.berkshelf.enabled = true

  couchPassword = 'Welcome1'

  ipAddrPrefix = '10.11.12.1'
  config.vm.define :app do |node|
    node.vm.box = 'precise64'
    node.vm.network :private_network, ip: ipAddrPrefix + '0'
    node.vm.hostname = 'backbone-app'
    node.vm.provision :chef_solo do |chef|
      chef.run_list = [
        'recipe[rails4_couchbase::app]'
      ]
    end
  end

  nodeCount = 3
  1.upto(nodeCount) do |n|
    nodeName = ("node" + n.to_s).to_sym
    config.vm.define nodeName do |node|
      node.vm.box = 'precise64'
      node.vm.network :private_network, ip: ipAddrPrefix + n.to_s
      node.vm.hostname = "#{nodeName}.couchbase.server"
      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[rails4_couchbase::db]'
        ]
        chef.json = {
          "couchbase" => {
            "server" => {
              "password" => couchPassword
            }
          }
        }
      end
    end
  end
end

