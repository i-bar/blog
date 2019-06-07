---
title: How to secure a Node.js app
date: 2019-05-30 16:14:30
tags:
  - SSL/TLS
  - HTTPS
  - Node
  - Headers
---

There are a lot of sites that help us **test the security level** of our apps. Some examples include:

- [Mozilla observatory](https://observatory.mozilla.org) for an overview of the app security;
- [SSL Labs](https://www.ssllabs.com) for secure connection checks;
- [Security Headers](https://securityheaders.com) for secure headers checks.

Below are some steps to make a Node.js app score <span style="color:green; font-size:24px">**A+**</span> on (most of?) them:

## SSL/TLS

1. Add support for **https**, if not done automatically by the deployment server.
2. **Redirect** all http to https.

Please see [this entry](https://i-bar.github.io/2019/05/14/http-how-to/) for `node` instructions.

## Secure Headers

[Helmet](https://helmetjs.github.io/docs/) does a wonderful job with adding the most obvious headers.

However, some of the headers need to be added manually:

- The [referrer-policy header](https://infosec.mozilla.org/guidelines/web_security#referrer-policy)
- The [content-security-policy header](https://infosec.mozilla.org/guidelines/web_security#referrer-policy)

This can also be done with helmet.

The final code looks something like this:

```javascript
const express = require('express');
const helmet = require('helmet');
var redirectToHTTPS = require('express-http-to-https').redirectToHTTPS;

const app = express();

// Security middle layers:

// Redirect to https
app.use(redirectToHTTPS([], [], 301));

// helmet adds *some* of the recommended headers: https://github.com/helmetjs/helmet
app.use(helmet());

// Add CSP header to only allow using resources from our 'self'
app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
    },
  })
);

// Do not set the referer header
app.use(helmet.referrerPolicy({ policy: 'no-referrer' }));
```

## Secure cookies

- Mark all cookies as `secure`, so that they are only sent via https;
- Mark all cookies as `httpOnly` so that they cannot be accessed via JavaScript on the client side.

`Node` example with such a cookie:

```javascript
res
  .cookie('token', 'some_secret_jwt_maybe', {
    httpOnly: true,
    secure: true,
  })
  .json('here, take a cookie');
```

Happy learning!
