# frozen_string_literal: true

prereqs = <<~SCRIPT
  export DEBIAN_FRONTEND=noninteractive
  apt-get update -q
  apt-get install -q -y apt-transport-https ca-certificates curl gnupg net-tools chrony
  systemctl enable --now chrony.service
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
  cat << EOF > /etc/ssh/sshd_config
  PermitRootLogin yes
  PasswordAuthentication yes
  PubkeyAuthentication yes
  ChallengeResponseAuthentication no
  UsePAM yes
  X11Forwarding yes
  PrintMotd no
  AcceptEnv LANG LC_*
  Subsystem sftp /usr/lib/openssh/sftp-server
  EOF
  systemctl restart sshd
  echo -e 'vagrant\\nvagrant' | passwd root
  echo "192.168.56.10 ceph-mon-a" >> /etc/hosts
  echo "192.168.56.11 ceph-mon-b" >> /etc/hosts
  echo "192.168.56.12 ceph-mon-c" >> /etc/hosts
SCRIPT

bootstrap = <<~SCRIPT
  IP_ADDRESS=$(ifconfig enp0s8 | grep "inet " | awk '{print $2}')
  cephadm bootstrap --mon-ip $IP_ADDRESS
SCRIPT

Vagrant.configure('2') do |config|
  config.vm.box = 'ubuntu/focal64'
  config.vm.provision 'shell', inline: prereqs
  config.vm.provision 'shell', inline: "ifconfig enp0s8 | grep 'inet ' | awk '{print $2}'"

  config.vm.provider 'virtualbox' do |v|
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.define 'mon-a' do |a|
    a.vm.network 'private_network', ip: '192.168.56.10', name: 'vboxnet0'
    a.vm.network 'forwarded_port', guest: 8443, host: 8443
    a.vm.provision 'shell', inline: bootstrap
    a.vm.hostname = 'ceph-mon-a'
  end

  config.vm.define 'mon-b' do |b|
    b.vm.network 'private_network', ip: '192.168.56.11', name: 'vboxnet0'
    b.vm.hostname = 'ceph-mon-b'
  end

  config.vm.define 'mon-c' do |c|
    c.vm.network 'private_network', ip: '192.168.56.12', name: 'vboxnet0'
    c.vm.hostname = 'ceph-mon-c'
  end
end
