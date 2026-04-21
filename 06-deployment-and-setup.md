# Deployment & Setup

Deploy the OpenCourseMatch application on your own server and customize it to your needs

## General Information

OpenCourseMatch is a web application that is hosted within a Docker container.
This means that you can host it on any server that supports Docker, regardless of the underlying operating system.
Besides the application itself, the production environment also requires a MySQL database to persist data.
To simplify the deployment process, it is therefore highly recommended to use Docker Compose, and in the following sections, it is also assumed that you are using it.

## Configuration File

The application is configured using a separate JSON file, which is mounted into the Docker container.
You can find an example of the latest version of this configuration file [here](https://github.com/OpenCourseMatch/OpenCourseMatch/blob/main/config/config.json.dist).
It is recommended to copy this file to your system and adjust the values according to your needs:

| Parameter       | Description                                                                                  | Recommendation                                                                                                                               |
|-----------------|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| `APP_URL`       | The URL under which the application will be accessible                                       | Your server's domain name                                                                                                                    |
| `PRODUCTION`    | Whether the application is running in production mode (`true`) or development mode (`false`) | `true`                                                                                                                                       |
| `LOG_RECPIENTS` | A comma-separated list of email addresses that will receive error logs                       | If you have configured the SMTP settings, you can specify the addresses here, otherwise, leave it empty                                      |
| `LOG_LEVEL`     | The level of logging                                                                         | `6` (logs everything)                                                                                                                        |
| `DB_HOST`       | The hostname of the MySQL database                                                           | `database` (for all Docker Compose setups provided here)                                                                                     |
| `DB_USER`       | The username for the MySQL database                                                          | Should be customized for security, but make sure to also adjust it in the `docker-compose.yml` file                                          |
| `DB_PASS`       | The password for the MySQL database                                                          | Should be customized for security, but make sure to also adjust it in the `docker-compose.yml` file                                          |
| `DB_NAME`       | The name of the MySQL database                                                               | `opencoursematch` (for all Docker Compose setups provided here                                                                               |
| `DB_ENABLED`    | Whether the database is enabled                                                              | Must be `true`                                                                                                                               |
| `SMTP_HOST`     | The hostname of the SMTP server                                                              | If you want to receive error logs via email, specify the hostname of your SMTP server here, otherwise, leave it empty                        |
| `SMTP_PORT`     | The port of the SMTP server                                                                  | If you want to receive error logs via email, specify the port of your SMTP server here, otherwise, leave it empty                            |
| `SMTP_USER`     | The username for the SMTP server                                                             | If you want to receive error logs via email, specify the username for your SMTP server here, otherwise, leave it empty                       |
| `SMTP_PASS`     | The password for the SMTP server                                                             | If you want to receive error logs via email, specify the password for your SMTP server here, otherwise, leave it empty                       |
| `SMTP_SECURE`   | Whether to use a secure connection for the SMTP server (`true` or `false`)                   | If you want to receive error logs via email, specify whether to use a secure connection for your SMTP server here, otherwise, leave it empty |
| `SMTP_AUTH`     | Whether to use authentication for the SMTP server (`true` or `false`)                        | If you want to receive error logs via email, specify whether to use authentication for your SMTP server here, otherwise, leave it empty      |


## Minimal Example

This setup shows a minimal working example of how to deploy the OpenCourseMatch application using Docker Compose.

<details>
<summary>Click to expand</summary>

### File Structure

```
📁 .
├── 📁 app-files
├── 📁 app-logs
├── 📁 config
│   └── 📄 config.json
├── 📁 database-backup
└── 📄 docker-compose.yml
```

### docker-compose.yml

```yaml
volumes:
    database:
        driver: "local"

networks:
    app:

services:
    database:
        image: "mysql:8.2"
        restart: "always"
        healthcheck:
            test: [ "CMD", "mysqladmin", "ping" ]
            interval: 5s
            timeout: 3s
            retries: 5
        environment:
            TZ: "UTC"
            MYSQL_ROOT_PASSWORD: "root" # ADJUST
            MYSQL_DATABASE: "opencoursematch" # ADJUST
            MYSQL_USER: "opencoursematch" # ADJUST
            MYSQL_PASSWORD: "opencoursematch" # ADJUST
        volumes:
            - "database:/var/lib/mysql"
        networks:
            - "app"

    app:
        image: "ghcr.io/opencoursematch/opencoursematch:latest" # ADJUST
        restart: "always"
        depends_on:
            database:
                condition: service_healthy
        environment:
            TZ: "UTC"
        volumes:
            - "./config:/app/config"
            - "./app-logs:/app/logs"
            - "./app-files:/app/files"
        ports:
            - "80:80"
        networks:
            - "app"

    backup:
        image: "ghcr.io/struktal/mysql-backup:latest"
        restart: "always"
        environment:
            TZ: "UTC"
            DB_HOST: "database"
            DB_USER: "opencoursematch" # ADJUST
            DB_PASS: "opencoursematch" # ADJUST
            DB_DATABASE: "opencoursematch" # ADJUST
        volumes:
            - "./database-backup:/backup"
```

</details>

## Customize Index Page

The application's index page is a simple Markdown file that can be easily customized to your needs.
You can create your own `index.md` file and mount it into the container to replace the default one, and to write your own welcome message, user instructions, or anything else you want to display on the index page.

<details>
<summary>Click to expand</summary>

### File Structure

```
📁 .
├── 📁 app-files
├── 📁 app-logs
├── 📁 config
│   └── 📄 config.json
├── 📁 database-backup
├── 📄 docker-compose.yml
└── 📄 index.md
```

### docker-compose.yml

```yaml
volumes:
    database:
        driver: "local"

networks:
    app:

services:
    database:
        image: "mysql:8.2"
        restart: "always"
        healthcheck:
            test: [ "CMD", "mysqladmin", "ping" ]
            interval: 5s
            timeout: 3s
            retries: 5
        environment:
            TZ: "UTC"
            MYSQL_ROOT_PASSWORD: "root" # ADJUST
            MYSQL_DATABASE: "opencoursematch" # ADJUST
            MYSQL_USER: "opencoursematch" # ADJUST
            MYSQL_PASSWORD: "opencoursematch" # ADJUST
        volumes:
            - "database:/var/lib/mysql"
        networks:
            - "app"

    app:
        image: "ghcr.io/opencoursematch/opencoursematch:latest" # ADJUST
        restart: "always"
        depends_on:
            database:
                condition: service_healthy
        environment:
            TZ: "UTC"
        volumes:
            - "./config:/app/config"
            - "./app-logs:/app/logs"
            - "./app-files:/app/files"
            - "./index.md:/app/src/static/md/index.md"
        ports:
            - "80:80"
        networks:
            - "app"

    backup:
        image: "ghcr.io/struktal/mysql-backup:latest"
        restart: "always"
        environment:
            TZ: "UTC"
            DB_HOST: "database"
            DB_USER: "opencoursematch" # ADJUST
            DB_PASS: "opencoursematch" # ADJUST
            DB_DATABASE: "opencoursematch" # ADJUST
        volumes:
            - "./database-backup:/backup"
```

</details>

## Custom SSL Certificates

By default, the application is configured to provide only an HTTP endpoint, which would then be accessible via port 80 to an external reverse proxy.
However, if you want to provide an HTTPS endpoint directly from the application container, you can do so by providing your own SSL certificates and adjusting the Nginx configuration accordingly.

<details>
<summary>Click to expand</summary>

### File Structure

```
📁 certificates
├── 📄 certificate.crt
└── 📄 server.key
📁 .
├── 📁 app-files
├── 📁 app-logs
├── 📁 config
│   └── 📄 config.json
├── 📁 database-backup
├── 📄 docker-compose.yml
└── 📁 sites
    └── 📄 site
```

### sites/site

```
server {
    listen 80;
    listen [::]:80;
    
    root /app/public;
    
    index index.php index.html index.htm;
    
    location ~* /static/ {
        rewrite ^(.*)/static/(.*)$ /static/$2 break;
    }
    
    location /deploy {
        rewrite ^(.*)/deploy$ /deployment/deploy.php last;
    }
    
    location / {
        rewrite ^/(.*)$ /front-controller.php last;
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass 127.0.0.1:9000;
    }
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    
    ssl_certificate /ssl-certificates/certificate.crt;
    ssl_certificate_key /ssl-certificates/server.key;
    
    root /app/public;
    
    index index.php index.html index.htm;
    
    location ~* /static/ {
        rewrite ^(.*)/static/(.*)$ /static/$2 break;
    }
    
    location /deploy {
        rewrite ^(.*)/deploy$ /deployment/deploy.php last;
    }
    
    location / {
        rewrite ^/(.*)$ /front-controller.php last;
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass 127.0.0.1:9000;
    }
}
```

### docker-compose.yml

```yaml
volumes:
    database:
        driver: "local"

networks:
    app:

services:
    database:
        image: "mysql:8.2"
        restart: "always"
        healthcheck:
            test: [ "CMD", "mysqladmin", "ping" ]
            interval: 5s
            timeout: 3s
            retries: 5
        environment:
            TZ: "UTC"
            MYSQL_ROOT_PASSWORD: "root" # ADJUST
            MYSQL_DATABASE: "opencoursematch" # ADJUST
            MYSQL_USER: "opencoursematch" # ADJUST
            MYSQL_PASSWORD: "opencoursematch" # ADJUST
        volumes:
            - "database:/var/lib/mysql"
        networks:
            - "app"

    app:
        image: "ghcr.io/opencoursematch/opencoursematch:latest" # ADJUST
        restart: "always"
        depends_on:
            database:
                condition: service_healthy
        environment:
            TZ: "UTC"
        volumes:
            - "./config:/app/config"
            - "./sites:/etc/nginx/sites"
            - "../certificates:/ssl-certificates"
            - "./app-logs:/app/logs"
            - "./app-files:/app/files"
        ports:
            - "80:80"
        networks:
            - "app"

    backup:
        image: "ghcr.io/struktal/mysql-backup:latest"
        restart: "always"
        environment:
            TZ: "UTC"
            DB_HOST: "database"
            DB_USER: "opencoursematch" # ADJUST
            DB_PASS: "opencoursematch" # ADJUST
            DB_DATABASE: "opencoursematch" # ADJUST
        volumes:
            - "./database-backup:/backup"
```

</details>
