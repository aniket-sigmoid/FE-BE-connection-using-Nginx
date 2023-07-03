
# Setup Instructions

Make sure you have separate repos of frontend and backend open in two different windows of VS code or any other code editor.

## Start BACKEND

1. Clone the repo of the backend to the required location.
2. Browse to the folder, just outside src.
3. Install Venv Dependencies: `pip install virtualenv`
4. Create Virtual Environment: `python3 -m venv env`
   This creates a virtual env folder named "env". Now all your dependencies will be installed there once activated.
5. Activate Virtual env: `source venv/bin/activate`
6. Run `pip3 install -r requirement.txt`
   Debug any error that might come during installation (mostly version related issue).
7. Run the backend through app.py entrypoint: `python3 src/app.py`

## Install and Configure NGINX

1. Install nginx in a separate terminal using `brew install nginx`.
2. Start nginx with admin access: `sudo nginx`.
   Nginx runs at localhost:8080 (http://localhost:8080) locally. If you open this URL in the local browser, it should display the following message:

```
“Welcome to nginx! 
If you see this page, the nginx web server is successfully installed and working. Further configuration is required. 

For online documentation and support, please refer to nginx.org. 
Commercial support is available at nginx.com. 

Thank you for using nginx.”

```







This confirms that nginx is running successfully.

## Configure NGINX to Connect Frontend and Backend

1. Locate the nginx.conf file. If you are using macOS and Homebrew, the location should be: `/opt/homebrew/etc/nginx`.
2. Stop the nginx server: `sudo nginx -s stop`.
3. Move to the nginx configuration folder: `cd /opt/homebrew/etc/nginx`.
4. Open the file `nginx.conf` with a text editor (e.g., VS Code).
5. Replace all the existing code block with the following code:



```
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

    server {
        listen 80;
        server_name  localhost;
        keepalive_timeout  18000;
        fastcgi_read_timeout 18000;
        proxy_read_timeout 18000;

        location /authorization-code/callback {
            proxy_pass http://localhost:5001;
            proxy_redirect     off;
            proxy_set_header   Host                 $host;
            proxy_set_header   X-Real-IP            $remote_addr;
            proxy_set_header   X-Forwarded-For      $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto    "http";
            proxy_read_timeout 18000;
            proxy_connect_timeout 18000;
            proxy_send_timeout 18000;
            send_timeout 18000;
        }

        location ^~ /api {
            proxy_pass http://localhost:5001;
            proxy_redirect     off;
            proxy_set_header   Host                 $host;
            proxy_set_header   X-Real-IP            $remote_addr;
            proxy_set_header   X-Forwarded-For      $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto    "http";
            proxy_read_timeout 18000;
            proxy_connect_timeout 18000;
            proxy_send_timeout 18000;
            send_timeout 18000;
        }

        location / {
            proxy_pass http://localhost:3000;
        }
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
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    include servers/*;
}

```



















Make sure to replace the nginx.conf file with the actual nginx.conf content shared above.
6. Save the `nginx.conf` file.
7. Start the nginx server: `sudo nginx`.

Now both the frontend and backend should be up and running at different ports. Make a note of their port numbers.

## Verify Connection

To verify that the frontend and backend are connected via nginx:

1. Open your web browser.
2. Enter `localhost:80` in the address bar.
3. You should see both the frontend and backend connected!

To stop nginx and use the individual frontend and backend:

1. Run `sudo nginx -s stop`.
Now `localhost:80` will give a "Not found" error (404).


