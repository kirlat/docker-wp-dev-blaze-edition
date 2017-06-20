# Docker WP Dev Blaze Edition
Development version of a Docker WordPress image, with Xdebug, WP CLI, and ZipArchive.

This image is an extension of a 'stock' WordPress image (<https://hub.docker.com/_/wordpress/>) with an addition of components that can be helpful in WordPress development.

It is based on a latest WordPress php7.1-apache image. It adds the following additional components to bare necessities of a base image :

* Xdebug
* WP CLI
* ZipArchive PHP extension


## Docker Compose File
A sample Docker Compose file for WP Dev Blaze Edition may look like this:
```
	version: '2'

	services:
	   db:
	     image: mysql:5.7
	     ports:
	      - "9001:3306"
	     volumes:
	       - ./database:/var/lib/mysql
	     restart: always
	     environment:
	       MYSQL_ROOT_PASSWORD: somewordpress
	       MYSQL_DATABASE: wordpress
	       MYSQL_USER: wordpress
	       MYSQL_PASSWORD: wordpress

	   wordpress:
	     depends_on:
	       - db
	     # image: wordpress:php7.1-apache
	     image: wp-dev-blaze-edition:latest
	     volumes:
	       - ./wordpress:/var/www/html
	     ports:
	       - "8001:80"
	     restart: always
	     environment:
	       WORDPRESS_DB_HOST: db:3306
	       WORDPRESS_DB_USER: wordpress
	       WORDPRESS_DB_PASSWORD: wordpress
	       XDEBUG_CONFIG: "remote_host=xxx.xxx.xxx.xxx"
	       PHP_IDE_CONFIG: "serverName=YourServerName"
```
It's pretty much the same Compose file that is used for a 'stock' WordPress immage, but with some minor additions. Because it changes (adds, in fact) so little, it can be used with the standard WordPress image as well. Please check a second 'image' entry for the WordPress configuration that is commented out. It will allow to switch between regular and developer versions of WordPress easily.

Here is what was added to the standard Compose file. It will be used by WP Dev Blaze Edition only and wil be ignored by the standard WordPress image.

### Environment variables
`XDEBUG_CONFIG: "remote_host=xxx.xxx.xxx.xxx"` - part of Xdebug configuration. Should have an IP of your machine instead of xxx.xxx.xxx.xxx. It should be your local network address, not '127.0.0.1' or 'localhost'. On Windows, you can check it with 'ipconfig /all' command.

`PHP_IDE_CONFIG: "serverName=YourServerName"` - part of Xdebug configuration. YourServerName should be the name of a debugger configuration as specified in an IDE. For PhpStorm IDE, for example, this should be a server name specified in `Settings - Languages & Frameworks - PHP - Servers`.

## WordPress
WordPress is installed into `/var/www/html`.

## Database
This image has been tested with MySQL (because it still represents a majority of installations), but should work with MariaDB. It does not change anything within the parent WordPress image about database interaction, so please refer to parent WordPress documentation (<https://hub.docker.com/_/wordpress/>) if you will need any additional information.

## WP CLI
A WP CLI command can be run from a Docker Compose directory as:
	`docker-compose exec wordpress wp user list --allow-root`

where 'user list' is a WP CLI command. `--allow-root` option is required because WP CLI commands will be executed as root. But that should not be a big issue within a development container.
