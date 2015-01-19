# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	
  config.ssh.insert_key = false

  $install_ansible = <<SCRIPT
  apt-get -qq update
  apt-get -qqy install software-properties-common
  apt-add-repository ppa:ansible/ansible
  apt-get -qq update
  apt-get -qqy install ansible git
  ssh-keyscan github.com >> /etc/ssh/ssh_known_hosts
SCRIPT

  # do this on all hosts
  config.vm.provision "shell", inline: $install_ansible

  config.vm.define "node1" do |node1|
    node1.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.33.101"
    
    # create Nodejs hello world app
    node1.vm.provision "shell",
      inline: "ansible-pull -U https://github.com/danryu/mojtest.git -i hosts playbook_appnode.yml"
  end

  config.vm.define "node2" do |node2|
    node2.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.33.102"

	# create Nodejs hello world app    
    node2.vm.provision "shell",
      inline: "ansible-pull -U https://github.com/danryu/mojtest.git -i hosts playbook_appnode.yml"
  end

  config.vm.define "nproxy" do |nproxy|
    nproxy.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    nproxy.vm.hostname = "nproxy"
    nproxy.vm.network "private_network", ip: "192.168.33.99"
    
    # install default nginx
    nproxy.vm.provision "shell",
      inline: "ansible-pull -U https://github.com/danryu/mojtest.git -i hosts playbook_nproxy.yml"
    
    # test default nginx response
    nproxy.vm.provision "shell",
      inline: "if curl -s localhost | grep nginx > /dev/null 2>&1; then echo \"\n\n######### NGINX Listening Test: OK\n\n\"; fi"
    
    # install basic nginx load balancing config
    nproxy.vm.provision "shell",
      inline: "ansible-pull -U https://github.com/danryu/mojtest.git -i hosts playbook_nproxy_balance.yml"
    
    # test nginx load balancer response
    nproxy.vm.provision "shell",
      inline: "if curl -s http://nproxy | grep Hello > /dev/null 2>&1; then echo \"\n\n######### Load Balancer: OK\n\n\"; fi"    
  end



end
