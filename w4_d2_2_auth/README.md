# Authentication

| Objectives |
| :---- |
| Review the request and response cycle and the stateless web |
| Discuss and use a cookie in a web application |
| Implement Sessions and review the concept of middleware. |

## Cookies

Let's take a look at how to create cookies before discussing their utility:

```js
var express = require("express");

var app = express();

app.get("/", function (req, res) {
  res.set({
    "Set-Cookie": "count=1"
  });
  res.send("Hello World");
});

app.listen(3000, function () {
  console.log("UP AND RUNNING");
});
```

This sends a response that looks something like the following:

```
HTTP/1.1 200 OK
X-Powered-By: Express
Set-Cookie: count=1
Content-Type: text/html; charset=utf-8
Content-Length: 11
ETag: W/"b-4a17b156"
Date: Mon, 18 May 2015 07:36:50 GMT
Connection: keep-alive

Hello World
```

The browser will save the cookie; you can view all cookies for the current domain with Chrome's developer tools (Resources tab).

The cookie will be included the next time your browser makes a request to the same domain.

```
...
  cookie: 'count=1',
...
```

Here's how cookies can be used to count the number of times someone has come to your site:

```js
var express = require("express");

var app = express();

app.get("/", function (req, res) {
  console.log(req.headers);
  var cookieStr = req.get("Cookie");
  var count = 0;
  if (cookieStr ) {
    count = parseInt(cookieStr.split("=")[1]);
  }
  count += 1;
  res.set({
    "Set-Cookie": "count=" + count
  });
  res.send("Hello World");
});

app.listen(3000, function () {
  console.log("UP AND RUNNING");
});


```

Express also has a built in method called `res.cookie` to make writing cookies easier:

```js

var express = require("express");

var app = express();

app.get("/", function (req, res) {
  console.log(req.headers);
  var cookieStr = req.get("Cookie");
  var count = 0;
  if (cookieStr ) {
    count = parseInt(cookieStr.split("=")[1]);
  }
  count += 1;
  res.cookie('count', count);
  res.send("Hello World");
});

app.listen(3000, function () {
  console.log("UP AND RUNNING");
});
```

**Note**: Cookies that don't have expiration dates are considered to be session cookies; they are deleted after the browser is closed.

If we want to manipulate cookies we should problem do a better job of parsing them.

`cookie-parser` gives us a nicer way to parse cookies from the request:

```
$ npm install --save cookie-parser

```

```js
var express      = require('express');
var cookieParser = require('cookie-parser');

var app = express();
app.use(cookieParser());

app.get("/", function (req, res) {
  console.log(req.cookies);
  var count = parseInt(req.cookies.count)
  res.cookie('count', count);
  res.send("Hello World");
});

app.listen(3000, function () {
  console.log("UP AND RUNNING");
});
```