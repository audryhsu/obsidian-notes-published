---
creation date: 2022-04-18 17:48
aliases: 
tags: 🖥️
---

# [VSP Setup Walkthrough](VSP%20Setup%20Walkthrough.md)
---
Learned how to create a [virtual private server](./Virtual%20Private%20Server.md) and set it up, which is what [Heroku](./Heroku.md) does under the hood.

### Overview
We'll start from a server running nothing but Linux, deploy a simple web app built with Express to it, add Nginx (a powerful open-source web server) to route HTTP requests from the internet to the web app, point a domain name at our server so we can use a human-readable name to reach it instead of a long IP address, & finally configure SSL to secure traffic to and from our little website. All in less than hour thanks to great documentation & the wonderful, open-source tools available to us all!

#### Goals
- [Create a droplet VPS](https://docs.digitalocean.com/products/droplets/how-to/create/) on Digital Ocean's cloud and [setup the server](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04) 
	- Create a SSH key pair on my computer to connect to VPS
	- Create a super user that can SSH into the VPS
	- Set up a basic firewall
- [Configure a Firewall](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-20-04) to only allow SSH connections to talk to our VPS
- Registering our VPS IP address to our own domain name on the [DNS](./HTTP.md#^f731f6) through Google Domain
- [Create and configure Nginx](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)as a [reverse proxy](./Proxy%20Server.md#Reverse%20Proxy) to route traffic for our Express app
- [Use a process manager to run our Node application](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-18-04) 
- [Install free SSL certificates for our Nginx](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)proxy to encrypt HTTP traffic to be [HTTPS](./HTTP.md#Security%20Brief)!

###### Server Blocks
- update DNS records to use Digital Ocean's name servers and DNS management instead of Googles (since i own audryhsu.com)

Later, set up [server blocks](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04) (virtual hosts) to encapsulate configuration details and host more than one domain from a single server.
- create a directory structure within `/var/www` for our audryhsu.com site
- made a new configuration file  at `/etc/nginx/sites-available/audryhsu.com`
- enabled the file by creating a symlink from config file created above and the `etc/nginx/sites-enabled/`, where Nginx reads from during startup
- now we have two server blocks enabled and configured to respond to requests based on their `listen` and `server_name` directives
	- `audryhsu.com` will respond to requests for `audryhsu.com` and `www.audryhsu.com`
	- `default` will respond to any requests on port 80 that don't match the other two blocks

Nginx files and directories:
- Content stored in `/var/www/html`
- 
###### Postgres
- Installed [PostgreSQL](./PostgreSQL.md) on [droplet](https://www.digitalocean.com/community/tutorials/how-to-install-[postgresql](postgresql.md)-on-ubuntu-20-04-quickstart)
```
Success. You can now start the database server using:

    pg_ctlcluster 12 main start

Ver Cluster Port Status Owner    Data directory              Log file
12  main    5432 down   postgres /var/lib/postgresql/12/main /var/log/postgresql/postgresql-12-main.log
```
- created a new role `audry` that is a super user (postgres account is default)
	- good idea to match linux user and postgres user to use `ident`

##### Deploy DB app
Deploy another app on the VPS, but this app should be db-powered and connect to the Postgres db you just installed.
- git clone an app from github
- install the app dependencies (like pg)
- create a database, and schema
- create a .env file in your project folder (nano .env) with the following details:
```
DATABASE_URL="postgres://[username]:[password]@localhost:5432/[db-name]"
HOST="localhost"
PORT="3000"
```
- use the schema.sql file included in the git repo to set up the initial table in your db:
```
$ psql db-name < schema.sql
```

- you may need to edit a postgres file to allow local permissions:
- https://stackoverflow.com/a/69664941
- try node app.js to see if it starts running properly
- if so, you can reboot pm2 so it detects your new app.js file: 
`pm2 stop app.js > pm2 start app.js `

Update nginx to be the reverse proxy for both of the apps, the static site as well as this db-powered app.
- in `/etc/nginx/sites-available/audryhsu.com` :
- `location <route>` should be the main route of your apps
- set the port for each appropriately to the port each app is running on
![Pasted image 20220527171617.png](./images/Pasted%20image%2020220527171617.png)

Google around for instructions. This is not mandatory at all and just a fun bonus if you have time.
Note: your web server (nginx) should now be the dispatcher that routes requests to the appropriate application (because there are now two apps). It's getting more complex!

#### Technology used
- Digital Ocean Droplet - [Linux](./Linux.md) based 
- **UFW** (Uncomplicated Firewall) - simplified firewall management interface
- SSH (secure shell) is a secure way to connect to another computer's terminal. Encrypted network protocol to connect to remote computer. Since we're working in a remote VPS, we need to use SSH protocol to create a secure connection. 
- **Nginx** is one of the most popular open source software for web serving, reverse [proxy](./Proxy%20Server.md), caching, [load balancing](./Load%20Balancing.md), media streaming, etc.. 
	- We are using Nginx as a [reverse proxy](./Proxy%20Server.md#Reverse%20Proxy) (e.g. 🚦traffic light that directs HTTP requests to the right app on our VPS ) based on a Nginx config file
	- Our reverse proxy will also handle admin tasks like SSL certification to encrypt our HTTP traffic. (now my site gets the `https` honor badge!)
- **pm2** - daemon process manager to keep our Node app running on the VPS without us manually starting and monitoring app. Runs Node apps as a service.
	- Configured pm2 to run express app whenever droplet turns on and to restart if droplet crashes 
	- When we run `npm start` for express app, the application's life depends completely on our current terminal session remaining open. 

#### Usage
Log into droplet:
account: audry
`ssh audry@165.227.77.142`
password for ssh: regular!!

Once in the droplet, the account password for `audry` is `password`.

`systemctl status nginx` checks nginx web server status

### Walk through Instructions and Video
[Video 📽️](https://www.youtube.com/watch?v=rXnjyo2NIHI&t=30s)
Steps
1. Create a DO droplet (VPS) ![01 - Creating a Droplet on Digital Ocean.pdf](./images/01%20-%20Creating%20a%20Droplet%20on%20Digital%20Ocean.pdf)
2. Run a web server on a Droplet using domain name, Nginx, Certbot, PM2, and Express
![02 - Running a Web Server on a Droplet Using a Domain Name, Nginx, Certbot, PM2, & Express.pdf](./images/02%20-%20Running%20a%20Web%20Server%20on%20a%20Droplet%20Using%20a%20Domain%20Name,%20Nginx,%20Certbot,%20PM2,%20&%20Express.pdf)

---
Tags: [Cloud](./Cloud.md) - [Infrastructure](Infrastructure.md)

References: 

Related: 