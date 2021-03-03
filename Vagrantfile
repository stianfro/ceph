$script = <<-SCRIPT
apt update
apt upgrade -y
apt install docker -y
curl --silent --remote-name --location https://github.com/ceph/ceph/raw/octopus/src/cephadm/cephadm
chmod +x cephadm
./cephadm add-repo --release octopus
./cephadm install
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.network "private_network", type: "dhcp"
  config.vm.provision "shell", inline: $script
  config.vm.box = "ubuntu/focal64"

  config.vm.define "mon-a"
  config.vm.define "mon-b"
  config.vm.define "mon-c"

  # config.vm.define "mon-c" do |mon|
  #   mon.vm.box = "ubuntu/focal64"
  # end
end

