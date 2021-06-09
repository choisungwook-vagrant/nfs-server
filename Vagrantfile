require 'yaml'

CONFIG = YAML.load_file(File.join(File.dirname(__FILE__), 'config.yml'))
MOUNT_VOLUME = CONFIG['nfs']['volume_name']
VOLUME_PERMISSION = CONFIG['nfs']['volume_permission']
MOUNT_CIDR = CONFIG['nfs']['cidr']

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.disksize.size = CONFIG['server']['disksize']

  config.vm.define CONFIG['server']['name'] do |cfg|
    cfg.vm.box = CONFIG['server']['box']
    cfg.vm.network "public_network", ip: CONFIG['server']['ip']
    cfg.vm.hostname = CONFIG['server']['hostname']
    
    cfg.vm.provider "virtualbox" do |v|
      v.memory = CONFIG['server']['memory']
      v.cpus =  CONFIG['server']['cpu']
      v.name =  CONFIG['server']['hostname']
    end

    # 1. virtualbox 초기 설정
    cfg.vm.provision "shell", inline: <<-SCRIPT
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      yum install -y epel-release
    SCRIPT

    # 2. 시간설정
    cfg.vm.provision "file", source: "chrony.conf", destination: "/tmp/chrony.conf"
    cfg.vm.provision "shell", inline: <<-SCRIPT
      timedatectl set-timezone Asia/Seoul
      yum install -y git net-tools
      cp /tmp/chrony.conf /etc/chrony.conf
      systemctl enable chronyd
      systemctl restart chronyd
    SCRIPT

    # 3. nfs 설정
    # 테스트 목적으로만 사용하세요. 보안문제 존재(default: mkdir 777)
    cfg.vm.provision "shell", inline: <<-SCRIPT
      yum -y install nfs-utils
      systemctl start nfs-server
      systemctl start rpcbind
      systemctl enable nfs-server
      systemctl enable rpcbind
      mkdir -p #{MOUNT_VOLUME}
      chmod #{VOLUME_PERMISSION} #{MOUNT_VOLUME}
      echo "#{MOUNT_VOLUME} #{MOUNT_CIDR}(rw,no_root_squash,sync)" >> /etc/exports
      systemctl restart nfs-server
    SCRIPT
  end
end
