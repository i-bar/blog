---
title: How to secure the transport layer
date: 2019-05-14 16:14:30
tags:
  - TLS/SSL
  - HTTPS
  - Node
---

Below are some travel notes from the path towards making a node.js express app only be accessible via https.  
Hopefully some of the signs on the way are generic enough to also be used with other technologies.

The following steps need to be taken:

1. Add support for https;
2. Redirect all http to https;
3. Extra (but mandatory) measures:
   - add the `hsts` header and
   - mark all cookies as `secure`.

---

## Step 1. Add support for https to our service(s)

This actually translates to 'generate and use an SSL certificate'.

### The easy way: use the default SSL certificate on your PaaS (if any)

Depending on where the app is deployed, SSL support might come out of the box or simply translate to a small setting.  
That is, our app might be accessible via both http and https without us needing to make any change to it - yay! :D

E.g., on Heroku:

- Free dynos have SSL enabled by default, with a default wildcard certificate (`\*.herokuapp.com`): see [this stackoverflow answer](https://stackoverflow.com/a/22751658/777833) for more details.
- A custom SSL certificate can only be added to paid dynos: see the [heroky SSL docs](https://devcenter.heroku.com/articles/ssl).

### The hard (and recommended?) way:

a) Generate a custom SSL certificate;  
b) Trust the generated certificate on our host;  
c) Use the certificate in our server.

#### a. Generate a custom SSL certificate

This can be achieved in one of the following ways:

1. Use a **self-signed** certificate (e.g. openssl)
   - Pros: fast, nothing to install, useful for testing / development purposes.
   - Cons: browsers won't trust it and will display the 'untrusted' warning. (more details [here](http://answers.ssl.com/2899/can-i-create-my-own-ssl-certificate))
