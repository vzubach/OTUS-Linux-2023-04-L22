# -*- mode: ruby -*-
# vim: set ft=ruby :
MACHINES = {
  # Имя DV "Centos8"
  :"Centos8" => {
              # VM box
              :box_name => "centos/8",
              #box_version
              :box_version => "2011.0",
              # Количество ядер CPU
              :cpus => 2,
              # Указываем количество ОЗУ (В Мегабайтах)
              :memory => 1024,
              # Указываем IP-адрес для ВМ
              :ip => "192.168.57.10",
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Отключаем сетевую папку
    config.vm.synced_folder ".", "/vagrant", disabled: true
    # Добавляем сетевой интерфейс
    config.vm.network "private_network", ip: boxconfig[:ip]
    # Применяем параметры, указанные выше
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s

      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
      box.vm.provision "shell", inline: <<-SHELL
          #Разрешаем подключение пользователей по SSH с использованием пароля
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
          #Перезапуск службы SSHD
          systemctl restart sshd.service
          cd /etc/yum.repos.d/
          sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
          sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
          yum install nano -y
      SHELL
    end
  end
end

