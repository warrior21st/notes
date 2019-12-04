#### 安装nginx

	yum install pcre*
	yum install openssl*
	yum install zlib 
	yum install zlib-devel
	yum install wget
	wget http://nginx.org/download/nginx-1.8.0.tar.gz
	tar -zxvf nginx-1.8.0.tar.gz
	cd nginx-1.8.0
	./configure --prefix=/usr/local/nginx-1.8.0 \--with-http_ssl_module --with-http_spdy_module \--with-http_stub_status_module --with-pcre
	make && make install 

#### 编辑 nginx.conf
	vi /usr/local/nginx-1.8.0/conf/nginx.conf
#### nginx.conf内容如下
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
		proxy_redirect                  off;
		proxy_set_header                Host                    $host;
		proxy_set_header                X-Real-IP               $remote_addr;
		proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
		client_max_body_size    10m;
		client_body_buffer_size 128k;
		proxy_connect_timeout   90;
		proxy_send_timeout              90;
		proxy_read_timeout              90;
		proxy_buffers                   32 4k;
		limit_req_zone $binary_remote_addr zone=one:10m rate=30r/s;
		server_tokens off;
	
		sendfile on;
		keepalive_timeout 29; # Adjust to the lowest possible value that makes sense for your use case.
		client_body_timeout 10; client_header_timeout 10; send_timeout 10;
	
		upstream zkor{
			server localhost:5000;
		}
	
		server {
			listen *:80;
			server_name www.zkor.com zkor.com;
			add_header Strict-Transport-Security max-age=15768000;		
			return 301 https://www.zkor.com$request_uri;
		}
	
		server {
			listen *:443    ssl;
			server_name    zkor.com;
			ssl_certificate /root/zkor_cert/zkor.crt;
			ssl_certificate_key  /root/zkor_cert/zkor.key;
			ssl_protocols TLSv1.1 TLSv1.2;
			ssl_prefer_server_ciphers on;
			ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
			ssl_ecdh_curve secp384r1;
			ssl_session_cache shared:SSL:10m;
			ssl_session_tickets off;
			ssl_stapling on; #ensure your cert is capable
			ssl_stapling_verify on; #ensure your cert is capable
	
			add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
			add_header X-Frame-Options DENY;
			add_header X-Content-Type-Options nosniff;
	
			#Redirects all traffic

			location /nginx-status-pass-zkor123456 {  
			    stub_status on;
			    access_log off;
			} 

			location / {
			    proxy_pass  http://zkor;
			    limit_req   zone=one burst=10;
			}
		}
	}
