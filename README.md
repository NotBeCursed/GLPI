# GLPI - IT Asset Management (Self-hosted)

This repository provides a Docker configuration to deploy **GLPI** (Gestionnaire libre de parc informatique), an open-source IT asset management, helpdesk, and ITSM solution. With this setup, you can quickly deploy a self-hosted instance of GLPI along with a MySQL database backend.

## Prerequisites

Before using this repository, ensure that you have the following installed on your system:

- Docker (latest version)
- Docker Compose (optional but recommended)

## Features

- Fully self-hosted GLPI application for managing IT assets, incidents, changes, and service requests.
- MySQL database integration for persistent data storage.
- Simple and easy deployment using Docker.
- Accessible via HTTP on port 80 (or other custom ports).
- Allows management of devices, software, licenses, and more.

## Installation

Follow these steps to deploy your own instance of **GLPI**:

### 1. Clone the repository

Clone this repository to your local machine:

```bash
git clone https://github.com/NotBeCursed/GLPI.git
cd GLPI
```

### 2. Modify configuration (optional)

If you want to change the configuration (e.g., ports, database credentials), you can modify the `docker-compose.yml` file. The most important settings are:

- **MYSQL_ROOT_PASSWORD**: The root password for MySQL.
- **MYSQL_DATABASE**: The name of the MySQL database to be created for GLPI.
- **MYSQL_USER**: The MySQL user for GLPI.
- **MYSQL_PASSWORD**: The password for the MySQL user.

Example of `docker-compose.yml` file:

```yaml
services:
  mysql:
    image: mysql:9.0.1
    container_name: glpi-db
    restart: unless-stopped
    hostname: mysql
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    networks:
      glpi-network:
        aliases:
          - db

  glpi:
    image: elestio/glpi:10.0.16
    container_name: glpi-app
    restart: unless-stopped
    hostname: glpi
    ports:
      - 80:80
    volumes:
      - glpi:/var/www/html/glpi
    environment:
      - TIMEZONE=Europe/Paris
    depends_on:
      - mysql
    networks:
      glpi-network:
        aliases:
          - app
```

### 3. Start the containers

Once the `docker-compose.yml` is configured to your liking, you can start the containers with Docker Compose:

```bash
docker-compose up -d
```

Alternatively, if you are using Docker without Compose, you can use individual `docker run` commands.

### 4. Access the GLPI application

After the containers are up and running, you can access **GLPI** via the web interface at:

```
http://localhost
```

By default, the application will be available on port 80. If you’ve changed the port in the `docker-compose.yml`, access it accordingly (e.g., `http://localhost:your-custom-port`).

### 5. GLPI Initial Setup

When you first access GLPI, you will be prompted to complete the installation:

1. Select the language and confirm the prerequisites.
2. Configure the database connection (the database information is already set in the `docker-compose.yml`).
3. Create the GLPI administrator account.

After completing the installation, you can start managing your IT assets, tickets, and services.

## Troubleshooting

If you encounter any issues, here are a few steps to help:

1. **Check logs**:
   To view logs for the GLPI container, use:

   ```bash
   docker logs glpi-app
   ```

2. **Check MySQL container logs**:
   If the MySQL service fails to start, view its logs with:

   ```bash
   docker logs glpi-db
   ```

3. **Permissions issues**:
   Ensure that the volumes for GLPI and MySQL have the correct file permissions. You can fix permissions with:

   ```bash
   sudo chown -R 1000:1000 glpi/
   sudo chown -R 1000:1000 db/
   ```

4. **Database connection errors**:
   If GLPI cannot connect to the database, double-check your `MYSQL_ROOT_PASSWORD`, `MYSQL_USER`, and `MYSQL_PASSWORD` environment variables in the `docker-compose.yml` file.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
