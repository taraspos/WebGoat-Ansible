Vagrant.configure("2") do |config|
  config.vm.define "webgoat" do |goat|
#   goat.vm.box = "centos/7"
    goat.vm.box = "geerlingguy/centos6"
    goat.vm.network  "private_network", ip: "192.168.1.100"
    goat.vm.provider :virtualbox do |vb|
      vb.name = "webgoat"
      vb.memory = 1024
    end
    goat.vm.provision "ansible" do |ansible|
      ansible.playbook = "vagrant_goat.yml"
    end
  end
end
