---
layout: post
title: Nginx, how to proxy to a Cloudfront distribution
---

To prevent some resolve errors, we need to append a resolver directive. The hostname shoul be the name of the cloudfront distribution.

```
location  /  {
  set $dsomehost dsomehost.cloudfront.net;
  resolver 1.1.1.1 valid=30s;
  proxy_ssl_protocols TLSv1.2;
  proxy_ssl_server_name on;
  proxy_pass  https://$dsomehost;
  proxy_set_header Host $dsomehost;
  break;
}
```

Taken from [here](https://medium.com/@chris.mckee/reverse-proxying-cloudfront-from-nginx-haproxy-over-tls-20ba729a2ddd).
