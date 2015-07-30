## Environment

    AWS EC2 Ubuntu Server 12.04.2 LTS 64 bits
    Ruby 1.9.3
    Nginx with Passenger
    Node.js
    imagemagick
    postgresql

## auto install server

    ssh -i ~/.ssh/vincent.pem ubuntu@xxx.xxx.xxx.xxx
    curl -L https://gist.github.com/vincentopensourcetaiwan/6061235/raw/ea0646c29f6065a0e4356991538d55b4ad5252bf/create-a-rails-server.sh | sh

## setup postgresql password

    sudo su - postgres
    psql -d template1
    template1=# ALTER USER postgres WITH PASSWORD 'some password';
    \q

## restart postgresql

    sudo /etc/init.d/postgresql restart

## create database

    sudo su - postgres
    psql -h localhost
    CREATE DATABASE DatabaseName;
    \q

## database.yml.production

    production:
      host: localhost
      adapter: postgresql
      encoding: unicode
      database: testpostgresql
      pool: 5
      username: postgres
      password: password


## /opt/nginx/conf/nginx.conf

    user deploy staff;

    #user  nobody;
    worker_processes  1;

    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;

    #pid        logs/nginx.pid;


    events {
        worker_connections  1024;
    }


    http {
        passenger_root /usr/local/lib/ruby/gems/1.9.1/gems/passenger-4.0.10;
        passenger_ruby /usr/local/bin/ruby;

        include       mime.types;
        default_type  application/octet-stream;

        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        #keepalive_timeout  0;
        keepalive_timeout  65;

        #gzip  on;

    #    server {
    #        listen       80;
    #        server_name  localhost;
    #
    #        #charset koi8-r;
    #
    #        #access_log  logs/host.access.log  main;
    #
    #        location / {
    #            root   html;
    #            index  index.html index.htm;
    #        }
    #
    #        #error_page  404              /404.html;
    #
    #        # redirect server error pages to the static page /50x.html
    #        #
    #        error_page   500 502 503 504  /50x.html;
    #        location = /50x.html {
    #            root   html;
    #        }
    #
    #        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #        #
    #        #location ~ \.php$ {
    #        #    proxy_pass   http://127.0.0.1;
    #        #}
    #
    #        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #        #
    #        #location ~ \.php$ {
    #        #    root           html;
    #        #    fastcgi_pass   127.0.0.1:9000;
    #        #    fastcgi_index  index.php;
    #        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #        #    include        fastcgi_params;
    #        #}
    #
    #        # deny access to .htaccess files, if Apache's document root
    #        # concurs with nginx's one
    #        #
    #        #location ~ /\.ht {
    #        #    deny  all;
    #        #}
    #    }


    server {
        listen 80;
        server_name localhost;
        root /home/deploy/test-postgresql/current/public;   # <--- be sure to point to 'public'!
        passenger_enabled on;
    }



        # another virtual host using mix of IP-, name-, and port-based configuration
        #
        #server {
        #    listen       8000;
        #    listen       somename:8080;
        #    server_name  somename  alias  another.alias;

        #    location / {
        #        root   html;
        #        index  index.html index.htm;
        #    }
        #}


        # HTTPS server
        #
        #server {
        #    listen       443;
        #    server_name  localhost;

        #    ssl                  on;
        #    ssl_certificate      cert.pem;
        #    ssl_certificate_key  cert.key;

        #    ssl_session_timeout  5m;

        #    ssl_protocols  SSLv2 SSLv3 TLSv1;
        #    ssl_ciphers  HIGH:!aNULL:!MD5;
        #    ssl_prefer_server_ciphers   on;

        #    location / {
        #        root   html;
        #        index  index.html index.htm;
        #    }
        #}

    }
