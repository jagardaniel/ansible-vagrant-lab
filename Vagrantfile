box = "debian/buster64"
domain = "lab.internal"
num_of_vms = 3 # number of lab VMs (update inventory file if you modify this)

$install_ansible = <<-SCRIPT
apt -y -t buster-backports install ansible
SCRIPT

$key_permission = <<-SCRIPT
chmod 400 /home/vagrant/.ssh/id_rsa
SCRIPT

$add_public_key = <<-SCRIPT
cat /home/vagrant/.ssh/vagrant_lab.pub >> /home/vagrant/.ssh/authorized_keys
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.define "ansible", primary: true do |conf|
    conf.vm.box = box
    conf.vm.hostname = "ansible.#{domain}"
    conf.vm.network "private_network", ip: "192.168.123.10"

    conf.vm.synced_folder ".", "/vagrant", disabled: true
    conf.vm.synced_folder "ansible/", "/home/vagrant/ansible", type: "rsync"
    conf.vm.provision "file", source: "keys/vagrant_lab",
      destination: "/home/vagrant/.ssh/id_rsa"

    conf.vm.provider "virtualbox" do |v|
      v.memory = 512
      v.cpus = 2
    end

    conf.vm.provision "shell", inline: $install_ansible
    conf.vm.provision "shell", inline: $key_permission
  end

  (1..num_of_vms).each do |i|
    config.vm.define "lab0#{i}" do |conf|
      conf.vm.box = box
      conf.vm.hostname = "lab0#{i}.#{domain}"
      conf.vm.network "private_network", ip: "192.168.123.1#{i}"

      conf.vm.synced_folder ".", "/vagrant", disabled: true
      conf.vm.provision "file", source: "keys/vagrant_lab.pub",
        destination: "/home/vagrant/.ssh/vagrant_lab.pub"

      conf.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
      end

      conf.vm.provision "shell", inline: $add_public_key
    end 
  end
end
