![Dashbrew Logo](https://raw.githubusercontent.com/mdkholy/mdkholy.github.io/master/assets/img/etc/dashbrew-logo-640.png)

## MadLab Fork of Dashbrew

### System Requirements

Before launching your Dashbrew environment, you must install VirtualBox and Vagrant. Both of these software packages provide easy-to-use visual installers for all popular operating systems.

* [Vagrant >= 1.6.5](http://www.vagrantup.com/)
* [VirtualBox 4.3.x](https://www.virtualbox.org/)
* [Vagrant Hosts Provisioner plugin](https://github.com/mdkholy/vagrant-hosts-provisioner) for managing the /etc/hosts file of both the host and guest machines.
	* ``$ vagrant plugin install vagrant-hosts-provisioner``

### Adding The Vagrant Box

Once VirtualBox and Vagrant have been installed, you should add the ``mdkholy/dashbrew`` box to your Vagrant installation using the following command in your terminal. It will take a few minutes to download the box, depending on your Internet connection speed:

```
$ vagrant box add mdkholy/dashbrew
```

### Clone The Dashbrew Repository

Once the box has been added to your Vagrant installation, you should clone this repository. Consider cloning the repository into a central directory where you keep all of your projects, as Dashbrew will serve as the host to all of your PHP projects.

```
$ git clone --recursive git://github.com/madlab/dashbrew.git
```

***Note:*** The ``--recursive`` is required to clone the repository with all its dependencies (i.e. git submodules).  

### The First Vagrant Up

Once you have cloned the Dashbrew Repository, start the Vagrant environment by running ``vagrant up`` command from the Dashbrew directory in your terminal. Vagrant will setup the virtual machine and boot it for the first time. This could take a while on the first run.

Once provisioned, PHP will not quite be working yet. SSH into your vagrant box using ``vagrant ssh`` then run the following command to create the PHP7 config file: 
``mv /opt/phpbrew/php/7.0.0/etc/php-fpm.d/www.conf.default /opt/phpbrew/php/7.0.0/etc/php-fpm.d/www.conf``

### Launch the Dashbrew Dashboard

Once the ``vagrant up`` command is finished, you can now launch the Dashbrew Dashboard by visiting [http://dashbrew.dev/](http://dashbrew.dev/) in your browser. Here is a screenshot of what it looks like.

![Dashbrew Dashboard](https://raw.githubusercontent.com/mdkholy/mdkholy.github.io/master/assets/img/etc/dashbrew-dashboard-1024.png)

## Configuration

Dashbrew environment can be configured via a configuration file located in ``config/environment.yaml``. This file allows managing different components on your environment such as php versions, system packages, ruby gems, npm modules, etc. By default php 5.6 and 7.0 are installed. 

## Adding Projects

In order to add a project to Dashbrew, all your project files needs to be under ``public/`` directory which is the root directory of the apache web server. 

Every project needs a project configurations file in order to be added to Dashbrew. This file needs to be created in the project's root directory with the name ``.dashbrew`` and must be a valid YAML file. This file allows configuring different project aspects such as the php version it runs on and the apache virtual host entry needed for launching it.

So lets say you have the following project structure (under ``public/`` directory):
```
myproject/
|-- assets/
|-- lib/
|-- index.php
```

Create a new file called ``.dashbrew`` under ``myproject/`` directory like this:
```
myproject/
|-- assets/
|-- lib/
|-- .dashbrew
|-- index.php
```

Edit ``.dashbrew`` file with the following configurations:
```
---
myproject:
  title: My Project
  php:
    build: system
  vhost:
    servername: myproject.dev
    serveraliases:
      - www.myproject.dev
    ssl: true
```

Run ``vagrant provision`` so that Dashbrew can find your project and make the nesseccary changes. Then visit [http://myproject.dev/](http://myproject.dev/) in your browser and you should see the output of the ``index.php``.

What we done here is that we added a new project that:

* runs on system php
* can be accessed via ``myproject.dev`` or ``www.myproject.dev``
* has SSL enabled so that it could be accessed using https

For more information on adding projects, please visit [the wiki page](https://github.com/mdkholy/dashbrew/wiki/Projects).

## Shared Configuration Files

These are configuration files primarily used by software installed on the guest machine and are shared with the host machine in order to facilitate editing them. Examples of these files are the Apache configuration file, MySQL configuration file and PHP INI files for each PHP build.

Dashbrew uses a bi-directional synchronization logic to keep the shared configuration files in sync between the host and guest machines. Synchronization of these files is done during [the provisioning process](#applying-your-changes).

All shared configuration files are located under ``config/`` directory and are organized in subfolders according their relevant software.

Here is a [list of the currently supported configuration files](https://github.com/mdkholy/dashbrew/wiki/SharedConfigurationFiles).

## Applying Your Changes

Whenever you make changes to the Dashbrew environment (e.g. changing a configuration file, adding/removing projects), you need to run ``vagrant provision`` in order to apply your changes. Dashbrew provisioner will provide you with useful info and debug (if enabled) messages during the provisioning process.

## Default Environment Information

### Virtual Machine

* IP Address: ``192.168.10.100``
* Base Memory: ``1024``
* CPUs: ``1``


### SSH

* Port: ``22``
* Username: ``vagrant``
* Password: ``vagrant``
* Private Key: *The default insecure private key that ships with Vagrant*


### MySQL

* Port: ``3306``
* Root Username: ``root``
* Root Password: ``root``
