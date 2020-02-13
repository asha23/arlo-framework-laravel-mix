# Arlo Wordpress Framework

###```composer create-project asha23/arlo-framework-laravel-mix```

### Notes

This is a very barebones setup. It does include a base theme, but doesn't rely on it.

It's recommended that you use Laravel Homestead to deploy this locally. Please note that the root folder is ```/web```, so you will need to configure the server correctly to point to this folder.

### Example NGINX config

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

### Example ```_htaccess``` config

Use this in the root if you are on an Apache setup.

```
RewriteEngine on
RewriteCond %{REQUEST_URI} !^/web
RewriteRule ^(.*)$ /web/$1 [NC,L]
```


