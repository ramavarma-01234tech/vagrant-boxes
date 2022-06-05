# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# Update the values over here if needed
APP="k8"
ENV_NAME="dev"

# Please do not edit anything below this
DOMAIN="01234.tech"
# INTERNAL_NET="192.168.15."
# BRIDGE_NET="192.168.1."

servers=[
  {
    :hostname => "master-1",
    :fqdn => "master-1." + DOMAIN,
    :ram => 4000,
    :box => "ubuntu/focal64",
    :user_home => "/home/vinay",
    :projects_home => "/home/vinay/projects",
    :bridged_nw_interface => "wlp3s0",
    :private_ip => "10.10.10.1"
},
{
  :hostname => "worker-1",
  :fqdn => "worker-1." + DOMAIN,
  :ram => 6000,
  :box => "ubuntu/focal64",
  :user_home => "/home/vinay",
  :projects_home => "/home/vinay/projects",
  :bridged_nw_interface => "wlp3s0",
  :private_ip => "10.10.10.2"
},
{
  :hostname => "worker-2",
  :fqdn => "worker-2." + DOMAIN,
  :ram => 6000,
  :box => "ubuntu/focal64",
  :user_home => "/home/vinay",
  :projects_home => "/home/vinay/projects",
  :bridged_nw_interface => "wlp3s0",
  :private_ip => "10.10.10.3"
  }
]




Vagrant.configure("2") do |config|
  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box = machine[:box]
      node.vm.hostname = machine[:fqdn]
      # node.vbguest.installer_options = { allow_kernel_upgrade: true }
      node.ssh.insert_key = false
      # node.ssh.host = machine[:hostname]
      node.vm.network "public_network",
        use_dhcp_assigned_default_route: true,
         bridge: machine[:bridged_nw_interface]
      node.vm.network "private_network",
        ip: machine[:private_ip],
        virtualbox__intnet: true
      
      node.vm.provision "shell", inline: <<-SHELL
        echo "########################################"
        echo "### Echo exeuting post boot scripts ###"
        echo "#######################################"
        sudo apt-get -y update
        echo "#######################################"
        echo "### Done exeuting post boot scripts ###"
        echo "#######################################"
      SHELL
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", machine[:ram]]
      end
      node.vm.synced_folder "guest_data/"+machine[:hostname], "/home/vagrant/data",
        create: true
      node.vm.synced_folder machine[:projects_home], "/home/vagrant/projects"
      node.vm.synced_folder machine[:user_home], "/home/vagrant/host_home"
      node.vm.synced_folder "guest_data/shared-folder", "/shared-folder",
        create: true
      node.vm.synced_folder "guest_data/k8s-pv", "/k8s-pv",
        create: true
    end
  end
end

