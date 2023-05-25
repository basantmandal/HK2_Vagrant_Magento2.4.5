# HK2 Vagrant Magento2.4.5

## Introduction
This Box is fully compatible for Magento 2.4.5. Includes Screenshot and Vagrantfile on how to use it. Detailed Information can be found at - [https://github.com/basantmandal/HK2_Vagrant_Magento2.4.5](https://github.com/basantmandal/HK2_Vagrant_Magento2.4.5)

## Prerequisites
Vagrant 2.3.x
VirtualBox 7.x

## Box Contains :- 
- Ubuntu 22.04
- Elastic Search
- Redis
- Adminer
- Nginx
- MySQL 8
- PHP 8
- GIT
- Composer
- MailHog

## Credentials
- Ubuntu UserName/Password :- vagrant/vagrant
- MySQL :- admin/admin

## Error 

> You may get `The IP address configured for the host-only network is not within the
allowed ranges`

Please change the `config.vm.network "private_network", ip: "192.168.2.183"` to the the allowed ranges IP and run the command again

> There was an error while executing `VBoxManage`, a CLI used by Vagrant
for controlling VirtualBox. The command and stderr is shown below.
Command: ["startvm", "30eed00a-68d8-4c2b-803b-c5faf04c708d", "--type", "headless"]

Run script:
```bash
sudo /sbin/vboxconfig
```

> If still error persists, Remove virtualbox and reinstall it.

## Change your Host Settings
On MacOS and Linux, open the hosts file (/etc/hosts) with elevated privileges:
sudo nano /etc/hosts

Add the following entry to a new line:

```bash
192.168.2.183 www.magento245.store
192.168.2.183 adminer.magento245.store
```

> Note: On Windows, the hosts file is located at: c:\Windows\System32\Drivers\etc\hosts

## Remember
Remember to change the `auth.json` file on the Magento Root Folder, Else Magento Specific Composer commands will not work

## Vagrant Configuration

```bash
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "basantmandal/HK2_Vagrant_Magento2.4.5"
    #config.vm.box_version = "1.0.1"
    config.vm.network "private_network", ip: "192.168.2.183"

    # Default HTTP port
    config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
    config.vm.network "forwarded_port", guest: 443, host: 8443, auto_correct: true

    # MySQL Port
    config.vm.network "forwarded_port", guest: 3306, host: 3306

    # Redis Port
    config.vm.network "forwarded_port", guest: 6379, host: 6379, auto_correct: true

    # OpenSearch Port
    config.vm.network "forwarded_port", guest: 9200, host: 9200, auto_correct: true
    config.vm.network "forwarded_port", guest: 9300, host: 9300, auto_correct: true

    config.vm.provider "virtualbox" do |vb|
      # Customize CPU, Ram, Vbox Name & Disable Audio
      vb.cpus = 2
      vb.memory = "8192"
      vb.name = "HK2_Magento245"
      vb.customize ["modifyvm", :id, "--audio", "none"]

      # Fixing issue of VM loosing sync of the date and time while sleeping.
      vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 10000]
    end

    # Share Local Folder with Vagrant Shared Folder
    # config.vm.synced_folder "/var/www/magento245/app/code", "/var/www/magento245/app/code", owner: "vagrant", group: "www-data"

    # Provision - Clean Magento Logs
    config.vm.provision "shell", privileged: true, inline: <<-SHELL
        echo $(tail -5000 /var/www/magento245/var/log/exception.log) >/var/www/magento245/var/log/exception.log
        echo $(tail -5000 /var/www/magento245/var/log/system.log) >/var/www/magento245/var/log/system.log
        echo $(tail -5000 /var/www/magento245/var/log/cron.log) >/var/www/magento245/var/log/cron.log
        echo $(tail -5000 /var/www/magento245/var/log/debug.log) >/var/www/magento245/var/log/debug.log
    SHELL
end
```

## Links
- [Magento Frontend (http://www.magento245.store)](http://www.magento245.store)
- [Magento Backend (http://www.magento245.store/admin)](http://www.magento245.store/admin)
- [Adminer (http://adminer.magento245.store)](http://adminer.magento245.store)
