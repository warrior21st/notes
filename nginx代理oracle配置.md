### nginx.conf

    #user  nobody;
    worker_processes  1;

    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;

    #pid        logs/nginx.pid;

    events {
        worker_connections  1024;
    }

    stream {
        upstream oracle {   
            server serverip:1521 weight=1 max_fails=2 fail_timeout=30s;   
        }
    server {
            listen       1521;
            proxy_connect_timeout 1s;
            proxy_timeout 3s;		
            proxy_pass oracle;
        }
    }