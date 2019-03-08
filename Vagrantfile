# -*- mode: ruby -*-
# vi: set ft=ruby :

$KUBE1_IP = "172.16.137.22"
$KUBE2_IP = "172.16.137.23"
$KUBE3_IP = "172.16.137.24"
$KUBE1_MEM_MBS = 2048
$KUBE2_MEM_MBS = 2048
$KUBE3_MEM_MBS = 2048
$KUBE1_NUM_CPUS = 2
$KUBE2_NUM_CPUS = 2
$KUBE3_NUM_CPUS = 2

ANSIBLE_RAW_SSH_ARGS = []

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
  end

  config.vm.define "kube3" do |kube3|
    kube3.vm.box = "generic/ubuntu1804"
    kube3.vm.hostname = "kube3"
    kube3.vm.network "private_network", ip: $KUBE3_IP

    kube3.vm.provider :virtualbox do |v, override|
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube1/virtualbox/private_key "
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube2/virtualbox/private_key "
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube3/virtualbox/private_key "
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $KUBE3_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $KUBE3_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    kube3.vm.provider :libvirt do |v, override|
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube1/libvirt/private_key "
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube2/libvirt/private_key "
      ANSIBLE_RAW_SSH_ARGS << " -o IdentityFile=./.vagrant/machines/kube3/libvirt/private_key "
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $KUBE3_NUM_CPUS
      v.memory = $KUBE3_MEM_MBS
    end

    kube3.vm.provision "shell", inline: "apt-get install -y python"
    kube3.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/site.yml"
      ansible.config_file = "ansible/ansible.cfg"
      ansible.inventory_path = "ansible/inventory"
      ansible.become = true
      ansible.limit = "all"
      ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
    end
  end
end
