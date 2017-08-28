---
layout: post
section-type: post
title: React Router에서 특정 URL 접속 시 페이지를 찾을 수 없는 문제 원인 및 해결 방법(nginx, node express, apache, jboss web app)
category: nginx
tags: [ 'nginx', 'react' ]
---

> react router를 사용해서 url을 관리 할 때 하위 url에서 새로고침을 하거나 example.com/post와 같이 직접 url을 입력해 접근하면
> 404 페이지, 즉 페이지를 찾을 수 없는 것을 알 수 있다.
>
> 이를 방지하기 위해, node.js의 express.js를 이용해 url을 관리하는 방법 등이 있는데, 각각의 방법에서
> react-router를 사용하는 방법에 대해 포스팅했다.

# react router로 배포하기

react-router를 사용했을 때, 로컬에서 http://localhost:8000/login 와 같이 접근하게 되면 /login에 해당되는 컴포넌트가 나온다.  
하지만 https://linkuniversity.me/login 과 같이 배포 후에 특정 도메인을 통해 접근한다면,ㅜ 페이지를 찾을 수 없다는 페이지를 볼 수 있게 된다.  

이런 현상이 일어나는 것은, BASE가 되는 URL은 index.html을 연결시켜 배포하지만, __다른 URL으로 접속하면 해당 URL에 맞는 HTML을 찾으려고 하기 때문에 생기는 문제이다.__  
 예를 들어 ```/login``` URL에 접속하려고 했을 때 ```/login``` URL에 맞는 html 파일을 nginx 내에서 찾으려고 하기 때문이다.  
__따라서 BASE가 되는 URL이 아닌 다른 URL을 통해 사이트에 접속해도, index.html을 연결시켜 배포해 주는 작업이 필요하다.__  

# nginx

/etc/nginx/conf.d/vhosts.conf의 location 부분에 다음과 같은 코드를 추가한다.

``` text
server {
    listen 80 default_server;
    server_name /var/www/example.com;

    root /var/www/example.com;
    index index.html index.htm;      

    location ~* \.(?:manifest|appcache|html?|xml|json)$ {
      expires -1;
      # access_log logs/static.log; # I don't usually include a static log
    }

    location ~* \.(?:css|js)$ {
      try_files $uri =404;
      expires 1y;
      access_log off;
      add_header Cache-Control "public";
    }

    # Any route containing a file extension (e.g. /devicesfile.js)
    location ~ ^.+\..+$ {
      try_files $uri =404;
    }

    # Any route that doesn't have a file extension (e.g. /devices)
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

# apache

/etc/httpd/conf.d\vhosts.conf에  Rewrite* lines 부분에 다음과 같이 추가한다.  

``` text
<VirtualHost *:8080>
  ServerName example.com
  DocumentRoot /var/www/httpd/example.com

  <Directory "/var/www/httpd/example.com">
    ...

    RewriteEngine on
    # Don't rewrite files or directories
    RewriteCond %{REQUEST_FILENAME} -f [OR]
    RewriteCond %{REQUEST_FILENAME} -d
    RewriteRule ^ - [L]
    # Rewrite everything else to index.html to allow html5 state links
    RewriteRule ^ index.html [L]
  </Directory>
</VirtualHost>
```

# node express

``` js
const express = require('express')
const path = require('path')
const port = process.env.PORT || 8080
const app = express()

// serve static assets normally
app.use(express.static(__dirname + '/public'))

// handle every other route with index.html, which will contain
// a script tag to your application's JavaScript file(s).
app.get('*', function (request, response){
  response.sendFile(path.resolve(__dirname, 'public', 'index.html'))
})

app.listen(port)
console.log("server started on port " + port)
```

# jboss web app

[이 링크](https://gkedge.gitbooks.io/react-router-in-the-real/content/jboss_web_app.html)를 참고하자.  

참고자료 --  
https://gkedge.gitbooks.io/react-router-in-the-real/content/index.html
