$copy_private_key_ansible = <<-SCRIPT
  cp /vagrant/id_bionic /home/vagrant && \
  chmod 600 /home/vagrant/id_bionic && 
  chown vagrant:vagrant /home/vagrant/id_bionic
SCRIPT

$install_ansible = <<-SCRIPT
  apt-get update && \
  apt-get install -y software-properties-common && \
  apt-add-repository --yes --update ppa:ansible/ansible && \
  apt-get -y install python3 ansible=2.9.16-1ppa~bionic
SCRIPT

$exec_ansible = <<-SCRIPT
  ansible-galaxy collection install community.mysql,v1.1.1 && \
  ansible-playbook -i /vagrant/ansible/hosts \
  /vagrant/ansible/main.yml
SCRIPT

$copy_public_key_host = <<-SCRIPT
  cat /vagrant/id_bionic.pub >> .ssh/authorized_keys
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "private_network", ip: "10.80.4.10"

    mysqlserver.vm.provider "virtualbox" do |vb|
      vb.name = "mysqlserver_ansible"
    end

    mysqlserver.vm.provision "shell", inline: $copy_public_key_host
  end

  config.vm.define "springapp" do |springapp|
    springapp.vm.network "forwarded_port", guest: 8080, host: 8080
    springapp.vm.network "private_network", ip: "10.80.4.14"

    springapp.vm.provider "virtualbox" do |vb|
      vb.name = "springapp_ansible"
      vb.memory = 2048
      vb.cpus = 2
    end

    springapp.vm.provision "shell", inline: $copy_public_key_host
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.network "private_network", ip: "10.80.4.12"

    ansible.vm.provider "virtualbox" do |vb|
      vb.name = "ansible"
    end

    ansible.vm.provision "shell", inline: $copy_private_key_ansible
    ansible.vm.provision "shell", inline: $install_ansible
    ansible.vm.provision "shell", privileged: false, inline: $exec_ansible
  end
end