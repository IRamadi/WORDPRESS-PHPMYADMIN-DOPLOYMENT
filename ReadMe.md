# Database
MYSQL_ROOT_PASSWORD=secure_root_password
# WordPress Docker Stack

## Overview

This project provides a complete, production-ready Docker-based stack for running WordPress. It includes WordPress with PHP-FPM, a MariaDB database, phpMyAdmin for database management, and Nginx as the web server. The stack is configured with proper health checks, logging, basic security measures, file persistence, and uses environment variables for easy configuration.

## Features

*   **WordPress with PHP-FPM:** The latest version of WordPress running on a high-performance PHP-FPM server.
*   **MariaDB Database:** A robust and reliable MariaDB database for your WordPress installation.
*   **phpMyAdmin:** A web-based interface for managing your MariaDB database.
*   **Nginx:** A high-performance web server configured to serve your WordPress site.
*   **Proper Health Checks:** Health checks are configured for all services to ensure they are running correctly.
*   **Logging Configuration:** Centralized logging for all services to easily debug and monitor the application.
*   **Basic Security Measures:** Includes basic security measures like securing phpMyAdmin with a password.
*   **File Persistence:** WordPress content and database files are persisted to the host machine to prevent data loss.
*   **Environment Variables:** All configuration is done through environment variables for easy setup and management.

## Prerequisites

*   Docker
*   Docker Compose

## Project Structure

```
wordpress-stack/
├── .env
├── docker-compose.yml
├── nginx-conf/
│   └── wordpress.conf
├── logs/
│   └── nginx/
├── db-backups/
└── wp-content/
```

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd wordpress-stack
```

### 2. Create Project Structure

```bash
mkdir -p wordpress-stack/{nginx-conf,logs/nginx,db-backups,wp-content}
cd wordpress-stack
touch .env docker-compose.yml
mkdir -p nginx-conf && touch nginx-conf/wordpress.conf
```

### 3. Configure Environment Variables

Create a `.env` file in the root of the project and add the following environment variables:

```
# WordPress
WORDPRESS_DB_HOST=mariadb
WORDPRESS_DB_USER=wordpress
WORDPRESS_DB_PASSWORD=wordpress
WORDPRESS_DB_NAME=wordpress

# MariaDB
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress
MYSQL_PASSWORD=wordpress

# phpMyAdmin
PMA_HOST=mariadb
PMA_PORT=3306
```

### 4. Start the Stack

```bash
docker-compose up -d
```

### 5. Set Permissions

After the first run, set the proper permissions for the WordPress files:

```bash
docker-compose exec wordpress chown -R www-data:www-data /var/www/html
```

## Post-Installation Steps

### Secure phpMyAdmin

For security, it is recommended to protect your phpMyAdmin installation with a username and password.

1.  **Create a `htpasswd` file for phpMyAdmin:**

    ```bash
    docker-compose exec nginx htpasswd -c /etc/nginx/.htpasswd admin
    ```

2.  **Update the Nginx configuration to add authentication.**

    Open `nginx-conf/wordpress.conf` and add the following location block:

    ```nginx
    location /phpmyadmin {
        auth_basic "Admin Login";
        auth_basic_user_file /etc/nginx/.htpasswd;
        proxy_pass http://phpmyadmin;
        # ... rest of the proxy settings
    }
    ```

3.  **Reload Nginx:**

    ```bash
    docker-compose exec nginx nginx -s reload
    ```

## Logging

Logs for all services are centralized and can be viewed using the following command:

```bash
docker-compose logs
```

## Health Checks

Health checks are configured for all services in the `docker-compose.yml` file. You can check the status of the services using the following command:

```bash
docker-compose ps
```

## Data Persistence and Backups

*   **WordPress Content:** The `wp-content` directory is mounted as a volume to the host machine, so you won't lose your themes, plugins, and uploads when you restart the containers.
*   **Database:** The MariaDB database files are stored in a named volume. Backups of the database are stored in the `db-backups` directory.

## Contributing

Contributions are welcome! Please feel free to submit a pull request.

## License

This project is licensed under the MIT License.



MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress_user
MYSQL_PASSWORD=secure_wordpress_password

# WordPress
WORDPRESS_DB_HOST=db:3306
WORDPRESS_DB_USER=wordpress_user
WORDPRESS_DB_PASSWORD=secure_wordpress_password
WORDPRESS_DB_NAME=wordpress

# phpMyAdmin
PMA_HOST=db
PMA_USER=wordpress_user
PMA_PASSWORD=secure_wordpress_password
PMA_ABSOLUTE_URI=/phpmyadmin