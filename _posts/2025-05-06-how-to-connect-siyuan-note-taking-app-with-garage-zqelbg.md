---
title: How to Connect SiYuan Note Taking App With Garage
date: '2025-05-06 13:27:52'
permalink: /post/how-to-connect-siyuan-note-taking-app-with-garage-zqelbg.html
tags:
  - siyuan
  - garage
  - s3
categories:
  - selfhosting
layout: single
---





In a previous guide, I explained how to install Garage S3 Storage on your Synology NAS. This blog post will show how to connect SiYuan to the previously created bucket.

Open SiYuan and head over to settings -> cloud and enter your details.

- Endpoint: use your domain name or your IP address and port if accessing from within your LAN
- Access Key and Secret Key: as per your set up (key and ID created in the previous guide, for example)
- Bucket: your bucket name, e.g. siyuan-notes as per previous guide
- Region ID: your region ID (relating to my previous guide, you find this information in the garage.toml file, [s3_api]  
  s3_region = "WHATEVERREGION" # can be something simple like "bucket"

- Addressing: Path-style
- TLS Verify: ideally Verify

Finally you can select your directory (bucket should show up correctly) and start syncing. Enjoy!
