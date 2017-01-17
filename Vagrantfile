GLUSTERSERVER = 3
GLUSTERCLIENT = 2

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
      node.vm.network :private_network, ip: "10.42.0.%d" % (21 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.customize ["createhd",  "--filename", "2nd-disk-glusterserver#{i}", "--size", "256"]
        vb.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", "1", "--device", "1", "--type", "hdd", "--medium", "2nd-disk-glusterserver#{i}.vdi"]
      end

      if i == GLUSTERSERVER
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "provisioning/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "glusterfs_group"
          ansible.groups = {
            "glusterfs_group" => ["glusterserver[1:#{GLUSTERSERVER}]"],
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
      node.vm.network :private_network, ip: "10.42.0.%d" % (24 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end

      if i == GLUSTERCLIENT
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "provisioning/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "glusterfs_client_group"
          ansible.groups = {
            "glusterfs_group"          => ["glusterserver[1:#{GLUSTERSERVER}]"],
            "glusterfs_client_group" => ["client[1:#{GLUSTERCLIENT}]"]
          }
        end
      end
    end
  end

end
