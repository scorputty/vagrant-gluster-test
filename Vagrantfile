Vagrant.configure("2") do |config|
  # create Gluster Servers
  # https://docs.vagrantup.com/v2/vagrantfile/tips.html
  (22..24).each do |i|
    config.vm.define "ansiblehost#{i}" do |node|
      node.vm.box = "scorputty/centos"
      node.vm.hostname = "ansiblehost#{i}"
      node.vm.network :private_network, ip: "10.42.0.#{i}"
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.customize ["createhd",  "--filename", "2nd-disk-ansiblehost#{i}", "--size", "256"]
        vb.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", "1", "--device", "1", "--type", "hdd", "--medium", "2nd-disk-ansiblehost#{i}.vdi"]
      end
    end
  end
  # create Gluster Clients
  # https://docs.vagrantup.com/v2/vagrantfile/tips.html
  (25..26).each do |i|
    config.vm.define "ansiblehost#{i}" do |node|
      node.vm.box = "scorputty/centos"
      node.vm.hostname = "ansiblehost#{i}"
      node.vm.network :private_network, ip: "10.42.0.#{i}"
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end
    end
  end
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end
  #Run Ansible from the Vagrant Host
  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true
    ansible.verbose = "v"
    ansible.host_key_checking = false
    ansible.inventory_path = "provisioning/inventory"
    ansible.playbook = "provisioning/site.yml"
    ansible.limit = "all" 
  end
end
