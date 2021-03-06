---
title: "Understand: From scratch"
date: 2019-08-19
publishdate : 2019-08-19
tags: [scratch,docker]
image: "images/logo/docker.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

How "from scratch" works ?

## Image

```
FROM scratch
ENV PROXY_LISTEN=0.0.0.0:8443
ENV PROXY_LISTEN_HTTP=0.0.0.0:8080

EXPOSE 8080 8443

ADD keycloak-gatekeeper /
ADD config.yaml /config.yaml
CMD ["/keycloak-gatekeeper","--config","config.yaml"]
```

```
d build -t keycloak-gatekeeper:scratch .

# No file except keycloak-gatekeeper
dive keycloak-gatekeeper:scratch
{
  "layer": [
    {
      "index": 0,
      "digestId": "sha256:5615180f624e082f675261de139dca5eefe09ced8653bf25e02cb1075ace24dc",
      "sizeBytes": 12768011,
      "command": "#(nop) ADD file:40e67ed7d86014658f5b01316b9193210dc93bfe2bdf1ee9ae9c714635c9699c in / "
    },
    {
      "index": 1,
      "digestId": "sha256:bd785d8e829b3305fc45051b901d54805e1c536404359eb1084d754cf643b69c",
      "sizeBytes": 501,
      "command": "#(nop) ADD file:3704f000080497680b5aeae92f9cfadddbc385c4dacc87df1b8d0b072d46e63e in /config.yaml "
    }
  ],
  "image": {
    "sizeBytes": 12768512,
    "inefficientBytes": 0,
    "efficiencyScore": 1,
    "inefficientFiles": []
  }
}
```


## Container

```
# Run
d run --rm keycloak-gatekeeper:scratch &

## Attach alpine
doatt determined_haibt
/ # ls -al /proc/1/root
lrwxrwxrwx    1 root     root             0 Aug 12 12:36 /proc/1/root -> /
/ # ls -al /proc/1/root/
total 12476
drwxr-xr-x    7 root     root           126 Aug 12 12:35 .
drwxr-xr-x    7 root     root           126 Aug 12 12:35 ..
-rwxr-xr-x    1 root     root             0 Aug 12 12:35 .dockerenv
-rwxr-xr-x    1 root     root           501 Aug 12 12:27 config.yaml        
drwxr-xr-x    5 root     root           340 Aug 12 12:35 dev
drwxr-xr-x    2 root     root            66 Aug 12 12:35 etc
-rwxr-xr-x    1 root     root      12768011 Aug 12 12:26 keycloak-gatekeeper
dr-xr-xr-x  147 root     root             0 Aug 12 12:35 proc
drwxr-xr-x    3 root     root            21 Aug 12 12:35 run
dr-xr-xr-x   13 root     root             0 Aug 12 11:37 sys
/ # ls -al /proc/1/root/etc
total 12
drwxr-xr-x    2 root     root            66 Aug 12 12:35 .
drwxr-xr-x    7 root     root           126 Aug 12 12:35 ..
-rw-r--r--    1 root     root            13 Aug 12 12:35 hostname
-rw-r--r--    1 root     root           174 Aug 12 12:35 hosts
lrwxrwxrwx    1 root     root            12 Aug 12 12:35 mtab -> /proc/mounts
-rw-r--r--    1 root     root            92 Aug 12 12:35 resolv.conf
```

Containers includes standard linux folder structure (see specification).

## Reference

* [Scratch blog post](https://www.mgasch.com/post/scratch/)

