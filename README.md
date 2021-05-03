# PHP + NGINX + MYSQL + POSTGRES + DOCKER

It is a simple setup to PHP Developers build their applications using PHP in Docker environment.

## How to use:

 - Clone or [download](https://github.com/sneycampos/php-nginx-mysql-docker/archive/refs/heads/master.zip) this repository and put into your project root folder.
 > e.g: 
 > project-folder/docker
 - Change the COMPOSE_PROJECT_NAME variable into the .env file to your project name
>  (it will be used in your container names, e.g: project1-nginx, project1-mysql)

- Using terminal, enter into the "docker" folder and run `docker-compose up -d` 
