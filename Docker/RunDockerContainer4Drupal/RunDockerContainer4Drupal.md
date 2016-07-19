## Run Docker container for hosting Drupal sites

* Set your current Docker Machine to the Docker Machine created in the previous section:

	```shell
	eval $(docker-machine env docker-machine4drupal)
	docker-machine ls => a star will appear next to docker-machine4drupal
	```

* Let’s first create a container for mysql from [Docker Hub] (https://hub.docker.com/r/mysql/):
	
	```shell
	docker run --name mysql-on-dm-host -p 3306:3306 -e MYSQL_ROOT_PASSWORD=yourMySQLpassword -d mysql/mysql-server:5.7 
	```
	
	(-p binds container port to host port)
	
	(-e is for setting environment variables)
	
	(-d is running the container in background and printing the container ID)
	
	
* Start a shell in the running container:

	```shell
	docker exec -it mysql-on-dm-host bash
	```

* Start mysql for adding a user:

	```shell
	mysql -uroot -p"yourMySQLpassword"
	create user 'drupal_user' identified by 'yourMySQLpassword';
	grant all privileges on * . * to ‘drupal_user’;
	flush privileges;
	```

* Or - alternatevilly - do the same from within another container based on the mysql image:

	```shell
	docker run -it --link mysql-on-default:mysql --rm mysql/mysql-server:5.7 sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
	```
	
	(-rm automatically removes the container when it exists)
	
	(--link= adds a link to another container)
	
	
* Now let's start another container for [Drupal] (https://hub.docker.com/_/drupal/):

	```shell
	docker run --name drupal-on-dm-host -p 9080:80 --link mysql-on-dm-host:myysql -d drupal
	```
	
* Check the result:

	```shell
	docker ps 
	CONTAINER ID        IMAGE                    COMMAND                  CREATED             				STATUS              		PORTS                               				NAMES
	4e08530e6b4c        drupal                   "apache2-foreground"     2 minutes ago       			Up 2 minutes        	0.0.0.0:9080->80/tcp                			drupal-on-dm-host
	6a4a070f2bab        mysql/mysql-server:5.7   "/entrypoint.sh mysql"   46 minutes ago     	Up 46 minutes       	0.0.0.0:3306->3306/tcp, 33060/tcp   	mysql-on-dm-host
	```

* Add a port forwarding on order to access Drupal:

	![](PortForwardings.png)
	
* Visit the Drupal installation page, running in the Docker container: http://\<public_id_address_cloud_space>:9080.
* Specify the user you created in MySQL and make sure to specify the private IP address of your virtual machine hosting the docker machine:

	![](DrupalConfig.png)