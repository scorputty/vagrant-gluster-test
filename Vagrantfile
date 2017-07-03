# Run different setup test by defining these variables
# provsimple (simple ansible with glusterfs fuse clients)
# provheketi (test installation with heketi)
# provgdeploy (test installton with gdeploy and ganesha)
# provtower setup system to use with Ansible Tower (you need a (free) tower licence)
# add Ansible Tower or skip it by setting: TOWERSERVER = 0
# Download the following plugins:
# hostmanager
# vagrant plugin install vagrant-hostmanager

PROVTYPE = "provtower"
GLUSTERSERVER = 3
GLUSTERCLIENT = 2
TOWERSERVER = 1

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.hostmanager.enabled = true

  config.vm.provider "virtualbox" do |v|
    v.linked_clone = true
  end


  (1..GLUSTERSERVER).each do |i|
    config.vm.define "glusterserver#{i}" do |node|
      node.vm.box = "scorputty/centos"
      node.vm.hostname = "glusterserver#{i}"
      node.vm.network :private_network, ip: "10.42.0.%d" % (20 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        unless File.exists?("2nd-disk-glusterserver#{i}.vdi")
          vb.customize ["createhd",  "--filename", "2nd-disk-glusterserver#{i}", "--size", "20480"] # min size for heketi is 10g
        end
        vb.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", "1", "--device", "1", "--type", "hdd", "--medium", "2nd-disk-glusterserver#{i}.vdi"]
      end

      if i == GLUSTERSERVER
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "#{PROVTYPE}/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "glusterfs_server_group"
          ansible.groups = {
            "tower_server_group" => ["towerserver[1:#{TOWERSERVER}]"],
            "glusterfs_server_group" => ["glusterserver[1:#{GLUSTERSERVER}]"],
            "glusterfs_client_group" => ["client[1:#{GLUSTERCLIENT}]"]
          }
        end
      end
    end
  end


  (1..GLUSTERCLIENT).each do |i|
    config.vm.define "client#{i}" do |node|
      node.vm.box = "scorputty/centos"
      node.vm.hostname = "client#{i}"
      node.vm.network :private_network, ip: "10.42.0.%d" % (23 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end

      if i == GLUSTERCLIENT
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "#{PROVTYPE}/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "glusterfs_client_group"
          ansible.groups = {
            "tower_server_group" => ["towerserver[1:#{TOWERSERVER}]"],
            "glusterfs_server_group" => ["glusterserver[1:#{GLUSTERSERVER}]"],
            "glusterfs_client_group" => ["client[1:#{GLUSTERCLIENT}]"]
          }
        end
      end
    end
  end


  (1..TOWERSERVER).each do |i|
    config.vm.define "ansible-tower" do |node|
      node.vm.box = "ansible/tower"
      node.vm.hostname = "ansible-tower"
      node.vm.network :private_network, ip: "10.42.0.%d" % (41 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
      end

      if i == TOWERSERVER
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "#{PROVTYPE}/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "tower_server_group"
          ansible.groups = {
            "tower_server_group" => ["ansible-tower"],
            "glusterfs_server_group" => ["glusterserver[1:#{GLUSTERSERVER}]"],
            "glusterfs_client_group" => ["client[1:#{GLUSTERCLIENT}]"]
          }
        end
      end
    end
  end


end

# Run from cli like this:
# ansible-playbook --user=vagrant --become -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory provgdeploy/site.yml --tags setup-firewall
