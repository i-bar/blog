---
title: Serverless with GCP - impressions
date: 2019-05-23 16:14:30
tags:
  - GCP
  - Serverless
---

The destination of this journey would have been a serverless application with an architecture similar to [this one](https://cdn.patricktriest.com/blog/images/posts/cheap-web-hosting/serverless.png), but with GCP services instead of AWS.

Below are the yay!s and boo!s along the way. Spoiler alert: destination was not reached.

### GCP first encounter: yaaay!

The way they do the billing is absolutely awesome!!

- So, with AWS's Free Tier, if you want to stay on the "free" side, you set a limit and (alas!) they NOTIFY you when you exceed it! :)) That's all!
  (I did manage to have some unexpected costs when using the free tier, but tbh with them, when I wrote to them, they returned it to me)
- With MS Azure you can also set some limits, but the services will simply not exceed those - this is what I was hoping for with AWS also...
- With Google Cloud Platform, if you want (need) to use resources that exceed the free limit (which is quite generous, btw: https://cloud.google.com/free/) - you need to enable billing!!  
  https://cloud.google.com/appengine/docs/standard/python/console/#billing

### GC NoSQL database: **Firestore**: yay!

- Easy to learn and use. Code snippets are helpful.  
  [The how-to from Google](https://cloud.google.com/firestore/docs/quickstart-servers) was very useful.

### GC Serverless: **Functions**: boo?

At a first glance not so fun like the AWS Lambdas - there seems to be no way to create the functions in place, we create them on our machines and upload them to GCP... :/ AWS was cooler.  
-- nnope, just needed to go to the 'console' thingy.  
https://cloud.google.com/functions/docs/quickstart-console

**BUT**: Have to enable billing to use them?! boo :(...

### GC **Endpoints** -> GC Functions: boo!

Why is it so difficult to point the Endpoints to my Functions? :(  
https://cloud.google.com/endpoints/docs/choose-endpoints-option#isnt_supported  
https://cloud.google.com/endpoints/docs/openapi/get-started-cloud-functions  
https://cloud.google.com/endpoints/docs/openapi/architecture-overview

---

Ok, so unfortunately I think I'll just give it up at this point, it feels like GCP's components are still work in progress and I think I actually prefer getting my hands dirty with code instead.

GCP... So long, and thanks for all the fish.
