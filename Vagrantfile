# frozen_string_literal: true

prereqs = <<~SCRIPT
  apt-get update -q
  apt-get install podman -y -q
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
  config.vm.provision 'shell', inline: prereqs
  config.vm.box = 'ubuntu/groovy64'

  config.vm.define 'mon-a' do |a|
    a.vm.network 'private_network', ip: '192.168.0.10'
    a.vm.provision 'shell', inline: bootstrap
  end
  config.vm.define 'mon-b' do |b|
    b.vm.network 'private_network', ip: '192.168.0.11'
  end
  config.vm.define 'mon-c' do |c|
    c.vm.network 'private_network', ip: '192.168.0.12'
  end
end