2. Use a **free** Certificate Authority (e.g. [Let's Encrypt](https://letsencrypt.org/)):
   - Pros: well... free. And recognized by browsers ;).
   - Cons: has to be renewed every 3 months. However, "renewal is as easy as running one simple command, which we can assign to a cron" ([source](https://www.sitepoint.com/how-to-use-ssltls-with-node-js/))
3. Pay a **trusted CA**.
   - Pros: no cons of the above :).
   - Cons: not free. However, not _extremely_ costly either..

**_Important note: where to store the certificates?_**  
Store the certificates in a secure place (e.g. behind a secret manager like keepass) and definitely do not store them in publicly accessible places like github. See [this answer](https://serverfault.com/a/648364/432012) for more details.

**Example** of generating a localhost self-signed certificate:

- inspired from [Let's Encrypt](https://letsencrypt.org/docs/certificates-for-localhost/)
- the following command will create two files, `localhost.key` and `localhost.crt`:

```
 openssl req -x509 -out localhost.crt -keyout localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

#### b. Trust the freshly generated SSL certificate

**Example** of trusting the above generated certificate on the localhost, taken from [this blog](https://derflounder.wordpress.com/2011/03/13/adding-new-trusted-root-certificates-to-system-keychain/):  
`sudo security add-trusted-cert -d -r trustRoot -k "/Library/Keychains/System.keychain" "./localhost.crt"`

#### c. Use the SSL certificate in the server app

**Example** of using the above generated localhost certificate in our server app:

```
var https = require("https");
var fs = require("fs");

[...]

const sslOptions = {
  key: fs.readFileSync("localhost.key"),
  cert: fs.readFileSync("localhost.crt"),
};

const server = https.createServer(sslOptions, app);
server.listen(process.env.PORT || 3001, function() {
  console.log(`Listening on port ${server.address().port}...`);
});
```

---

## Step 2. Redirect all `http` to `https`:

Just in order to avoid the 404 pages when a user (accidently, not for attacking reasons, of course...) tries to access the app via http, we also need to support http requests - and redirect them to https.

We would need a middle-layer to intercept all requests, verify if they come via an unencrypted channel and if so, redirect them. That can be echieved in two ways:

- By using an already implemented middle layer.
  The most popular among the [currently available npm packages](https://www.npmjs.com/search?q=express%20https&ranking=maintenance) that do that is [express-http-to-https](https://www.npmjs.com/package/express-http-to-https):

  ```
  var redirectToHTTPS = require("express-http-to-https").redirectToHTTPS;
  app.use(redirectToHTTPS([], [], 301));
  ```

  - Pros: code is clean(-ish... not really crazy about it). Would count on the package to be careful to do updates.
  - Cons: actually didn't find any package recent enough or that seems to be kept up to date.

- By manually implementing a middle layer:  
  Because we are using Heroku, we need to verify the `x-forwarded-proto` instead of only the req.secure flag.  
  (see the [Heroku docs](https://help.heroku.com/J2R1S4T8/can-heroku-force-an-application-to-use-ssl-tls) for more details)
  ```
  app.use(function(req, res, next) {
    // x-forwarded-proto because we are behind a load balancer (heroku, in our case)
    if (!req.secure && req.get("x-forwarded-proto") !== "https") {
      // request was via http, so redirect to https
      res.redirect("https://" + req.headers.host + req.url);
    } else {
      // request was via https, so do no special handling
      next();
    }
  });
  ```

**_TODO_**: The x-forwarded-proto might be unsecure, according to both [heroku docs](https://devcenter.heroku.com/articles/http-routing#heroku-headers) and [express docs](http://expressjs.com/en/4x/api.html#app.set). Find another way to do the forwarding...

### Exception: on localhost we need to create two servers :/

Even if when deploying to Heroku creating one server is enough and it will be accessible via both http and https, on localhost we need to create two servers:

- one that listens to the :80 port for http requests (and redirects them to https) and
- one that listens on the :443 port for https requests.

**TODO** There's gotta be a common way for localhost and cloud-deployed app to listen to both and redirect http to https... just that I haven't found it (yet) :/...

**`Code` sample** for localhost:

```
const httpsServer = https.createServer(sslOptions, app);
httpsServer.listen(443, function() {
  console.log(`Listening on port ${httpsServer.address().port}...`);
});

// Also listen on http and redirect from all to https
var httpServer = require("http");
httpServer
  .createServer(function(req, res) {
    res.writeHead(301, {
      Location: "https://" + req.headers["host"] + req.url,
    });
    res.end();
  })
  .listen(80);
```

## Step 3. Extra security measures

... because no app is secure enough... because hackers are (too) creative...

### Step 3.1. Use the HSTS header

**Why?**  
Scary [example of an attack](https://blog.duszynski.eu/hijacking-browser-tls-traffic-through-client-domain-hooking/) that leverages web apps that don't have hsts enabled.  
Even one single http request can be a breach.

**How** does it help us?  
Enabling hsts is only a step towards https-only requests: it will instruct browsers to only use https to access our app _in the future_. That is, the very first request can still be done via http. But hey... something is better than nothing.  
A [nice explanation of HSTS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security) from Mozilla.

**`Code` sample** using the [helmet npm package](https://github.com/helmetjs/helmet):

```
var hsts = require("hsts");

app.use(
  hsts({
    maxAge: 15552000, // 180 days in seconds
  })
);
```

### Step 3.2. Use Secure cookies

**Why?**  
Even if the app runs over https and we redirect all http to https, a user can still send their session id via the cookie over http.

**How** do they fix this?  
Cookies with the 'secure' flag set can only be sent through https.

---

## Other (hopefully) useful resources

- [OWASP cheat sheet for transpor layer protection](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.md)
- [Secure authentication and sessions for node apps](http://scottksmith.com/blog/2015/06/15/secure-node-apps-against-owasp-top-10-authentication-and-sessions/)
- [Helmet repo](https://github.com/helmetjs/helmet) - TODO: use it to add more secure headers;
