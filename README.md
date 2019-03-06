# MSP Portal

The product is directed to Managed Service Providers who are maintaining multiple FileWave Server instances in their environments.

The MSP Portal currently allows an administrator to:
* Catalog FileWave Servers.
* Monitor the status and display FileWave Server revision.
* Launch FileWave Admin directly from the MSP Portal.

## Getting Started

These instructions will help you deploy the project to production environment.

### Prerequisites

* Basic knowledge and hands-on experience with Docker and Docker Compose.
* Machine in your data center running up-to-date Docker and Docker Compose.
```
$ docker --version
Docker version 18.09.3, build 774a1f4

$ docker-compose --version
docker-compose version 1.23.2, build 1110ad01
```
* Location on this machine where internal database and log files will be persisted (later referred as `MSP_PORTAL_DB_HOST_LOCATION`).
* External, off the machine backup of `MSP_PORTAL_DB_HOST_LOCATION` contents.
* (Optional) SSL certificate matching machine's host name in order to enable HTTPS communication.

### Important notes
* Minimum support version of FileWave Server that is able to provide online status and its current version is 13.0.0.
* Launching FileWave Admin from the MSP Portal will only work with compatible versions of FileWave Servers. FileWave Admin installed on your machine must be compatible with FileWave Server you're trying to connect to. Minimum supported version of FileWave Admin for this feature is 12.9.0.
* Enabling Kubernetes on the machine hosting the portal will very likely break the networking inside portal's containers. Please ensure that Kubernetes is disabled.

### Installing

Following steps must be executed only once, during initial deployment of the product. For upgrade instruction please refer to 'Upgrading' section.

* Download and unpack the newest [release](https://github.com/fw-dev/msp_portal/releases) of MSP Portal.
* Ensure that `MSP_PORTAL_DB_HOST_LOCATION` location exists on the machine which will host the portal.
* Go to the location where MSP Portal release is unpacked.
* Open `.env` file in text editor and provide following required configuration options.
  * `MSP_PORTAL_DB_HOST_LOCATION`
  * `MSP_PORTAL_HOSTNAME`
  * `MSP_PORTAL_DJANGO_SECRET_KEY`
* Start the product by executing `docker-compose -f docker-compose.yml -f docker-compose_production.yml up -d` command.
```
$ docker-compose -f docker-compose.yml -f docker-compose_production.yml up -d
Pulling gunicorn (filewave/msp_portal:dev-0.1.0)...
dev-0.1.0: Pulling from filewave/msp_portal
6c40cc604d8e: Already exists
eb28c72fd5c9: Pull complete
8b7b7e8a3ec6: Pull complete
07400c149ca6: Pull complete
93b52b0b4673: Pull complete
3264d4524802: Pull complete
18b563dda86d: Pull complete
3f028c2ae280: Pull complete
8f1b448145a6: Pull complete
Pulling rqworker (filewave/msp_portal:dev-0.1.0)...
dev-0.1.0: Pulling from filewave/msp_portal
Creating msp_portal_redis_1 ... done
Creating msp_portal_rqscheduler_1 ... done
Creating msp_portal_rqworker_1    ... done
Creating msp_portal_gunicorn_1    ... done
```
* Create super user account.
  * Open shell in `msp_portal_gunicorn_1` container by executing `docker-compose exec gunicorn /bin/sh` command.
  ```
  $ docker-compose exec gunicorn /bin/sh
  /app #
  ```
  * Execute `python /app/msp_portal/manage.py createsuperuser` command and follow prompts.
  ```
  /app # python /app/msp_portal/manage.py createsuperuser
  Username (leave blank to use 'root'): fwadmin
  Email address:
  Password:
  Password (again):
  Superuser created successfully.
  ```
* Test deployment entering hostname (`MSP_PORTAL_HOSTNAME`) and port 8000 in your browser (e.g. http://192.168.0.102:8000/). Please note that this is `http` not `https`. Log in with super user credentials.

Congratulations! You successfully finished the deployment.

### Configuring HTTPS

Please remember! Certificate must be matching machine's hostname.

In order to configure HTTPS for communication between the portal and your browser you need to copy private key and public key certificate files to `MSP_PORTAL_DB_HOST_LOCATION` and configure filenames in `.env` file.
* `MSP_PORTAL_KEYFILE` must be PKCS#8 in PEM format.
* `MSP_PORTAL_CERTFILE` must be X.509 Certificate in PEM format.

If your deployment is already running you need to restart it by executing `docker-compose down` and `docker-compose -f docker-compose.yml -f docker-compose_production.yml up -d` commands.

You can test HTTPS configuration by entering hostname (`MSP_PORTAL_HOSTNAME`) and port 8000 in your browser using HTTPS schema (e.g. https://192.168.0.102:8000). Please note that this is `https` not `http`.

## Upgrading

This section will describe steps to perform to upgrade from one version to another.

## Frequently Asked Questions (FAQ)
Q: Why are we creating MSP Portal?  
A: FileWave would like to improve the ability of managed service providers to sell their solutions to their end customers.

Q: Who can use it?  
A: The intent is to target Managed Service Providers, but there isn’t a technical limitation that enforces this. Anyone who needs to handle multiple FileWave instances can benefit from using the portal.

Q: How do I add my FileWave Servers to the portal?  
A: Go to **_Servers_** and press **_Add Server_**.
