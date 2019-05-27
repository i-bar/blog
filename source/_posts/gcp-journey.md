---
title: Serverless with GCP - impressions
date: 2019-05-23 16:14:30
tags:
  - GCP
  - Serverless
---

This entry collects the steps and thoughts for building a serverless application with Google Cloud Platform services.

The intention was to build a project with an architecture similar to the following one, but with GCP services instead of AWS:
![this one](https://cdn.patricktriest.com/blog/images/posts/cheap-web-hosting/serverless.png)

Below are the *yay!*s and *boo!*s along the way.  
Spoiler alert: the destination was not reached.

### GCP first encounter: the billing (yaaay!)

The way they do the billing is absolutely awesome!!  
(another spoiler alert - it didn't take long to change my mind about this)

- So, with **AWS**'s Free Tier, if you want to stay on the "free" side, you set a limit and (alas!) they NOTIFY you when you exceed it! :)) That's all!
  (I did manage to have some unexpected costs when using the free tier, but tbh with them, when I wrote, they returned it to me)
- With **MS Azure** you can also set some limits, but the services will simply not exceed those - this is what I was hoping for with AWS also...
- With **Google Cloud Platform**, if you want (need) to use resources that exceed the free limit (which is quite generous, btw: https://cloud.google.com/free/) - you need to enable billing!!  
  https://cloud.google.com/appengine/docs/standard/python/console/#billing  
  Also another awesome part is that the services are **stopped when the limit is exceeded**! No extra charges! In-your-face, AWS! :)

**This point got dismissed!!**  
This proved to be a false alarm. I mean, yes, we can use **some** services without enabling the billing part, but for using most of them - even those that offer a generous free usage - we need to enable billing :/.  
Whyyy.....

### GC NoSQL database: **Firestore** (yay!)

_"Cloud **Firestore** is a fast, fully managed, serverless, cloud-native NoSQL document database that simplifies storing, syncing, and querying data for your mobile, web, and IoT apps at global scale."_ ([GCP Firestore docs](https://cloud.google.com/firestore/docs/))

- Easy to learn and use. Code snippets are helpful.  
  [The how-to from Google](https://cloud.google.com/firestore/docs/quickstart-servers) was very useful.

The `node code` used to **add new data** to the database:

```javascript
require('dotenv').config();
const Firestore = require('@google-cloud/firestore');

const db = new Firestore({
  projectId: 'hello-world-ib',
  keyFilename: process.env.GOOGLE_APPLICATION_CREDENTIALS,
});

var spiDoc = db.collection('volops').doc('cop');
var setCopii = spiDoc.set({
  name: 'Copii',
  desc: 'Descriere mare',
  address: 'adresa',
});

var batDoc = db.collection('volops').doc('bat');
var setBatrani = batDoc.set({
  name: 'Batrani',
  desc: 'Descriere mare batrani',
  address: 'adresa batrani',
});

Promise.all([setCopii, setBatrani]);
```

The `node code` used to **print the data** in the database:

```javascript
require('dotenv').config();
const Firestore = require('@google-cloud/firestore');

const db = new Firestore({
  projectId: 'hello-world-ib',
  keyFilename: process.env.GOOGLE_APPLICATION_CREDENTIALS,
});

db.collection('volops')
  .get()
  .then(snapshot => {
    const array = [];
    snapshot.forEach(doc => {
      array.push({
        doc: doc.id,
        data: doc.data(),
      });
      console.log(doc.id, ' => ', doc.data());
    });
    console.log('collection: ', array);
  })
  .catch(err => console.log('err: ', err));
```

### GC Serverless: **Functions** (boo?)

_"Google Cloud **Functions** is a lightweight compute solution for developers to create single-purpose, stand-alone functions that respond to Cloud events without the need to manage a server or runtime environment."_ ([GCP Functions docs](https://cloud.google.com/functions/docs/))

There are two ways to create a Function:

1. By using the Console, we write the function directly in the browser. Drawback is that it takes a while to test them:
   https://cloud.google.com/functions/docs/quickstart-console
2. By using the GCP cli, but I really didn't like this approach because the recommended way is to clone [this _huge_ repo](https://github.com/GoogleCloudPlatform/nodejs-docs-samples) in order to create the first small 'hello world' function :|.
   https://cloud.google.com/functions/docs/quickstart

<!-- <details>
<summary>Click to expand the `node code` for a Function that **responds to GET requests** and returns the data in the Firestore db previously created.</summary> -->

The `node code` for a Function that **responds to GET requests** and returns the data in the Firestore db previously created:

```javascript
require('dotenv').config();
const Firestore = require('@google-cloud/firestore');

const db = new Firestore({
  projectId: 'hello-world-ib',
  keyFilename: process.env.GOOGLE_APPLICATION_CREDENTIALS,
});

db.collection('volops')
  .get()
  .then(snapshot => {
    const array = [];
    snapshot.forEach(doc => {
      array.push({
        doc: doc.id,
        data: doc.data(),
      });
      console.log(doc.id, ' => ', doc.data());
    });
    console.log('collection: ', array);
  })
  .catch(err => console.log('err: ', err));
```

<!-- </details> -->

**However... thumbs down for the billing**: Have to enable billing to use them for free?! Whaaa :(...

### GC API Gateway: **Endpoints** (boo!)

_"**Endpoints** is an API management system that helps you secure, monitor, analyze, and set quotas on your APIs using the same infrastructure Google uses for its own APIs."_ ([GCP Endpoints docs](https://cloud.google.com/endpoints/docs/))

The plan was to configure the Endpoints as an API gateway, a **single point of entry** for the app that would redirect calls to the right Function.

For mysteryous reasons this proved to be far from straight-forward:

- First, it seemed [impossible](https://cloud.google.com/endpoints/docs/choose-endpoints-option#isnt_supported);
- Then, we find out it is actually possible, but in an [alpha, pre-release state](https://cloud.google.com/endpoints/docs/openapi/get-started-cloud-functions);
- The [endpoints architecture](https://cloud.google.com/endpoints/docs/openapi/architecture-overview) might also be helpful for better understanding it.

---

Ok, so unfortunately I think I'll just give it up at this point, it feels like these GCP's components are still work in progress (at least for the serverless project) and I think I actually prefer getting my hands dirty with code instead.

GCP... So long, and thanks for all the fish.

---

_Big thanks to [David](https://github.com/davified) for his helpful feedback - and actually for the whole blog incentive :)._
