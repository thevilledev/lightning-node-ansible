# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/contrib-buster64"
  config.vm.network "private_network", ip: "192.168.33.103"
  config.ssh.insert_key = true
  config.ssh.forward_agent = true

  config.vm.provider :virtualbox do |vb|
     vb.name = "lightning-node-ansible"
     vb.customize ["modifyvm", :id, "--memory", "4096"]
     vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  config.vm.provision "shell",
    inline: "mkdir -p /data"

  config.vm.provision :ansible_local do |ansible|
    ansible.playbook = "provisioning/deploy-bitcoind.yml"
    ansible.groups = {
      "lnd-nodes" => ["default"]
    }
  end

end
