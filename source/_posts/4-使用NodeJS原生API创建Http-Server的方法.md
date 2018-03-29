---
title: 4. 使用NodeJS原生API创建Http Server的方法
date: 2018-03-29 16:21:23
categories:
  - JavaScript
  - NodeJS
tags:
  - NodeJS
  - JavaScript
---

在使用[`Koa`][Koa]写服务，发现自己一直使用的都是`Express`之类的框架，却不知道NodeJS原生的Http API使用方法，遂查阅了文档，在此处记一下

<!-- more -->

```javascript
#!/usr/bin/env node
const url = require('url');
const http = require('http');
const querystring = require('querystring');

const server = http.createServer((request, response) => {
  try {
    if(request.method === "GET") {
      response.writeHead(200, {
        'Content-Type': 'text/html'
      });
      let { query } = url.parse(request.url, true);
      response.write(`hello ${ query.name || 'world' }!`);
    } else if(request.method == "POST") {
      response.writeHead(405, {
        'Content-Type': 'text/html'
      });
      response.write(`Method Not Allowed!`);
    }
  } catch (err) {
    response.writeHead(500, {
      'Content-Type': 'text/json'
    });
    response.write(JSON.stringify(err));
  } finally {
    response.end();
  }
});
server.listen(3000);
```

[Koa]: https://koa.bootcss.com/
