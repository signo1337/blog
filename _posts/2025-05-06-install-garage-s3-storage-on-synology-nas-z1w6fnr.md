---
title: Install Garage S3 Storage on Synology NAS
date: '2025-05-06 12:44:32'
permalink: /post/install-garage-s3-storage-on-synology-nas-z1w6fnr.html
tags:
  - garage
  - s3
  - synology
categories:
  - selfhosting
layout: single
---





In this guide, I will explain how to install Garage ([https://garagehq.deuxfleurs.fr/documentation/quick-start/](https://garagehq.deuxfleurs.fr/documentation/quick-start/)) on Synology NAS. Garage is "an open-source distributed object storage service tailored for self-hosting". The following example will provide instructions to set garage up without replication (i.e. a single bucket)

## Creating Folder Structure

First of all, you will need to create the following folder structure on your Syno:

- /volume1/docker/garage
- /volume1/docker/garage/meta
- /volume1/docker/garage/data

## Create garage.toml

In the root folder of garage, create a file, name it "garage.toml" and add the following content:

```toml
metadata_dir = "/var/lib/garage/meta"
data_dir = "/var/lib/garage/data"
db_engine = "lmdb"

replication_mode = "none"
# idk why rcp_ is needed without replication but ok 
rpc_bind_addr = "[::]:3901"
rpc_public_addr = "127.0.0.1:3901"
rpc_secret = "SOMESECRET" # you can use "openssl rand -hex 32" to create the token

compression_level = 2

[s3_api]
s3_region = "WHATEVERREGION" # you can select any name, simple as "bucket"
api_bind_addr = "[::]:3900"
root_domain = ".s3.garage.localhost" # or garage.sample.com, for example

[s3_web]
bind_addr = "[::]:3902"
root_domain = ".web.garage.localhost"
index = "index.html"

[admin]
api_bind_addr = "0.0.0.0:3903"
metrics_token = "SOMETOKEN" # you can use "openssl rand -base64 32" to create a token
admin_token = "SOMEADMINTOKEN" # you can use "openssl rand -base64 32" to create a token
```

Make sure to add some secure string instead of SOMESECRET, SOMETOKEN and SOMEADMINTOKEN. Also, adjust to your domain and the region. In my case, I only needed the root_domain for the API.

## Create the docker-compose.yaml

Next step is to create a docker-compose as follows:

```yaml
services:
  garage:
    image: dxflrs/garage:v1.1.0
    container_name: garage
    user: 1026:100 # your UID and GID; if not known, check this guide by Marius: https://mariushosting.com/synology-how-to-find-uid-userid-and-gid-groupid/
    restart: unless-stopped
    environment:
      - GARAGE_ADMIN_TOKEN=SOMEADMINTOKEN # use the same token as you placed in the garage.toml file
      - TZ=Europe/Madrid # change to your timezone
    ports:
      - 3900:3900
      - 3901:3901
      - 3902:3902
      - 3903:3903
    volumes:
      - /volume1/docker/garage/garage.toml:/etc/garage.toml
      - /volume1/docker/garage/meta:/var/lib/garage/meta
      - /volume1/docker/garage/data:/var/lib/garage/data
```

This should get the container up and running.

Before you can use it as an S3 storage, you need to run some commands inside of the garage docker container, to check the status, create a cluster layout, a bucket and a key.

## Initialize the Storage

SSH into your NAS, and paste this command:

​`alias garage="docker exec -ti <container name> /garage"`

This creates an alias for the further commands. You can also skip the command and use `docker exec -ti <container name> /garage` before every command instead.

To test if everything worked, you can now run `garage status` and should see something like this:

```shell
==== HEALTHY NODES ====
ID                 Hostname  Address         Tag                   Zone  Capacity
783ebcea23bb7a5   testenv  127.0.0.1:3901  NO ROLE ASSIGNED
```

Let's create now a cluster layout, by entering this command, whereas the node_id corresponds to the Zone ID (output from previous command):

​`garage layout assign -z dc1 -c 1G <node_id>`

-  *-c* denotes the capacity you want to assign to this bucket. Completely up to you. I'd go with something between 10G and 100G for SiYuan (note taking app) as an example, depending on your needs

-  *-z dc1* -> an abbreviation for the zone you are creating, here for example short for datacenter1 (can be anything)

After this, the layout has to be applied with:

​`garage layout apply --version 1`

## Creating Bucket and Key

Let's assume we want to create a bucket to store SiYuan notes we can create a bucket like this:

​`garage bucket create siyuan-notes`

To verify that the bucket is created, we run:

​`garage bucket list`​ and `garage bucket info siyuan-notes`

The next step is to create and assign an API-key:

​`garage key create siyuan-key`

The output should show a Key ID and a Secret Key, with empty Authorized Buckets (we will authorize the siyuan-notes bucket in the next steps).

Another quick check, that all went well:

```shell
garage key list
garage key info siyuan-key
```

Authorize the bucket:

​`garage bucket allow --read --write --owner siyuan-notes --key siyuan-key`

Next we run this command again, this time the Authorized Buckets should not be empty anymore:

​`garage bucket info siyuan-notes`

The bucket is now ready to be filled with whatever data you choose. For a guide on how to connect SiYuan to your bucket, stay tuned for my next post.
