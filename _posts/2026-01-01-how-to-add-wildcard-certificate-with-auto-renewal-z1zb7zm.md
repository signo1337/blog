---
title: How to add Wildcard Certificate with auto Renewal
date: '2026-01-01 07:33:31'
permalink: /post/how-to-add-wildcard-certificate-with-auto-renewal-z1zb7zm.html
tags:
  - safeline
  - waf
  - security
  - guide
  - certificate
layout: single
---





This guide outlines the setup for automating **ECC Wildcard SSL certificates** for SafeLine WAF on a Synology NAS using `acme.sh`​ and the Cloudflare DNS challenge. As an example, I have used `redact.me`. Replace it with your domain.

---

### 🚀 Security & Privacy Benefits

- **Closed Port 80:**  SafeLine normally requires Port 80 to be open for "HTTP-01" challenges. By using the ​**DNS Challenge**, you can close Port 80 on your router entirely. Validation happens via your DNS provider's API, keeping your local network closed to the world.
- **Wildcard Stealth:**  A single `*.redact.me`​ certificate covers all subdomains. This prevents specific service names (e.g., `vault.redact.me`​, `nas.redact.me`​) from being published in public **Certificate Transparency (CT)**  logs, making your internal infrastructure harder for attackers to map.
- **Attack Surface Reduction:**  Fewer open ports and broader encryption coverage significantly harden your network perimeter.

---

### ⚠️ Important Compatibility Notes

- **Manual Path Creation:**  Before starting, you **must manually create** the following folder on your Synology NAS (via File Station or SSH):

  - ​`/volume1/docker/acme`
- **Synology Specific Paths:**  This guide uses `/volume1/...`​ paths. If you are using a different Linux distribution (Ubuntu/Debian), simply change these to your local paths (e.g., `/home/user/acme`).
- **DNS Providers:**  This guide uses ​**Cloudflare**​, but `acme.sh`​ supports 100+ providers. To use another (e.g., GoDaddy, Namecheap), change the `--dns` flag and environment variables.

  - [Check the acme.sh DNS API list here](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

---

### 1. Set the Default CA (Let's Encrypt)

By default, `acme.sh`​ uses ZeroSSL. To ensure compatibility and avoid account registration issues, we manually set **Let's Encrypt** as the default Certificate Authority (CA).

Bash

```
docker run --rm -v "/volume1/docker/acme:/acme.sh" \
  neilpang/acme.sh --set-default-ca --server letsencrypt
```

---

### 2. First-Time Issue (DNS Challenge)

Run this to register your credentials and generate your first cert. *Replace* *​`YOUR_CLOUDFLARE_TOKEN`​*​ *and* *​`YOUR_ACCOUNT_ID`​*​*with your real data.*

Bash

```
docker run --rm -it \
  -e CF_Token="YOUR_CLOUDFLARE_TOKEN" \
  -e CF_Account_ID="YOUR_ACCOUNT_ID" \
  -v "/volume1/docker/acme:/acme.sh" \
  neilpang/acme.sh --issue --dns dns_cf \
  -d "redact.me" -d "*.redact.me" --ecc
```

---

### 3. Link to SafeLine (Install Command)

This "installs" the cert into SafeLine's **ID 1** folder. `acme.sh` will now remember these paths for all future automatic renewals.

Bash

```
docker run --rm -it \
  -v "/volume1/docker/acme:/acme.sh" \
  -v "/volume1/docker/safeline/resources/nginx/certs:/safeline_output" \
  neilpang/acme.sh --install-cert -d "redact.me" --ecc \
  --key-file /safeline_output/cert_1.key \
  --fullchain-file /safeline_output/cert_1.crt
```

---

### 4. Why "Script Mode" instead of "Daemon Mode"?

We run this as a **Script** (via Synology Task Scheduler) rather than a **Daemon** (Always-on container) for several reasons:

1. **Security (The Docker Socket):**  Daemon mode often requires mounting the Docker socket (`/var/run/docker.sock`) 24/7 to reload SafeLine. This is a root-level security risk. Script mode only runs for 30 seconds once a week.
2. **Resources:**  A script uses 0% CPU and 0MB RAM when not in use. A daemon consumes persistent system memory.
3. **Cleanliness:**  If the container crashes in daemon mode, you might not notice. If the Task Scheduler fails, Synology sends you a system notification immediately.

---

### 5. Synology Task Scheduler (The Automation)

1. **Control Panel** \> **Task Scheduler** \> **Create** \> **Scheduled Task** \> ​**User-defined script**.
2. ​**User**​: `root`​ | ​**Schedule**: Weekly (e.g., Sunday 03:00).
3. ​**Run Command**:

Bash

```
#!/bin/bash

# 1. Check for Renewal
# This container runs, checks if the cert is 60+ days old, 
# renews if needed, then deletes itself (--rm).
docker run --rm \
  -v "/volume1/docker/acme:/acme.sh" \
  -v "/volume1/docker/safeline/resources/nginx/certs:/safeline_output" \
  neilpang/acme.sh --cron

# 2. Force SafeLine to Reload
# Even if no renewal happened, this weekly reload ensures the engine 
# is perfectly synced with the files on disk.
docker exec safeline-tengine nginx -s reload

echo "SafeLine SSL Auto-Renew for ID 1 finished at $(date)"
```

---

### 🔍 Troubleshooting

- **Check File Date:**  `openssl x509 -in /volume1/docker/safeline/resources/nginx/certs/cert_1.crt -noout -dates`, via SSH
- **Check Live Site for actual certificate and expiry date:**  SafeLine GUI needs some time to be in sync with the actual certificate
- **Logs:**  Check container logs

‍
