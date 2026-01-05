---
title: 'SafeLine WAF: A self-hosted, open-source Web Application Firewall'
date: '2025-05-06 04:54:13'
permalink: >-
  /post/safeline-waf-a-selfhosted-opensource-web-application-firewall-z2nexvu.html
tags:
  - safeline
  - waf
  - security
categories:
  - selfhosting
layout: single
---





*What* *is* SafeLine WAF? It is a self-hosted Web Application Firewall that helps you protect your web apps from various attacks and exploits. Some features and limitations of the free edition vs. the commercial licenses:

|Features|Personal|Lite|Pro|
| -----------------------------------------| -----------------------------| --------------------| ---------------------------|
|Web Attacks|✅|✅|✅|
|Semantic Analytics Engine|✅|✅|✅|
|HTTP Flood|✅|✅|✅|
|BOT Protect|✅|✅|✅|
|Dynamic Protection|✅|✅|✅|
|Auth (SSO)|✅|✅|✅|
|Access/Error Log|✅|✅|✅|
|Business Geo-Blocking|-|✅|✅|
|Export Attacks Logs|-|✅|✅|
|Discord/Telegram Notifications|-|✅|✅|
|Dynamic Protection-Skip Decryption Page|-|-|✅|
|Advanced Dashboard|-|-|✅|
|Custom Blocking Pages|-|-|✅|
|Isolated Application Security Settings|-|-|✅|
|Upstream Load Balancing|-|-|✅|
|Master-slave Node Config Auto-sync|-|-|✅|
|Anti-Bot/Auth/Waiting Room Logs|-|-|✅|
|Multi-user Management|-|-|✅|
|Data Dashboard|-|-|✅|
|ARM Architecture Deployment|-|-|✅|
|Software Delivery|✅|✅|✅|
|Software Performance|Single thread Mode|Single thread Mode|Ultimate Performance Mode|
|Threat Intelligence|Community Shared Threat IPs|Pro Threat IP|Pro Threat IP|
|App Limit|10|10|Unlimited|

In my opinion, one of the most beneficial features is the option to add an additional security layer to any web application, for example through the use of OIDC. This in turn enables you to enforce MFA on an application that does not support it natively (for example Immich Google Photos alternative or the SiYuan note taking app).

Install guide on Synology NAS and more posts on how to use it are in the pipeline - stay tuned!
