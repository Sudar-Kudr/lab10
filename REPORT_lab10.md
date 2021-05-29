## Laboratory work X

Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием **Vagrant**

```sh
$ open https://www.vagrantup.com/intro/index.html
```

## Tasks

- [ok] 1. Ознакомиться со ссылками учебного материала
- [ok] 2. Выполнить инструкцию учебного материала
- [ok] 3. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>    #присваиваем имя пользователя GitHub в переменную GITHUB_USERNAME
$ export PACKAGE_MANAGER=<пакетный_менеджер>  #указываем используемый пакетный менеджер
```

```sh
$ cd ${GITHUB_USERNAME}/workspace      #спускаемся в workspace
$ ${PACKAGE_MANAGER} install vagrant    #скачиваем vagrant
Updating Homebrew...
==> Auto-updated Homebrew!
..........................................................
```

```sh
$ vagrant version                      #узнаем версию vagrant
Installed Version: 2.2.16
Latest Version: 2.2.16
You're running an up-to-date version of Vagrant!
$ vagrant init bento/ubuntu-19.10       #создаем виртуальную машину убунту
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
$ less Vagrantfile                       #увидим содержимое Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10   #перезаписываем изначальную вирт. машину (-f)
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

```sh
$ mkdir shared     #создаем директорию shared
```

```sh
$ cat > Vagrantfile <<EOF #записываем в файл Vagrantfile комманды для запуска скрипта
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```

```sh
$ cat >> Vagrantfile <<EOF #дозаписываем конфигурацию для виртуальной машины

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```

```sh
$ cat >> Vagrantfile <<EOF #дозаписываем конфигурацию для виртуальной машины

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
```

```sh
$ vagrant validate        #устраняем неполадки
Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
Fetching micromachine-3.0.0.gem
Fetching vagrant-vbguest-0.29.0.gem
Installed the plugin 'vagrant-vbguest (0.29.0)'!
$ vagrant validate         #проверяем файл Vagrantfile
Vagrantfile validated successfully
$ vagrant status          #посмотрим списки вируальных машин и их статусы
Current machine states:

default                   not created (virtualbox)

$ vagrant up # --provider virtualbox        #запускаем виртуальную машину
$ vagrant port                               #информация портов
$ vagrant status                              #посмотрим списки вируальных машин и их статусы
Current machine states:

default                   running (virtualbox)
$ vagrant ssh                      #подключим по ssh к виртуальной машине
$ vagrant snapshot list              #посмотрим список сохранённых состояний виртальной машины
$ vagrant snapshot push                #сделаем снимок текущего состояния виртуальной машины
$ vagrant snapshot list                  #посмотрим список сохранённых состояний виртальной машины
$ vagrant halt                             #останавливаем виртуальную машину
$ vagrant snapshot pop                       #востановим состояние виртуальной машины по снимку
```

```ruby                     #настраиваем Vagrant для работы с VMware
  config.vm.provider :vmware_esxi do |esxi| 

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```

```sh
$ vagrant plugin install vagrant-vmware-esxi           #установим плагин 
$ vagrant plugin list                                 #посмотрим список плагин
$ vagrant up --provider=vmware_esxi                  #запускаем
```

## Report

```sh
$ cd ~/workspace/                                                                    #спускаемся в директорию workspace
$ export LAB_NUMBER=10                                                              #присваиваем 10 в переменную LAB_NUMBER
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER} #клонируем из ссылки в директорию (в нашем случае-tasks/lab10)
$ mkdir reports/lab${LAB_NUMBER}                                                  #создаем директорию (в нашем случае- lab10)
cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md           #копируем из одной директории в другую
cd reports/lab${LAB_NUMBER}                                                     #спускаемся в директорию (в нашем случае- lab10)
edit REPORT.md                                                                 #редактируем REPORT.md
gist REPORT.md                                                                #сохраняем REPORT.md
```

## Links

- [VirualBox](https://www.virtualbox.org/)
- [Vagrant providers](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins#providers)
- [Vagrant vbguest plugin](https://github.com/dotless-de/vagrant-vbguest)
- [Vagrant disksize plugin](https://github.com/sprotheroe/vagrant-disksize)
- [Vagrant vmware esxi plugin](https://github.com/josenk/vagrant-vmware-esxi)

```
Copyright (c) 2015-2021 The ISC Authors
```
