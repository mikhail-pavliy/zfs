# -*- mode: ruby -*-
# vim: set ft=ruby :


MACHINES = {
    :ZFS => {
        :box_name => "centos/7",
        :box_version => "2004.01",
        :ip_addr => '192.168.11.101',
        :disks => {
            :sata1 => {
                :dfile => './sata1.vdi',
                :size => 1024,
                :port => 1
            },
            :sata2 => {
                :dfile => './sata2.vdi',
                :size => 1024, # Megabytes
                :port => 2
            },
            :sata3 => {
                :dfile => './sata3.vdi',
                :size => 1024, # Megabytes
                :port => 3
            },
            :sata4 => {
                :dfile => './sata4.vdi',
                :size => 1024,  # Megabytes
                :port => 4
            },
            :sata5 => {
                :dfile => './sata5.vdi',
                :size => 1024, # Megabytes
                :port => 5
            }, 
            :sata6 => {
                :dfile => './sata6.vdi',
                :size => 1024, # Megabytes
                :port => 6
            }
        }
    },
}

Vagrant.configure("2") do |config|

    config.vm.box_version = "2004.01"
    MACHINES.each do |boxname, boxconfig|
  
        config.vm.define boxname do |box|
  
            box.vm.box = boxconfig[:box_name]
            box.vm.host_name = boxname.to_s
            box.vm.network "private_network", ip: boxconfig[:ip_addr]
  
            box.vm.provider :virtualbox do |vb|
                    vb.customize ["modifyvm", :id, "--memory", "2048"]
                    needsController = false
            boxconfig[:disks].each do |dname, dconf|
                unless File.exist?(dconf[:dfile])
                  vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                                  needsController =  true
                            end
  
            end
                    if needsController == true
                       vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
                       boxconfig[:disks].each do |dname, dconf|
                           vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                       end
                    end
            end
  
        box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
            sudo yum update && sudo yum upgrade -y
            sudo yum install -y yum-utils
            sudo yum install -y http://download.zfsonlinux.org/epel/zfs-release.el7_8.noarch.rpm
            sudo yum-config-manager --enable zfs-kmod && sudo yum-config-manager --disable zfs
            sudo yum  install -y zfs
            sudo modprobe zfs
          SHELL
  
        end
    end
  end
