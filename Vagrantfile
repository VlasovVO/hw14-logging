# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = true
  config.vm.provider :virtualbox do |v|
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.define "ELK", primary: true do |e|
    e.vm.hostname = 'elk'
    e.vm.network "private_network", ip: "192.168.111.10"
    e.vm.provision "shell", inline: <<-SHELL
      # install docker
      yum install -y yum-utils device-mapper-persistent-data lvm2
      yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      yum install -y docker-ce
      usermod -aG docker vagrant
      systemctl enable docker.service
      systemctl start docker.service
      # install docker compose
      yum install -y epel-release
      yum install -y python-pip
      pip install docker-compose
      yum upgrade -y python*
      # install Elastic + kibana
      sysctl -w vm.max_map_count=262144
      cp /vagrant/docker-compose-ek.yml /home/vagrant/docker-compose-ek.yml
      docker-compose -f /home/vagrant/docker-compose-ek.yml up -d
    SHELL
  end

  config.vm.define "nginx", primary: true do |n|
    n.vm.hostname = 'nginx'
    n.vm.network "private_network", ip: "192.168.111.11"
    n.vm.provision "shell", inline: <<-SHELL
      # install nginx
      yum install -y yum-utils
      cp /vagrant/nginx.repo /etc/yum.repos.d/nginx.repo
      yum install -y nginx
      systemctl start nginx
      # install filebeat
      curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.7.0-x86_64.rpm
      rpm -vi filebeat-6.7.0-x86_64.rpm
      cp /vagrant/filebeat.yml /etc/filebeat/filebeat.yml
      filebeat modules enable nginx
      filebeat setup
      service filebeat start


    SHELL
  end
end
