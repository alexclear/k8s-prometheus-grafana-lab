# -*- mode: ruby -*-
# vi: set ft=ruby :

$KUBE1_IP = "172.16.38.22"
$KUBE2_IP = "172.16.38.23"
$KUBE3_IP = "172.16.38.24"
$KUBE1_MEM_MBS = 2048
$KUBE2_MEM_MBS = 2048
$KUBE3_MEM_MBS = 2048
$KUBE1_NUM_CPUS = 2
$KUBE2_NUM_CPUS = 2
$KUBE3_NUM_CPUS = 2

Vagrant.configure("2") do |config|
  config.vm.define "kube1" do |kube1|
    kube1.vm.box = "generic/ubuntu1804"
    kube1.vm.hostname = "kube1"
    kube1.vm.network "private_network", ip: $KUBE1_IP

    kube1.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $KUBE1_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $KUBE1_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    kube1.vm.provider :libvirt do |v, override|
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $KUBE1_NUM_CPUS
      v.memory = $KUBE1_MEM_MBS
    end

    kube1.vm.provision "shell", inline: "apt-get install -y python"
    kube1.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    kube1.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
  end

  config.vm.define "kube2" do |kube2|
    kube2.vm.box = "generic/ubuntu1804"
    kube2.vm.hostname = "kube2"
    kube2.vm.network "private_network", ip: $KUBE2_IP

    kube2.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $KUBE2_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $KUBE2_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    kube2.vm.provider :libvirt do |v, override|
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $KUBE2_NUM_CPUS
      v.memory = $KUBE2_MEM_MBS
    end

    kube2.vm.provision "shell", inline: "apt-get install -y python"
    kube2.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    kube2.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
  end

  config.vm.define "kube3" do |kube3|
    kube3.vm.box = "generic/ubuntu1804"
    kube3.vm.hostname = "kube3"
    kube3.vm.network "private_network", ip: $KUBE3_IP

    kube3.vm.provider :virtualbox do |v, override|
      override.vm.synced_folder ".", "/vagrant"
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $KUBE3_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $KUBE3_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    kube3.vm.provider :libvirt do |v, override|
      override.vm.synced_folder ".", "/vagrant", type: "nfs"
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $KUBE3_NUM_CPUS
      v.memory = $KUBE3_MEM_MBS
    end

    kube3.vm.provision "shell", inline: "apt-get install -y python python-netaddr"
    kube3.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    kube3.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
    kube3.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/site.yml"
      ansible.config_file = "ansible/ansible.cfg"
      ansible.inventory_path = "ansible/inventory"
      ansible.become = true
      ansible.galaxy_role_file = "ansible/roles/requirements.yml"
      ansible.galaxy_roles_path = "/etc/ansible/roles"
      ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"
      ansible.raw_arguments = ["--diff"]
      ansible.limit = "all"
    end
  end
end
