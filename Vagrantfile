nodes = {
  "ansible" => {:ip => "192.168.123.10", :primary => true, :memory => 512, :ports => []},
  "lab01" => {:ip => "192.168.123.11", :primary => false, :memory => 2048, :ports => ["8000:8000"]},
  "lab02" => {:ip => "192.168.123.12", :primary => false, :memory => 2048, :ports => []},
  "lab03" => {:ip => "192.168.123.13", :primary => false, :memory => 2048, :ports => []}
}

$setup_primary = <<-SCRIPT
apt update
apt -y -t buster-backports install ansible
chmod 400 /home/vagrant/.ssh/id_rsa
SCRIPT

$setup_base = <<-SCRIPT
cat /home/vagrant/.ssh/vagrant_lab.pub >> /home/vagrant/.ssh/authorized_keys
SCRIPT

Vagrant.configure("2") do |config|
  nodes.each_with_index do |(hostname, detail), index|
    config.vm.define "#{hostname}", primary: detail[:primary] do |conf|
      conf.vm.box = "debian/buster64"
      conf.vm.hostname = "#{hostname}"
      conf.vm.network "private_network", ip: detail[:ip]

      conf.vm.synced_folder ".", "/vagrant", disabled: true

      if detail[:primary]
        conf.vm.synced_folder "ansible/", "/ansible", type: "rsync"
        conf.vm.provision "file", source: "keys/vagrant_lab",
          destination: "/home/vagrant/.ssh/id_rsa"

        conf.vm.provision "shell", inline: $setup_primary
      else
        conf.vm.provision "file", source: "keys/vagrant_lab.pub",
          destination: "/home/vagrant/.ssh/vagrant_lab.pub"

        conf.vm.provision "shell", inline: $setup_base
      end

      # Forward ports so we can reach services from the host
      detail[:ports].each do |port|
        ports = port.split(":")
        conf.vm.network "forwarded_port", guest: ports[0].to_i, host: ports[1].to_i
      end

      conf.vm.provider "virtualbox" do |v|
        v.memory = detail[:memory]
        v.cpus = 2
      end
    end
  end
end
