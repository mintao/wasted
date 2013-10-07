# -*- mode: ruby -*-
# vi: set ft=ruby :

$ip = '192.168.56.112'

configfile = File.expand_path("config")
load configfile if File.exist?(configfile)

Vagrant.configure("2") do |config|
  config.vm.box = "trusty64"

  # Use vagrant-hostmanager if installed
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.vm.hostname = "app-name.dev"
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.include_offline = true
  end

  # filedump.mayflowerkunden.de currently only accessible via IPv4! (2013-11-25)
  config.vm.box_url = 'http://filedump.mayflower.de/baseboxes/ubuntu-14.04-puppet3.5.1-vbox4.3.10.box'

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--name", "devstack"]
  end

  # Use vagrant-cachier if installed
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.auto_detect = true
  end

  # Install r10k using the shell provisioner and download the Puppet modules
  config.vm.provision :shell, :path => "bootstrap.sh"

  config.vm.synced_folder "./", "/var/www/app_name"
  config.vm.network :private_network, :ip => $ip

  config.vm.provision :hostmanager if Vagrant.has_plugin?("vagrant-hostmanager")
  config.vm.provision :puppet do |puppet|
    puppet.manifests_path    = "manifests"
    puppet.manifest_file     = "ubuntu-devstack.pp"
    puppet.module_path       = ["modules", "site"]
    puppet.options           = "--verbose "
    puppet.hiera_config_path = "hiera.yaml"
  end
end
