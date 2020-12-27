
## Default NGINX configs

In case you need to compare against the original - this section contains the default site level NGINX configurations by site type. 

<base-info>
If you copy back to your NGINX config, be sure to replace <example.com> with your site's name. Also, if you applied your site to a server user
other than <b>cleavr</b>, then you'll need to swap <server user - eg. cleavr> out with the correct server username.
</base-info>

### Laravel

```
include cleavr-conf/<example.com>/header/*.conf;

server {
    
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current/public;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}


    index index.html index.htm index.php;

    error_page 404 /index.php;

    location / {
        include cleavr-conf/<example.com>/*.conf.pre;
        try_files $uri $uri/ /index.php?$query_string;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~* /(?:uploads|files)/.*\.php$ {
        deny all;
    }

    location ~* \.(gif|jpg|jpeg|png|css|js)$ {
        expires max;
    }

    location ~ \.php$ {
        include cleavr-conf/<example.com>/*.conf.pre;
        # fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm-cleavr.sock;
        # fastcgi_index index.php;
        # include fastcgi_params;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~ /\.ht {
        deny all;
    }
}

```

### Alpas

```

include cleavr-conf/<example.com>/header/*.conf;

map $http_upgrade $connection_upgrade {
    default upgrade;
}

server {
    
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}


    client_max_body_size 0;
    client_header_buffer_size 50M;
    proxy_read_timeout     1200;
    proxy_connect_timeout  240;

    location / {
        include cleavr-conf/<example.com>/*.conf.pre;
        proxy_pass              http://localhost:7871;
        proxy_http_version      1.1;
        proxy_set_header        X-Forwarded-For        $remote_addr;
        proxy_set_header        X-Forwarded-Proto      $scheme;
        proxy_set_header        X-Forwarded-Host       $http_host;
        proxy_set_header        Host                   $http_host;
        proxy_set_header        Upgrade                $http_upgrade;
        proxy_set_header        Connection             $connection_upgrade;
        include cleavr-conf/<example.com>/*.conf.post;
    }
}


```

### Nuxt / Node SSR

```
include cleavr-conf/<example.com>/header/*.conf;
map $http_upgrade $connection_upgrade {
    default upgrade;
}

server {
    
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}


    client_max_body_size 0;
    client_header_buffer_size 50M;
    proxy_read_timeout     1200;
    proxy_connect_timeout  240;

    location / {
        include cleavr-conf/<example.com>/*.conf.pre;
        proxy_pass              http://localhost:9657;
        proxy_pass_header Server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass_header Server;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~ /\.ht {
        deny all;
    }
}

```

### Nuxt / Node Static / HTML Static

```javascript
include cleavr-conf/<example.com>/header/*.conf;
map $http_upgrade $connection_upgrade {
  default upgrade;
}

server {
  
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}


  index index.html index.htm;

  location ~* \.(gif|jpg|jpeg|png|css|js)$ {
    expires max;
  }

  location / {
    include cleavr-conf/<example.com>/*.conf.pre;
    try_files $uri $uri/ =404;
    include cleavr-conf/<example.com>/*.conf.post;
  }

  location ~ /\.ht {
    deny all;
  }
}
```

### PHP
```php
include cleavr-conf/<example.com>/header/*.conf;

server {
    
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current/;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}

    index index.html index.htm index.php;

    error_page 404 /index.php;

    location / {
        include cleavr-conf/<example.com>/*.conf.pre;
        try_files $uri $uri/ /index.php?$query_string;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~* /(?:uploads|files)/.*\.php$ {
        deny all;
    }

    location ~* \.(gif|jpg|jpeg|png|css|js)$ {
        expires max;
    }

    location ~ \.php$ {
        include cleavr-conf/<example.com>/*.conf.pre;
        # fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm-cleavr.sock;
        # fastcgi_index index.php;
        # include fastcgi_params;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~ /\.ht {
        deny all;
    }
}

```


### WordPress 

```PHP
include cleavr-conf/<example.com>/header/*.conf;
server {
    
server_name <example.com>;

root /home/<server user - eg. cleavr>/<example.com>/current;
charset utf-8;
include cleavr-conf/<example.com>/*.conf;

# Uncomment the following line only if you don't want to enable NGINX monitoring for this site
# access_log off;
# Uncomment the following line if you don't have a favicon and don't want to log 404 errors
# location = /favicon.ico { access_log off; log_not_found off; }

# Comment the following line if you do have a robots.txt file
location = /robots.txt  { access_log off; log_not_found off; }

error_log /var/log/nginx/error.log error;

location ^~ /.well-known/acme-challenge/ {
  allow all;
}


    index index.php;

    error_page 404 /index.php;

    location / {
        include cleavr-conf/<example.com>/*.conf.pre;
        try_files $uri $uri/ /index.php?$query_string;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location ~* /(?:uploads|files)/.*\.php$ {
        deny all;
    }

    location ~* \.(gif|jpg|jpeg|png|css|js|svg)$ {
        expires max;
        access_log off;
    }

    location ~ \.php$ {
        include cleavr-conf/<example.com>/*.conf.pre;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm-cleavr.sock;
        include cleavr-conf/<example.com>/*.conf.post;
    }

    location = /wp-includes/js/tinymce/wp-tinymce.php {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/run/php/php7.4-fpm-cleavr.sock;
    }

    location ~* \.(?:svgz?|ttf|ttc|otf|eot|woff2?)$ {
      add_header Access-Control-Allow-Origin "*";
      expires    max;
      access_log off;
    }

    # WordPress: deny wp-content, wp-includes php files
    location ~* ^/(?:wp-content|wp-includes)/.*\.php$ {
        deny all;
    }

    # WordPress: deny wp-content/uploads nasty stuff
    location ~* ^/wp-content/uploads/.*\.(?:s?html?|php|js|swf)$ {
        deny all;
    }

    # WordPress: SEO plugin
    location ~* ^/wp-content/plugins/wordpress-seo(?:-premium)?/css/main-sitemap\.xsl$ {}

    # WordPress: deny wp-content/plugins (except earlier rules)
    location ~ ^/wp-content/plugins {
        deny all;
    }

    # WordPress: deny scripts and styles concat
    location ~* \/wp-admin\/load-(?:scripts|styles)\.php {
        deny all;
    }

    # WordPress: deny general stuff
    location ~* ^/(?:xmlrpc\.php|wp-links-opml\.php|wp-config\.php|wp-config-sample\.php|wp-comments-post\.php|readme\.html|license\.txt)$ {
        deny all;
    }

    location ~ /\.ht {
        deny all;
    }
}

```
