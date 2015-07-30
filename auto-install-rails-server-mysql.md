## Environment

    AWS EC2 Ubuntu Server 12.04.2 LTS 64 bits
    Ruby 1.9.3
    Nginx with Passenger
    Node.js
    imagemagick
    mysql

## auto install server

    ssh -i ~/.ssh/vincent.pem ubuntu@xxx.xxx.xxx.xxx
    sudo vi install.sh

## install.sh

	# Update and install dependencies
	sudo apt-get -y update
	sudo apt-get -y install build-essential zlib1g-dev libssl-dev libreadline-dev libyaml-dev libcurl4-openssl-dev curl git-core python-software-properties

	# setup time zome
	sudo echo "Asia/Taipei" > /etc/timezone
    sudo dpkg-reconfigure -f noninteractive tzdata
    sudo apt-get -y install ntp
    sudo ntpdate ntp.ubuntu.com # Update time

	# Install Ruby 1.9.3
	wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p429.tar.gz
	tar -xvzf ruby-1.9.3-p429.tar.gz
	cd ruby-1.9.3-p429/
	./configure
	make
	sudo make install
	echo "gem: --no-ri --no-rdoc" >> ~/.gemrc
	sudo gem install bundler --no-rdoc --no-ri

	#Adds user with login deploy and password vincent90152900 (Encrypted)
	sudo useradd --create-home --password $(perl -e 'print crypt("vincent90152900", "aa")') deploy

	# Install Nginx with Passenger
	sudo gem install passenger
	sudo passenger-install-nginx-module --auto --prefix=/opt/nginx --auto-download

	# setup a script to control Nginx
	wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh
	sudo mv init-deb.sh /etc/init.d/nginx
	sudo chmod +x /etc/init.d/nginx
	sudo /usr/sbin/update-rc.d -f nginx defaults

	# tell nginx to run as the deploy user
	sudo sed -i 1i"user deploy staff;" /opt/nginx/conf/nginx.conf

	# Node.js for the Rails asset pipeline
	sudo apt-add-repository -y ppa:chris-lea/node.js
	sudo apt-get -y update
	sudo apt-get -y install nodejs

	# install imagemagick
	sudo apt-get -y install imagemagick

	# install postgresql
	#sudo apt-get -y install postgresql libpq-dev

	# install mysql
	sudo echo "mysql-server-5.5 mysql-server/root_password_again password vincent90152900" | debconf-set-selections
	sudo echo "mysql-server-5.5 mysql-server/root_password password vincent90152900" | debconf-set-selections
	sudo apt-get -y install mysql-server
	sudo apt-get -y install mysql-client
	sudo apt-get -y install libmysqlclient-dev

## execute install.sh

    sudo sh install.sh


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
