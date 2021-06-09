IP = "192.168.25.132"
MEMORY = 2048
CPU = 2
IMAGE = "centos/7"
HOSTNAME = "nfs-server"

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.disksize.size = '200GB'

  config.vm.define HOSTNAME do |cfg|
    cfg.vm.box = IMAGE
    cfg.vm.network "public_network", ip: IP
    cfg.vm.hostname = HOSTNAME
    
    cfg.vm.provider "virtualbox" do |v|
      v.memory = MEMORY
      v.cpus = CPU
      v.name = HOSTNAME
    end

    cfg.vm.provision "file", source: "chrony.conf", destination: "/tmp/chrony.conf"

    # 예: 쿠버네티스 nfs볼륨
    cfg.vm.provision "shell", inline: <<-SCRIPT
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      yum install -y epel-release

      timedatectl set-timezone Asia/Seoul
      yum install -y git net-tools
      cp /tmp/chrony.conf /etc/chrony.conf
      systemctl enable chronyd
      systemctl restart chronyd

      yum -y install nfs-utils
      systemctl start nfs-server
      systemctl start rpcbind
      systemctl enable nfs-server
      systemctl enable rpcbind
      mkdir -p /mnt/kubernetes
      chmod 777 /mnt/kubernetes
      echo "/mnt/kubernetes 192.168.25.0/24 (rw,no_root_squash,sync)" >> /etc/exports
      systemctl restart nfs-server
    SCRIPT
  end
end
