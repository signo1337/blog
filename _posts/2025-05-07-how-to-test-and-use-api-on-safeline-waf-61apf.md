---
title: How to Test and Use API on SafeLine WAF
date: '2025-05-07 07:57:15'
permalink: /post/how-to-test-and-use-api-on-safeline-waf-61apf.html
tags:
  - safeline
  - waf
  - security
  - api
  - guide
categories:
  - selfhosting
layout: single
---





In this quick tutorial I will show you how you can test and potentially use the API connection of SafeLine WAF. For this purpose, I will use Hoppscotch desktop application ([https://docs.hoppscotch.io/documentation/clients/desktop](https://docs.hoppscotch.io/documentation/clients/desktop)). There is an online version, too, but it is better to use a local installation due to security best practices. The API documentation of SafeLine WAF can be reached via your SafeLine instance `Settings > Management > API DOC` and contains the available commands. Here you can also create your API Token.

Here an excerpt:

![image](/assets/images/image-20250506140614-5tth8zf.png)

Let's test the API:

- first thing is to disable SSL verification, if you use the API via LAN / IP address. `Hoppscotch > Settings > Interceptor > Global Defaults`:

![image](/assets/images/image-20250506143037-16wjd6h.png)

- next we construct a basic request, like retrieving the attack records:

![image](/assets/images/image-20250506143320-8t9dc8f.png)

- basically you only need to set the request to `GET`​, enter `https://yoursafelineip:yoursafelineport/api/open/records`​ (or any other requests you find in the API DOC) and then most importantly head over to the `Headers`​ ​tab, add the variable `X-SLICE-API-TOKEN` with the value of your token (which you can create in the same section of SafeLine WAF, as you find the API DOC)
- Click "Send" and enjoy the result

Having an API-connection to SafeLine WAF is highly appreciated, as this lets you automate things for example with tools like n8n. You can set up notifications for logins, for attacks, change settings etc. Big thanks to SafeLine for providing the option to use the community edition for free.

Stay tuned!
