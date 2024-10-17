Vagrant.configure("2") do |config|
  config.vm.box = "jammy"
  config.vm.provider :virtualbox do |v|
    v.memory = 2048
    v.cpus = 2
  end


  boxes = [
    { :name => "server",
      :ip => "192.168.56.12",
    },
    { :name => "client",
      :ip => "192.168.56.13",
    }
  ]

  
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.hostname = opts[:name]
      config.vm.network "private_network", ip: opts[:ip]
      config.vm.disk :disk, size: "5GB", name: "disk-1"

      config.vm.provision "shell", inline: <<-SHELL
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
          systemctl restart sshd.service
      SHELL

      if opts[:name] == boxes.last[:name]
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "ansible/provision.yml"
          ansible.inventory_path = "ansible/hosts"
          ansible.host_key_checking = "false"
          ansible.limit = "all"
       end
      end
    end
  end
end
