---
layout: post
title: CloudFront temporary redirect
author: Pere Vilas
tags:
- aws
---


If you are configuring a distribution pointing to a S3 public bucket and you are (culr -v) getting a redirection like

```
<Error><Code>TemporaryRedirect</Code><Message>Please re-send this request to the specified temporary endpoint. Continue to use the original request endpoint for future requests.</Message><Endpoint>
```

Or the browser is rewriting the domain url with the s3 url bucket you likely have the **Origin** bucket named without the zone. 
Simply add the zone between the s3 and amazonaws.com like in  `mybucket.s3.eu-central-1.amazonaws.com`.
