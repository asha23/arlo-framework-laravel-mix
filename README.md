# Arlo Wordpress Framework

## Install

You should have Composer installed globally on your machine. Simply run:

### ```composer create-project asha23/arlo-framework-laravel-mix```

## Overview

This framework is not an off-the-shelf theme. It's a boilerplate to help developers get up and running with WordPress quickly and easily. It's not designed to be used by beginners and some knowledge of php is assumed.

## Features

1. WordPress is installed using Composer as a dependency. This means you can update WordPress by running ```composer update``` in the project root.

1. Similar to Laravel, the core files are out of the root. Database information is kept in a .env file.

3. WordPress Packagist - Keep your plugins up to date by running ```composer update```

4. Uses Laravel Mix in the base theme included. This is a simple way to manage all your build dependencies.

## Notes

This is a very barebones setup. It does include a base theme, but doesn't rely on it.

It's recommended that you use Laravel Homestead to deploy this locally. Please note that the root folder is ```/web```, so you will need to configure the server correctly to point to this folder.

## Example NGINX config

```
    server {
        listen 80;
        listen 443 ssl http2;
        server_name .plugin-dev.test;
        root "/home/vagrant/code/***your-website***/web";

        index index.php index.html index.htm;

        charset utf-8;

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { allow all; access_log off; log_not_found off; }

        location ~ /.*\.(jpg|jpeg|png|js|css)$ {
            try_files $uri =404;
        }

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        if (!-e $request_filename) {
            # Add trailing slash to */wp-admin requests.
            rewrite /wp-admin$ $scheme://$host$uri/ permanent;

            # WordPress in a subdirectory rewrite rules
            rewrite ^/([_0-9a-zA-Z-]+/)?(wp-.*|xmlrpc.php) /web/wp/$2 break;
        }

        location ~ \.php$ {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            include fastcgi_params;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
            fastcgi_intercept_errors on;
            fastcgi_buffers 16 16k;
            fastcgi_buffer_size 32k;
        }

        access_log off;
        error_log  /var/log/nginx/plugin-dev.test-error.log error;

        sendfile off;

        client_max_body_size 100m;

        location ~ /\.ht {
            deny all;
        }

        ssl_certificate     /etc/nginx/ssl/***your-website.crt***;
        ssl_certificate_key /etc/nginx/ssl/***your-website.key***;
    }
```

## Example ```_htaccess``` config

Use this in the root if you are on an Apache setup.

```
    RewriteEngine on
    RewriteCond %{REQUEST_URI} !^/web
    RewriteRule ^(.*)$ /web/$1 [NC,L]
```

___

[Created by Ash Whiting](http://ashwhiting.com)

Thanks to: [Wordplate](https://github.com/wordplate) for their excellent ACF Builder module.
