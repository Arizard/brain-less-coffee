---
title: "How to Deploy a Static Hugo Site to Google Cloud Storage"
date: 2019-07-04T12:26:38+10:00
draft: false
---

This assumes you already have a static hugo site generated and you need to 
upload the `/public` directory to the web.

[Source](https://stephenmann.io/post/hosting-a-hugo-site-in-a-google-bucket/)

# Create a Bucket

I want to host this website at https://brain.less.coffee, so I'm going to call my bucket `brain.less.coffee`.

```
gsutil mb gs://brain.less.coffee/
```

The bucket won't let anyone access the content until we edit the default 
permissions.

```
gsutil defacl ch -u allUsers:READER gs://brain.less.coffee
```

Now we can tell the bucket to resolve directories to `index.html` and resolve 
missing pages to `404.html`.

```
gsutil web set -m index.html -e 404.html gs://brain.less.coffee
```

Now we push our website using rsync.

```
gsutil -m rsync -R public gs://brain.less.coffee
```

# Point a domain at a Bucket

Great - the files are available on Google Cloud Storage. Let's make it more 
accessible by pointing a user-friendly domain name to the bucket.

My configuration is through Google Cloud DNS - You might be able to translate 
this to other providers.

```
    DNS Name: brain.less.coffee
    Record type: CNAME
    TTL: 5 Minutes
    Canonical Name: c.storage.googleapis.com
```



