# Nginx

## common diretories:
### /etc/nginx - installation and main config file (nginx.conf), where it is set the global settings, tunings, logs
```
----------
----------
http {
    ----------
    include conf.d/*.conf
}
```
http should have all virtual server configurations

### /etc/nginx/conf.d/ - virtual server configurations
virtualserver1.conf - should be named like www.site.com
```
server{
    listen <parameters>;
    location <url> {
        -------------
    }
}

upstream {
    ----------------
}
```
location is used to handle each request

### /var/log/nginx/
error.log Important operational messages
access.log record of each request

## key commands:
nginx -h - display help menu
nginx -t - Check if nginx conf is ok
nginx - s reload check if conf is ok and gracefully reload NGINX process
nginx -V detaoled information
nginx -T Dump full configuration 

## Simple virtual server
server{
    listen 80 default_server;
    server_name www.example.com;

    return 200;
}

server defines the context for a virtual server
listen specifies Ip/port for nginx should listen on. No ip means all ips
server_name spedifies hostname of virtual server
return tells nginx to respond directlyy to the request


