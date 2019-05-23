---
title: Cookie JWT with Node.js reverse proxy
date: 2019-05-10 16:16:39
tags:
  - JWT
  - Cookie
  - Node
---

### Our problem was...

... that the server and the client resided on different domains;  
... and that we wanted to do the authentication via cookies, in order to avoid storing the token in the local storage;  
... and that the browser would block the the cookie sent by the backend, considering it a [third party cookie](https://whatis.techtarget.com/definition/third-party-cookie).

### What we tried (and didn't work)

1. Setting the cookie `domain`. Actually, we were deployin to something like `fe.herokuapp.com` and `be.herokuapp.com`.  
   Setting the domain to `herokuapp.com` should actually work (see [this question](https://stackoverflow.com/questions/56019218/inconsistency-in-rfc6265-about-the-cookie-domain-handling)) but didn't because herokuapp is a [public suffix](https://publicsuffix.org/list/public_suffix_list.dat) and thus [ignored by the browser](https://mailarchive.ietf.org/arch/msg/http-state/TPAIg769WRpZsQVkKOCFpQNqFmE).
2. Adding `allowHeaders` and `exposeHeaders` to the `cors options` on the server, with the following value: `['cookie', 'set-cookie']`;
3. Deploying both BE and FE to heroku...  
   ? maybe backend.heroku.com and frontend.heroku.com are on the "same domain"?  
   --> Yes. but diferent sub-domains => cookie still blocked.

### A possible sollution would be...

... to go back to passing the token via header / body and to store it locally on the client side.  
React seems to protect us against XSS (the vulnerability with storing the token locally).  
But we are stubborn and really really want to use cookies. Hah.

### So our solution was to...

... use a 3rd service as a reverse-proxy:

1. Deploy client to `frontend.heroku.com`.
2. Deploy server to `backend.heroku.com`.  
   _N.B._ At this point, the cookies sent from the server to the client are blocked.  
   Issue reproduced.
3. Use [http proxy middleware](https://github.com/chimurai/http-proxy-middleware) to create a reverse proxy (see code below).
4. Deploy the proxy to `proxy.heroku.com`.
5. The following changes need to be done to the existing workflow:
   - The browser will call the proxy (the proxy will redirect the calls to the client);
   - The client code will use the proxy as a server (the proxy will also redirect calls to the server);
   - The server code will accept the proxy as a client (e.g. for the `allow origin` cors options).

Cookies are sent and life is beautiful again :).

### Next steps

- Use the reverse proxy to enforce tls/ssl usage;
- Use nginx instead of node as a reverse proxy;
  - Why? What are the advantages? (besides learning a bit about nginx)
- Avoid deploying 3 apps to heroku:
  - put all 3 inside a docker image; (can-can?)
  - only publish the port of the proxy; (how?)
  - deploy this docker image to heroku; (?)
- Doc with cookie vs. token lessons learned.

### Helpful resources along the way:

- [HTTP rfc6265](https://tools.ietf.org/html/rfc6265#section-4.1.2.3)
- [Dockerize node app](https://nodejs.org/de/docs/guides/nodejs-docker-webapp/)
- [Deploy node app to heroku](https://facebook.github.io/create-react-app/docs/deployment#heroku-https-wwwherokucom)
- [Cookies vs tokens](https://dzone.com/articles/cookies-vs-tokens-the-definitive-guide)
- [React protects us against XSS](https://reactjs.org/docs/introducing-jsx.html#jsx-prevents-injection-attacks) ...
- [... but not completely](https://stackoverflow.com/questions/33644499/what-does-it-mean-when-they-say-react-is-xss-protected/51852579#51852579)
