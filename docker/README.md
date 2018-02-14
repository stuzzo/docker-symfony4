## Installation

1. Go inside the docker folder and create a `.env` from the `.env.dist` file. Adapt it according to your symfony application

    ```bash
    cp .env.dist .env
    ```

2. Build/run containers with

    ```bash
    ./docker/commands/start
    ```

3. Update your system host file (add PROJECT_NAME.local)

    ```bash
    # UNIX only: get containers IP address and update host (replace IP according to your configuration)
    $ ./docker/commands/get-gateway-ip

    # unix only (on Windows, edit C:\Windows\System32\drivers\etc\hosts)
    $ sudo echo "IP PROJECT_NAME.local" >> /etc/hosts
    ```

    **Note:** For **OS X**, please take a look [here](https://docs.docker.com/docker-for-mac/networking/) and for **Windows** read [this](https://docs.docker.com/docker-for-windows/#/step-4-explore-the-application-and-run-examples) (4th step).
    
4. Use your app on http(s)://PROJECT_NAME.local and phpmyadmin on http://PROJECT_NAME.local:8080

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `docker-symfony_db`: This is the MySQL database container,
* `docker-symfony_php`: This is the PHP-FPM container in which the application volume is mounted,
* `docker-symfony_nginx`: This is the Nginx webserver container in which application volume is mounted too,
* `docker-symfony_phpmyadmin`: This is the PhpMyAdmin database container.

This results in the following running containers:

```bash
$ docker-compose ps
           Name                          Command               State              Ports            
--------------------------------------------------------------------------------------------------
docker-symfony_db             docker-entrypoint.s…             Up      3306/tcp      
docker-symfony_phpmyadmin     /run.sh phpmyadmin               Up      0.0.0.0:8080->80/tcp          
docker-symfony_nginx          nginx                            Up      0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp
docker-symfony_php            docker-php-entrypoi…             Up      9000/tcp      
```

## Useful commands

```bash
# Enter in ssh on php container
./docker/commands/ssh

# Read Logs for container
./docker/commands/read-logs-for-container CONTAINER_NAME

# Run composer install
./docker/commands/symfony-composer-install

# Set permission to cache and log folders
./docker/commands/symfony-permissions

# Check CPU consumption
docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Stop all containers
./docker/commands/stop

# Delete all project's containers
./docker/commands/remove-project-containers

# Delete all images and containers (ALERT: it deletes ALL images and containers, not only related to project)
./docker/commands/remove-project-containers
```

## TIPS

* If you set https on .env it will be created a self-signed certificate

* Use XDebug configuring your IDE to connect port `9000` and id key `PHPSTORM`
