# vim: set ft=ruby :
disk_controller = 'IDE' # MacOS. This setting is OS dependent. Details https://github.com/hashicorp/vagrant/issues/8105

Vagrant.configure("2") do |config|
  # Определите хэш для конфигурации виртуальных машин
  MACHINES = {
    "bash" => {
      :box_name => "debian/bookworm64",
      :cpus => 1,
      :memory => 1024,
      :ip => "192.168.57.20",
      :public_ip => "10.68.7.42",
      :bridge_interface => "enp1s0",
      :forwarded_port_int => "80",
      :forwarded_port_ext => "8080",
      :hostname => "rpm1.local",
    }
  }

  # Проходим по каждой записи в хэше
  MACHINES.each do |name, machine|
   config.vm.define name do |machine_config|
    machine_config.vm.network "private_network", ip: machine[:ip]
#    machine_config.vm.network "public_network", bridge: "machine[:bridge_interface]", ip: machine[:public_ip]
    machine_config.vm.network "forwarded_port", guest: machine[:forwarded_port_int], host: machine[:forwarded_port_ext] 
    machine_config.vm.box = machine[:box_name]
    machine_config.vm.box_version = machine[:box_version]
    machine_config.vm.host_name = machine[:hostname]
      machine_config.vm.provider "virtualbox" do |v|
        v.memory = machine[:memory]
        v.cpus = machine[:cpus]
      end
    machine_config.vm.provision "shell", inline: <<-SHELL
       sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
       systemctl restart sshd.service 
       apt update -y
    SHELL
    end
    config.vm.provision "ansible" do |ansible|
    #ansible.verbose = "vvv"
    ansible.playbook = "provisioning/playbook.yml"
    ansible.become = "true"
    end
end
end
