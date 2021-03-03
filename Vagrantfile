# frozen_string_literal: true

prereqs = <<~SCRIPT
  export DEBIAN_FRONTEND=noninteractive
  apt-get update -q
  apt-get install -q -y apt-transport-https ca-certificates curl gnupg net-tools
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  apt-get update
  apt-get install -q -y docker-ce docker-ce-cli containerd.io
  curl --silent --remote-name --location https://github.com/ceph/ceph/raw/octopus/src/cephadm/cephadm
  chmod +x cephadm
  ./cephadm add-repo --release octopus
  ./cephadm install
  mkdir /etc/ceph
SCRIPT

bootstrap = <<~SCRIPT
  IP_ADDRESS=$(ifconfig enp0s8 | grep "inet " | awk '{print $2}')
  cephadm bootstrap --mon-ip $IP_ADDRESS
SCRIPT

Vagrant.configure('2') do |config|
  config.vm.box = 'ubuntu/focal64'
  config.vm.network 'forwarded_port', guest: 8443, host: 8443
  config.vm.network 'private_network', type: 'dhcp'
  config.vm.provision 'shell', inline: prereqs

  config.vm.provider 'virtualbox' do |v|
    v.memory = 2048
    v.cpus = 2
  end

  config.vm.define 'mon-a' do |a|
    a.vm.provision 'shell', inline: bootstrap
  end

  config.vm.define 'mon-b'
  config.vm.define 'mon-c'
end
