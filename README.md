docker-plesk-traffic-monitoring-elastic
=======================================

This is a traffic monitoring project for Plesk into docker containers using Docker-compose.  

All of the Nginx access logs, whether from all domains, under domains from all subscriptions are retrieved from the folder `/var/www/vhosts/system/*/logs/proxy_access_ssl_log` and error logs from `/var/www/vhosts/system/*/logs/proxy_error_log`.  

We are also retrieving Mysql logs (errors / slow query) from `/var/log/mysql/error.log` and `/var/log/mysql/slow.log`

# Installation

This tutorial is for linux only, working on Ubuntu and Debian.

## Requirements

In order to run this project, you need to install `docker` and `docker-compose`.  
From Plesk UI, you can install Docker using extensions.  
Then, install docker-compose by running:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Configure Plesk a little bit

The basic Plesk configuration doesn't logs slow queries, and sometimes neither error logs for Mysql/MariaDB.  
In order to enable logging, edit the file `/etc/mysql/my.cnf` by adding under the [mysqld] section:
```
log_error = /var/log/mysql/error.log
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 2
```
Then restart your Mysql or MariaDB process:
```bash
# service mariadb restart
# service mysql restart
# service mysqld restart
```

## Run the project

First, you need to clone this repository
```bash
git clone https://github.com/faille76/docker-plesk-traffic-monitoring-elastic.git
```

Then, you will be able to run the project by running:
```bash
cd docker-plesk-traffic-monitoring-elastic
sudo docker-compose up -d
```

**If you use a firewall, don't forget to allow incoming traffic to port `5601`, you can do that using the Plesk UI.**


# How it works?

Here are the `docker-compose` built images:
* `elasticsearch`: The server for storing the web server logs.
* `kibana`: This is the UI that is used to render logs and create dashboards.
* `filebeat`: This is a tool that will read the logs from your file system and send them to elasticsearch. It will also configure multiple dashboard to kibana.  

This results in the following running containers:
```bash
> $ docker-compose ps
      Name                Command               State                 Ports
--------------------------------------------------------------------------------------------------------
elasticsearch   /usr/local/bin/docker-entr ...   Up      0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp
filebeat        /usr/local/bin/docker-entr ...   Up                                                    
kibana          /usr/local/bin/dumb-init - ...   Up      0.0.0.0:5601->5601/tcp
```


# Use Kibana

In order to visualize Nginx & MySQL logs, you can visit this url `http://<your-ip>:5601`


# Code license

You are free to use the code in this repository under the terms of the 0-clause BSD license. LICENSE contains a copy of this license.
