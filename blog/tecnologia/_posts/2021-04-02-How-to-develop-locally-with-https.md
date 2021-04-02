As more and more restrictions to http come with the new versions of the browsers (especially Chrome) it is necessary find out a solution 
to develop locally without the burden of self-signed certificates (that work on some browser versions but not in others), keys, etc.

One option is to develop using local http developer servers (Flask, Django, etc.) but let a proxy (Nginx) to deal with https. 

We will need a fully trusted self-signed certificate and nginx.

## Cookies

First of all, to avoid problems with cookies, you should rewrite the name of localhost with other name in `/etc/hosts` like:

```120.0.0.1 mymac.myoffice.dev```

## Mkcert

With [mkcert](https://github.com/FiloSottile/mkcert) we can create a "fake" CA (Authority) on our machine and issue self-signed certificate that is **fully trusted** by the browser. 

## Nginx proxy

Once we have the certicate, we indicate nginx to **proxy_pass** the https calls to our local http service (on port 5000 in this example).

```
    server {
        server_name casa.pere.dev;
        rewrite ^(.*) https://mymac.myoffice.dev$1 permanent;
    }
    server {
        server_name          mymac.myoffice.dev;
        listen               443 ssl;        
        ssl_certificate      certs/_wildcard.myoffice.dev+3.pem;
        ssl_certificate_key  certs/_wildcard.myoffice.dev+3-key.pem;
        ssl_ciphers          HIGH:!aNULL:!MD5;
        access_log           logs/access.log;        
        error_log            logs/error.log debug;
       
        location / {
            proxy_pass  http://mymac.myoffice.dev:5000;
        }
    }
```

Note that the proxy_pass directive will send all the traffic to your local http development server on port 5000.

## Bonus: WebSockets

If you want to pass the WS traffic, include this location onto the server

```
    location /socket.io {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_http_version 1.1;
            proxy_buffering off;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_pass http://mymac.myoffice.dev:5555/socket.io;
    }
```
