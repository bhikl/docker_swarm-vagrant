# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|vm_box = 'ubuntu/focal64'
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbooks/docker.yml"
  end
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbooks/gluster.yml"
    ansible.galaxy_role_file = "playbooks/requirements.yml"
    ansible.galaxy_command = "ansible-galaxy install -r %{role_file}"
  end
  
  (1..2).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      worker.vm.box = vm_box
      worker.vm.box_check_update = true
      worker.vm.network :private_network, ip: "192.168.57.20#{i}"
      worker.vm.hostname = "worker0#{i}"
      worker.vm.synced_folder ".", "/vagrant"
      worker.vm.provider "virtualbox" do |vb|
          vb.name = "worker0#{i}"
          vb.memory = "1024"
      end
    end
  end

  config.vm.define :manager, primary: true  do |manager|
    manager.vm.box = vm_box
    manager.vm.box_check_update = true
    manager.vm.network :private_network, ip: "192.168.57.200"
    manager.vm.network :forwarded_port, guest: 8080, host: 8080
    manager.vm.network :forwarded_port, guest: 5000, host: 5000
    manager.vm.hostname = "manager"
    manager.vm.synced_folder ".", "/vagrant"
    manager.vm.provision "ansible", preserve_order: true do |ansible|
      ansible.limit = "all"
      ansible.playbook = "playbooks/docker_swarm.yml"
      ansible.galaxy_role_file = "playbooks/requirements.yml"
      ansible.galaxy_command = "ansible-galaxy install -r %{role_file}"
    end
    manager.vm.provider "virtualbox" do |vb|
      vb.name = "manager"
      vb.memory = "1024"
    end
  end
end