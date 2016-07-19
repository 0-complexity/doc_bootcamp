## Setup Docker Machine on a Virtual Machine

Below we provide a step by step guide on how to set-up a working Drupal environment deployed in two Docker containers, one for Drupal and one for MySQL.

Create a new cloud space, suggested name is 'Drupal'


* Add a new virtual machine to the cloud space, suggested name 'DM-Host', this virtual machine will be used to host docker machine.

* Add following port forwardings for this virtual machine:
 * 7122:22
 * 2376:2376

* Open a SSH session into the new virtual machine:

	```shell
	ssh cloudscalers@<ip-address> -p 7122
	```

* If not already the case, add the hostname of the virtual machine (typically "vm-three\_digit\_number") in `/etc/hosts` to the first line:

	```shell
	sudo vi /etc/hosts
	127.0.0.1 localhost virtual_machine_name
	```

* Create new user that is member of the sudo group in order to allow the new user to use sudo commands

	```shell
	sudo adduser new_user_name
	sudo adduser new_user_name sudo
	```
	
* Logout and login again as new_user_name

* Add your public key (`cat ~/.shh/id_rsa.pub`) to the remote `authorized_keys`:

	```shell
	mkdir ~/.ssh
	vi ~/.ssh/authorized_keys
	```

* In order to meet [the prerequisites of Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/) and in order to make sure that your Docker version is 1.8.3 or higher: 

	```shell
	sudo apt-get update
	sudo apt-get install linux-image-extra-$(uname -r)
	```
	
* Enable usage of sudo without password (needed for the next step, installation of docker-machine from local machine):

	```shell
	echo ‘<new_username> ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/<new_username>
	sudo chmod 400 /etc/sudoers.d/<new_username> => only the owner (root) will have read access
	```

* Make sure you have [Docker machine installed](https://docs.docker.com/machine/install-machine/) on your local machine, if not the case:

	```shell
 	curl -L https://github.com/docker/machine/releases/download/v0.7.0/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine
 	chmod +x /usr/local/bin/docker-machine
	```

	```shell
	docker-machine -D create -d generic --generic-ip-address <cloudspace_IP> --generic-ssh-port 7122 --generic-ssh-user <new_username> dm4drupal
	```

* Check that Docker machine got installed

	```shell
	docker-machine ls
	```
	
* Make the newly created docker machine current:

	```shell
	eval $(docker-machine env dm4drupal)
	docker-machine ls => a star will appear next to dm4drupal
	```
