## References

[ #357 Adding SSL pro](http://railscasts.com/episodes/357-adding-ssl "#357 Adding SSL pro")


## generate the self-signed certificate

    cd /opt/nginx/conf/
    openssl req -new -nodes -keyout server.key -out server.csr
    openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt


## configure Nginx server

/opt/nginx/conf/nginx.conf

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
        passenger_root /usr/local/lib/ruby/gems/1.9.1/gems/passenger-4.0.5;
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
    		    server_name 123.123.12.12;
    		    root /home/deploy/online-store/current/public;   # <--- be sure to point to 'public'!
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
        server {
            listen       443;
            server_name  123.123.12.12;

            ssl                  on;
            ssl_certificate      server.crt;
            ssl_certificate_key  server.key;

            ssl_session_timeout  5m;

            ssl_protocols  SSLv2 SSLv3 TLSv1;
            ssl_ciphers  HIGH:!aNULL:!MD5;
            ssl_prefer_server_ciphers   on;

            location / {

                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_redirect off;
                proxy_pass http://127.0.0.1;
            }
        }

    }

## Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.

config/environments/production.rb

    config.force_ssl = true


## purchase third party ssl certificates

## Generating a Certificate Signing Request - NGINX

[Generating a Certificate Signing Request - NGINX](http://support.godaddy.com/help/article/3601/generating-a-certificate-signing-request-nginx "Generating a Certificate Signing Request - NGINX")

## submit CSR file to Godday

## Downloading an SSL Certificate

[Downloading an SSL Certificate](http://support.godaddy.com/help/article/4754/downloading-an-ssl-certificate "Downloading an SSL Certificate")

## Installing an SSL Certificate - Nginx

upload gd_bundle.crt and xxx.com.crt to the server

[Installing an SSL Certificate - Nginx](http://support.godaddy.com/help/article/6722/installing-an-ssl-certificate-nginx "Installing an SSL Certificate - Nginx")

## configure Nginx server

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
        passenger_root /usr/local/lib/ruby/gems/1.9.1/gems/passenger-4.0.5;
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
    	    server_name XXX.com;
            root /home/deploy/XXX/current/public;   # <--- be sure to point to 'public'!
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
        server {
            listen       443;
            server_name  XXX.com;

            ssl                  on;
            ssl_certificate      XXX.com.crt;
            ssl_certificate_key  XXX.com.key;

            ssl_session_timeout  5m;

            ssl_protocols  SSLv2 SSLv3 TLSv1;
            ssl_ciphers  HIGH:!aNULL:!MD5;
            ssl_prefer_server_ciphers   on;

            location / {
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_redirect off;
                proxy_pass http://127.0.0.1;
            }
        }
    }

# force ssl connection

/ config / environments / production.rb

    # Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.
    config.force_ssl = true




