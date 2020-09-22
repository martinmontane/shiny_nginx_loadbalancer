# Shiny server in a NGINX Load Balancer

This folder holds a configuration of a nginx proxy server that functions as a load balancer to a number of dockerized Shiny Servers. The goal is to scale the limitations of 150 users being served in a single instance of Shiny Server. This part of the project relies heavily on [a project of ltorres](https://github.com/lrtorres/nginx-load-balanced-shinyserver-containers), although there are significant changes

## What's in the docker-compose

The docker-compose in this folder contains five services that communicate through an external docker network, which has to be created (the default name is nginx_network). Four services are different shiny servers, which are defined in the shiny-nodes folder, and the remaining one is a nginx proxy server that works as a load balancer, defined in the folder nginx-proxy.

If you create the docker network and execute docker-compose up -d you will have all the services up and running in a local docker network called **nginx_network**

### Where are the apps?

We are doing all of this because we want to host our shiny apps, so where are they? All shiny apps are stored in individual subfolders within the folder "AppsToPublish" in the shiny-nodes folder. There is an intro folder which holds the default Shiny App when creting a shiny project in RStudio. If you want to add an aditional app, you should just create a folder with a custom name and that's it.

### Where are the packages beign installed?

Our shiny apps would not be the same without our R packages. The Dockerfile in the shiny-nodes directory from lines 76 through 97 specifies the packages that will be installed in each of the shiny servers. Add whatever packages you want !

## Accessing the network

Ok, so port 80 of the nginx_network is hosting the load balancer that will gently deliver each user to a specific node. We need to communicate to this network from the outside. There are several ways to do it, in this project there is a nginxSSL that has everything to build a nginx server that communicates with the port 80 of the network and that communicates with SSL with the clients ! To do so, one has to modify the domain names that will be linked to the server in the **init-letsencrypt.sh** file and execute it: it will create dummy certificates for the domains, which are needed for the certbot to renew.

Once you did this, you just need to 'docker-compose up' the docker-compose file in the nginxSSL folder. Now you have everything working ! If you want to check if all containers are running, you should see something like the following if you execute *docker ps*
```
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS                  PORTS                                      NAMES
9b6a58b5b196        nginx:1.15-alpine      "/bin/sh -c 'while :…"   5 seconds ago       Up Less than a second   0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   nginxssl_nginx_1
5ab151d8431a        certbot/certbot        "/bin/sh -c 'trap ex…"   5 seconds ago       Up Less than a second   80/tcp, 443/tcp                            nginxssl_certbot_1
0ddad2f1f854        nginxlb_loadbalancer   "nginx"                  8 weeks ago         Up 8 weeks              127.0.0.1:8080->80/tcp                     nginxlb_loadbalancer_1
df7f6dfbc7fa        nginxlb_shiny-proxy2   "/bin/sh -c '/usr/bi…"   8 weeks ago         Up 8 weeks              3838/tcp                                   nginxlb_shiny-proxy2_1
1788fc724ecf        nginxlb_shiny-proxy3   "/bin/sh -c '/usr/bi…"   8 weeks ago         Up 8 weeks              3838/tcp                                   nginxlb_shiny-proxy3_1
b4ff4f50cdd9        nginxlb_shiny-proxy4   "/bin/sh -c '/usr/bi…"   8 weeks ago         Up 8 weeks              3838/tcp                                   nginxlb_shiny-proxy4_1
745ff900ed36        nginxlb_shiny-proxy1   "/bin/sh -c '/usr/bi…"   8 weeks ago         Up 8 weeks              3838/tcp                                   nginxlb_shiny-proxy1_1
```
If you already have a server that has its own nginx, you might want to add the nginx_network to it.

## Enjoying it

Go to yourdomain.com/intro and see how it well it works ! 
