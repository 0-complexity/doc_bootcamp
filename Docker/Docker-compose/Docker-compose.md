## Using Docker-compose

```yaml
# Files
www_files:
  image: drupal
  volumes:
    - drupalfiles:/var/www
  #command: /bin/true

# PHP Web Server
web:
  # Build the image off of Dockie
  # https://github.com/robloach/dockie
  image: dockie/dockie
  # Environment variables
  environment:
    # Drupal settings
    DRUPAL_PROFILE: standard
    DRUPAL_SITE_NAME: Drupal
    DRUPAL_USER: admin
    DRUPAL_PASS: admin
    DRUPAL_DBURL: mysql://drupal:drupal@mysql:3306/drupal
  ports:
    # Host machine's port 8000 will map to Drupal's port 80
    - "8000:80"
  volumes:
    # Drupal should live in the "drupal" directory
    - drupalfiles:/app
    #- ./docker-compose.sh:/docker-compose.sh
  links:
    - mysql:mysql
    - www_files
  command: php -S 0.0.0.0:80 -t /app/html
  #command: php -S 0.0.0.0:80 -t /var/www/html
  working_dir: /app
  #working_dir: /var/www
  restart: always

# MySQL Server
mysql:
  #image: mysql:5.7
  build: .
  environment:
    MYSQL_USER: drupal
    MYSQL_PASSWORD: drupal
    MYSQL_DATABASE: drupal
    MYSQL_ROOT_PASSWORD: ''
    MYSQL_ALLOW_EMPTY_PASSWORD: 'yes'
  #volumes:
  #  - ./my.cnf:/etc/mysql/my.cnf
  expose:
    - "3306"
  restart: always
  ```
  
The Dockerfile wich is used here:
  
```
FROM mysql:5.7
MAINTAINER yves.kerwyn@greenitglobe.com

COPY ./my.cnf /etc/mysql/my.cnf
```

The my.cnf has the following extra entry in the section `[mysqld]`:
```shell
max_allowed_packet=16M
```