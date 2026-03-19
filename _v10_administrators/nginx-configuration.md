---
title: Setting up NGINX
tagline: A guide to configuring an NGINX to work with Materia
class: admin
category: first_time
---

# Production-Ready NGINX Configuration

## Features
* Options for hosting behind a load balancer like (AWS ALB)
* HTTP redirects to HTTPS
* FuelPHP Environment set to `production`
* sendfile enabled for speedy media delivery
* gzip enabled for faster downloads
* php ini settings: Max Upload set to 50MB for widget package uploading
* blocking web access to common files of concern
* works as both a static domain and non-static

## nginx.conf template

We run a single server dedicated to Materia. As such, we prefer to simplify configuration by replacing nginx.conf rather than using the fancy sites-enabled method. It should be fine to run in a multi-tenant environment, but the configuration below will take a few extra steps to conform to that.

Search for 'SETUP' and update settings as needed.  The config below assumes you're running a static asset domain and https on both domains.  Adjust as needed.

``` nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /tmp/nginx.pid;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    log_format slim '[$time_local] "$request_method $request_uri" $status - $remote_addr';

    # choose main or slim formats
    access_log  /var/log/nginx/access.log  slim;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    gzip on;
    gzip_disable "msei6";
    gzip_http_version 1.1;
    gzip_comp_level 5;
    gzip_min_length 256;
    gzip_proxied any;
    gzip_vary on;
    gzip_types
        application/javascript
        application/json
        application/vnd.ms-fontobject
        application/x-font-ttf
        application/x-web-app-manifest+json
        application/xhtml+xml
        application/xml
        font/opentype
        image/svg+xml
        image/x-icon
        text/css
        text/javascript
        text/xml
        text/plain;

    include        /etc/nginx/mime.types;
    default_type   application/octet-stream;
    index          index.html index.htm;

    ## SETUP OPTIONAL
    ## enable if https is not handled at a load balancer
    ## if using a single SAN cert for both domains, you can set it above the
    ## server blocks to simplify config (both will share these settings)
    ## !NOTE!: SSL configs needs to conform to your needs and change with time
    ## These options are simply a placeholder and are likely outdated
    ## an excellent place to start: https://ssl-config.mozilla.org/
    #
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers off;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;
    ssl_certificate     /etc/nginx/conf.d/cert.pem;
    ssl_certificate_key /etc/nginx/conf.d/key.pem;


    ## SETUP OPTIONAL
    ## Add a domain to host your static files
    ## Helps isolate scripts and assets contained in 3rd party widgets
    ## Can also be used to speed up materia with a CDN caching layer
    ## Materia can operate without, all static asset requests will
    ## simply go to the main app server domain

    ## Static Assets (including widgets)
    server{
        listen 80;
        listen [::]:80;

        ## SETUP OPTIONAL
        ## enable if https is not handled at a load balancer
        ## You may want to add cert options, like ssl_certificate, above server {} if they can be shared with the app server
        listen *:443 ssl;
        listen [::]:443 ssl;

        ## SETUP
        ## make sure this matches the domain for your static assets
        ## this domain should match /config/materia.php urls
        ## for 'static' and 'engines'
        server_name  static-materia.your-org.edu;

        ## SETUP MUST SET ROOT
        ## The root directory should be configured to match the public directory's volume mount location
        root /var/www/html/public;

        ## Redirect to https if non-https request received
        if ($scheme = http) {
            return 301 https://$host$request_uri;
        }

        ## ONLY return files for directories where static files are hosted
        ## The reason for this - is you may put a cache layer in front
        ## of the static domain.  This will prevent the dynamic web app
        ## parts of Materia from being cached int a scary way
        location ~* ^/(?:js|css|img|widget|default-creator|dist) {
            # this will simply pass the request to the application server
            # which will help us reuse all the configuration we have set up there
            proxy_pass https://$server_addr$request_uri;
        }

        ## 404 for everything that isn't static
        ## this doesn't yet show the nice materia 404 page
        location / {
            return 404;
        }
    }

    # Main Application Server
    server {
        listen 80;
        listen [::]:80;

        ## SETUP OPTIONAL
        ## enable if https is not handled at a load balancer
        ## You may want to add cert options, like ssl_certificate, above server {} if they can be shared with the static server
        #
        listen *:443 ssl;
        listen [::]:443 ssl;

        server_name _;

        server_tokens off;

        ## send 404 and 405 pages to the backend
        error_page 404 = @handler;
        error_page 405 = @handler;

        ## SETUP OPTIONAL
        ## Forward ip addresses from a load balancer
        #
        # set_real_ip_from  192.168.1.0/24; # which servers do you trust to provide real replacement ip info
        # real_ip_header    X-Forwarded-For;
        # real_ip_recursive on;

        ## SETUP OPTIONAL
        ## HTTPS terminating at a load balancer?
        ## requires LB to send X-Forwarded-Proto header
        ## Redirect to https if non-https request received
        #
        # if ($http_x_forwarded_proto = "http") {
        #    return 301 https://$host$request_uri;
        # }

        ## SETUP OPTIONAL
        ## HTTPS terminating here at nginx?
        ## comment out if https is terminating at a load balancer
        ## Redirect to https if non-https request received
        #
        if ($scheme = http) {
            return 301 https://$host$request_uri;
        }

        ## SETUP MUST SET ROOT
        ## The root directory should be configured to match the public directory's volume mount location
        root /var/www/html/public;

        index index.php index.html index.htm;

        ## Note that the logs are buffered
        ## output may be delayed slightly if you're waiting for it
        access_log /var/log/nginx/access.log combined buffer=32k;
        error_log /var/log/nginx/error.log;

        ## Increase max body size to 50m for media uploads
        client_max_body_size 50m;

        ## block access to dot files
        location ~ /\. {
            deny all;
            log_not_found off;
        }

        ## block access to .composer files
        location ~ composer\..* {
            deny all;
            log_not_found off;
        }

        ## block access to node_modules
        location ~ node_modules {
            deny all;
            log_not_found off;
        }

        ## SETUP OPTIONAL
        ## restrict access to admin interfaces from certain ip ranges
        #
        # location ^~ /admin {
        #     allow  192.168.1.0/24
        #     deny all;
        # }

        ## try static files first, then directory, then fall back to @handler
        location / {
            try_files   $uri $uri/ @handler;
            expires     30d;
        }

        ## funnel requests into index.php (with the request uri becoming a get param ?some/directory/thing)
        location @handler {
            rewrite ^ /index.php?/$request_uri;
        }

        ## handle all requests to index.php
        location ~ ^/index.php$ {
            try_files $uri =404;

            fastcgi_split_path_info ^(.+\.php)(/.+)$;

            ## SETUP
            ## note that your phpfpm socket may not be at this location
            ## especially for multi-tenant uses
            fastcgi_pass        unix:/var/run/php/php-fpm.sock;
            fastcgi_index       index.php;
            fastcgi_param       SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param       FUEL_ENV production;
            include             fastcgi_params;

            ## SETUP OPTIONAL
            ## HTTPS terminating at a load balancer?
            ## PHP needs to know if it's using https via the $_SERVER['HTTPS'] variable
            ## options: hard code to 'on', use $https, or some processing of $http_x_forwarded_proto
            #
            fastcgi_param       HTTPS $http_x_forwarded_proto;

			add_header X-Content-Type-Options "nosniff";
    		add_header X-XSS-Protection "1; mode=block";

            ## SETUP OPTIONAL
            ## set php ini values to increase file upload capacity.
            ## You may prefer to set this elsewhere if you run other php applications on this server
            ## options would be here, php-fpm config, php.ini, or add to Materia's code
            fastcgi_param       PHP_VALUE "upload_max_filesize=50M \n post_max_size=50M \n memory_limit=250M \n max_execution_time=100";

        }

        ## Directives to send expires headers and turn off 404 error logging.
        # note this may make updates to assets require cache clearing in the browser
        location ~* ^.+\.(?:ogg|ogv|svg|svgz|eot|otf|woff|mp4|ttf|jpe?g|gif|png|ico|zip|tgz|gz|ppt|tar|mid|midi|wav|bmp|rtf|js|css|json)$ {
            access_log off;
            log_not_found off;
            expires 30d;
            add_header Cache-Control "public, no-transform";
        }

        ## deny direct access to any php files
        ## can be tested by visiting materia.your-org.edu/test.php
        ## AND static-materia.your-org.edu/test.php
        location ~ \.php$ {
            return 404;
        }
    }
}

```
