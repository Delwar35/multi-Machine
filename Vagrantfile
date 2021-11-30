Vagrant.configure("2") do |config|
  config.vm.provision "shell", path:"provision.sh" 

#creating app vm
  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "192.168.10.100"
    app.vm.synced_folder ".", "/home/vagrant/app"
  end

#creating db vm
  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.10.100"
    db.vm.synced_folder ".", "/home/vagrant/app"

  end
end
