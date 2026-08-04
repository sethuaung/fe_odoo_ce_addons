# Odoo 19 with Docker

We can orchestrate the **Odoo** application and a PostgreSQL database container seamlessly using **Docker Compose**. [[1](https://www.youtube.com/watch?v=YxUIu2bLwn4), [2](https://deploymonkey.com/blog/install-odoo-19-community)]

## Quick Start Guide

### 1. Setup Project Structure

Create a dedicated project directory and folders for custom addons and configuration files to ensure persistent storage: [[1](https://cloudpepper.io/blog/how-to-install-odoo-with-docker-on-ubuntu-24-04/), [2](https://www.youtube.com/watch?v=YxUIu2bLwn4), [3](https://www.zehntech.com/how-to-configure-odoo-using-docker-compose-in-just-15-mins/), [4](https://www.cybrosys.com/blog/how-to-install-odoo-19-on-ubuntu-24-lts-server), [5](https://introserv.com/tutorials/odoo-19-in-docker-complete-installation-guide-for-ubuntu-24-04-community-and-enterprise/)]

bash

```
mkdir -p odoo19-docker/{addons,config,data,filestore}
cd odoo19-docker

```

### 2. Create the Docker Compose File [[1](https://introserv.com/tutorials/odoo-19-in-docker-complete-installation-guide-for-ubuntu-24-04-community-and-enterprise/)]

Create a file named `docker-compose.yml` in your project folder. Paste the following configuration, which leverages the official `odoo:19` image paired with a stable `postgres:16` database background: [[1](https://introserv.com/tutorials/odoo-19-in-docker-complete-installation-guide-for-ubuntu-24-04-community-and-enterprise/), [2](https://deploymonkey.com/blog/install-odoo-19-community), [3](https://cloudpepper.io/blog/how-to-install-odoo-with-docker-on-ubuntu-24-04/)]

[[1](https://introserv.com/tutorials/odoo-19-in-docker-complete-installation-guide-for-ubuntu-24-04-community-and-enterprise/), [2](https://deploymonkey.com/blog/install-odoo-19-community), [3](https://cloudpepper.io/blog/how-to-install-odoo-with-docker-on-ubuntu-24-04/)]

yaml

```
version: '3.8'

services:
  db:
    image: postgres:16
    environment:
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo_db_password
      - POSTGRES_DB=postgres
    volumes:
      - ./data:/var/lib/postgresql/data
    restart: always

  odoo:
    image: odoo:19
    depends_on:
      - db
    ports:
      - "8069:8069"
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=odoo_db_password
    volumes:
      - ./filestore:/var/lib/odoo
      - ./addons:/mnt/extra-addons
      - ./config:/etc/odoo
    restart: always

```

### 3. Launch the Stack

Run the following terminal command to download the required container images and initialize the services in detached (background) mode: [[1](https://www.youtube.com/watch?v=NSWkwNuRFGI), [2](https://deploymonkey.com/blog/install-odoo-19-community)]

bash

```
docker compose up -d

```

### 4. Complete Initial Setup [[1](https://introserv.com/tutorials/odoo-19-in-docker-complete-installation-guide-for-ubuntu-24-04-community-and-enterprise/)]

-   Open your preferred web browser and navigate to `http://localhost:8069`.

-   Provide a **Master Password** to protect your database cluster management panel.

-   Fill out the application details to generate your first active Odoo 19 instance. [[1](https://github.com/minhng92/odoo-19-docker-compose/blob/master/README.md), [2](https://www.youtube.com/watch?v=YxUIu2bLwn4), [3](https://deploymonkey.com/blog/install-odoo-19-community), [4](https://www.youtube.com/watch?v=NSWkwNuRFGI)]

## Key Operational Considerations

-   **Adding Custom Modules:** To install third-party applications, place your custom module folders into the local `./addons` folder. Afterward, log into Odoo, activate developer mode, and click **Update Apps List**. [[1](https://dev.to/adrianluong/setup-odoo-19-and-postgres-15-with-docker-45lg), [2](https://www.cybrosys.com/blog/how-to-install-a-custom-module-in-odoo-19), [3](https://www.packtpub.com/en-us/learning/how-to-tutorials/step-by-step-guide-to-creating-odoo-addon-modules?srsltid=AfmBOoqVl-uZKx1VpsszXCd4auxvMu6_A9t4gGlC_OcslDdG-vq10inV), [4](https://www.cybrosys.com/odoo-apps/)]

-   **Managing Container Logs:** To troubleshoot initialization scripts or monitor active worker processes, run the live log terminal stream:
    
   bash
    
    ```
    docker compose logs -f
    
    ```
-   **Correcting Permission Failures:** On Linux host operating systems, if Odoo throws access denied errors writing to mounted data volumes, change your folder permissions to match Odoo's internal container user ID:

   bash
    
    ```
    sudo chown -R 100:101 ./filestore ./addons ./config
    sudo chmod -R 755 ./filestore ./addons ./config
    
    ```
  
-   **Image Update Lag:** Official Docker Hub releases for `odoo:19` evaluate stable branches. If you need the bleeding-edge nightlies containing the latest upstream GitHub commits, consider building a custom local image pointing directly to the [Odoo GitHub Repository](https://github.com/odoo/odoo). [[1](https://www.odoo.com/forum/help-1/odoo-19-odoo-latest-on-docker-is-not-up-to-date-with-source-code-on-github-294408)]
