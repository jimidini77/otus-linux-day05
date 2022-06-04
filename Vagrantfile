# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :zfs => {
        :box_name => "centos/7",
        :box_version => "2004.01",
        :ip_addr => '192.168.11.101',
	:disks => {
		:sata1 => {
			:dfile => './sata1.vdi',
			:size => 100,
			:port => 1
		},
		:sata2 => {
                        :dfile => './sata2.vdi',
                        :size => 100, # Megabytes
			:port => 2
		},
                :sata3 => {
                        :dfile => './sata3.vdi',
                        :size => 100,
                        :port => 3
                },
                :sata4 => {
                        :dfile => './sata4.vdi',
                        :size => 100, # Megabytes
                        :port => 4
                },
                :sata5 => {
                        :dfile => './sata5.vdi',
                        :size => 100, # Megabytes
                        :port => 5
                },
                :sata6 => {
                        :dfile => './sata6.vdi',
                        :size => 100, # Megabytes
                        :port => 6
                },
                :sata7 => {
                        :dfile => './sata7.vdi',
                        :size => 100, # Megabytes
                        :port => 7
                },
                :sata8 => {
                        :dfile => './sata8.vdi',
                        :size => 100, # Megabytes
                        :port => 8
                }
	}
  },
}

Vagrant.configure("2") do |config|
config.vm.synced_folder ".", "/vagrant", disabled: true
  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            	  vb.customize ["modifyvm", :id, "--memory", "1024"]
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
          box.vm.provision "shell", path: "setup_zfs.sh"
# 	  box.vm.provision "shell", inline: <<-SHELL
#	      mkdir -p ~root/.ssh
#	      cp ~vagrant/.ssh/auth* ~root/.ssh
#	      yum install -y http://download.zfsonlinux.org/epel/zfs-release.el7_8.noarch.rpm
#	      #import gpg key
#	rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux
#	#install DKMS style packages for correct work ZFS
#	yum install -y epel-release
#	#yum install -y kernel-devel
#	#yum install -y dkms
#	#yum install -y zfs
#	#change ZFS repo
#	yum-config-manager --disable zfs
#	yum-config-manager --enable zfs-kmod
#	yum install -y zfs
#	#Add kernel module zfs
#	modprobe zfs
#	#install wget
#	yum install -y wget
##	      yum install -y mdadm smartmontools hdparm gdisk
##              mdadm --zero-superblock --force /dev/sd{b,c,d,e,f,g}
##              mdadm --create /dev/md0 -l 5 -n 4 /dev/sd{b,c,d,e}
##              mdadm /dev/md0 --add /dev/sd{f,g}
##              mkdir -p /etc/mdadm
#              touch /etc/mdadm/mdadm.conf
#              chmod o+w /etc/mdadm/mdadm.conf
#              echo "DEVICE partitions" > /etc/mdadm/mdadm.conf
#              mdadm --detail --scan --verbose | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf
#              chmod o-w /etc/mdadm/mdadm.conf
#              parted -s /dev/md0 mklabel gpt
#              for i in $(seq 0 20 80); do parted -s /dev/md0 mkpart primary ext4 $i% $((i+20))%; done 
#              for i in $(seq 1 5); do sudo mkfs.ext4 /dev/md0p$i; done
#              mkdir -p /raid/part{1,2,3,4,5}
#              for i in $(seq 1 5); do mount /dev/md0p$i /raid/part$i; done
#              chmod o+w /etc/fstab
#              for i in $(seq 1 5); do echo -e "/dev/md0p$i\\t/raid/part$i\\text4\\trw,relatime,seclabel,stripe=1536,data=ordered\\t0\\t0" >> /etc/fstab; done
#              chmod o-w /etc/fstab
#  	  SHELL

      end
  end
end

