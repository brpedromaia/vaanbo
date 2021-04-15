# -*- mode: ruby -*-
# vi: set ft=ruby :

#
# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 2.2.9"
VAGRANTFILE_API_VERSION = "2"
VAGRANT_EXPERIMENTAL="disks"

# Require YAML module
require "yaml"

require 'securerandom'

current_dir = File.dirname(File.expand_path(__FILE__))
vagrantplan = YAML.load_file("#{current_dir}/.vaanbo/plan/provision-plan.yml")

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  #Always use Vagrant's default insecure key
  config.ssh.insert_key = false

  # Enabling /etc/hosts update
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true

  machineNumber = 1

  vagrantplan.each do |plan|
    machine = YAML.load_file("#{current_dir}/playlists/#{plan}")
    provision=machine["provision"]

    provision["instances"].reverse.each do |instance|
      config.vm.define "#{instance["instance_name"]}" do |srv|
        currentMachine = machineNumber
        machineNumber += 1

        puts "[ MACHINE #{currentMachine} ][  HOST/NAME ] #{instance["instance_name"]}"
        srv.vm.hostname = "#{instance["instance_name"]}"

        puts "[ MACHINE #{currentMachine} ][     BOX    ] #{provision["box"]}"
        srv.vm.box = provision["box"]
        srv.vm.box_check_update = false

        puts "[ MACHINE #{currentMachine} ][     SSH    ] add public key"
        srv.vm.provision "shell", inline: "sudo sh -c 'mkdir -p /root/.ssh;cat /vagrant/.vaanbo/ssh/vagrant.pub >> /root/.ssh/authorized_keys'"
       
        if instance["command_line"] != nil
          instance["command_line"].each do |command_line|
            puts "[ MACHINE #{currentMachine} ][    SHELL   ] #{command_line}"
            srv.vm.provision "shell", inline: "#{command_line}"
          end
        end

        puts "[ MACHINE #{currentMachine} ][   NETWORK  ] #{instance["type"]}: #{instance["ip"]}"
        if instance["type"] == "dhcp"
          srv.vm.network "private_network", type: instance["type"]
        elsif instance["type"] == "public"
          srv.vm.network "public_network", :bridge => "#{net["bridge"]}", ip: instance["ip"], :netmask => "255.255.255.128", auto_config: false
        elsif instance["type"] == "private"
          srv.vm.network "private_network", ip: instance["ip"]
        end # if net['ip_addr']

        puts "[ MACHINE #{currentMachine} ][     HD     ] #{instance["hd"]}"
        srv.vm.disk :disk, size: "#{instance["hd"]}", primary: true

        puts "[ MACHINE #{currentMachine} ][  PROVIDER  ] #{provision["provider"]}"
        srv.vm.provider provision["provider"] do |provider|
        
          if provision["provider"] == "vmware"
            # Configure CPU & RAM per settings in machines.yml (Fusion)
            provider.vmx["memsize"] = provision["ram"]
            provider.vmx["numvcpus"] = provision["vcpu"]
            if template["nested"] == true
              provider.vmx["vhv.enable"] = "TRUE"
            end #if machine['nested']
          end # if provider == "vmware"

          if provision["provider"] == "virtualbox"
            # Configure CPU & RAM per settings in machines.yml (VirtualBox)
            randomstring = SecureRandom.hex(8)
            provider.name = "#{instance["instance_name"]}"
            provider.memory = provision["ram"]
            provider.cpus = provision["vcpu"]
          end # srv.vm.provider 'virtualbox'
        end # srv.vm.provider
      end # config.vm.define
    end # instance.each
  end # machine.each
end # Vagrant.configure
