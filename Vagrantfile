# -*- mode: ruby -*-
# vim: set ft=ruby :
# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :inetRouter => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.255.1', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "router-net"},
                ]
  },
  :inetRouter2 => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.255.3', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "router-net"},
                ]
  },
  :centralRouter => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.255.2', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "router-net"},
                   {ip: '192.168.0.1', adapter: 3, netmask: "255.255.255.240", virtualbox__intnet: "server-net"},
                ]
  },
  
  :centralServer => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.240", virtualbox__intnet: "server-net"}, {adapter: 3, auto_config: false, virtualbox__intnet: true},
                   {adapter: 4, auto_config: false, virtualbox__intnet: true},
                ]
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        if boxname.to_s == "inetRouter2"
          box.vm.network "forwarded_port", guest: 8080, guest_ip: "192.168.255.3", host: 9090, host_ip: "127.0.0.1",  protocol: "tcp"
        end
        box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        SHELL
        box.vm.provision "ansible" do |ansible|
          ansible.become = true
          ansible.verbose = "v"
          ansible.playbook = "provisioning/playbook.yml"
        end
      end
  end
end

