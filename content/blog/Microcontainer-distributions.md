---
title: "Microcontainer distributions"
date: 2019-08-15
publishdate : 2019-08-15
tags: [optimization,java,microcontainer,comparative]
image: "https://img.stackshare.io/service/586/logo_docker.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

# Goal
Container must include minimal dependencies to run Java application (microservice or legacy application).

# Sizing
Creating a minimalist image will decrease size and improve performance to download it. It will also limit disk storage on docker registry and docker host (Layer duplication, automatic pull of base image).

The same service with less resources. (Ubuntu docker base image to alpine, Eclipse to distroless, Kubernetes to distroless) 

# Security
NIST recommendation: "An image should only include the executable and libraries required by the app itself; all other OS functionality is provided by the OS kernel within the underlying host OS".

Attack surface vulnerability are limited.
Container immutability is guarantee (no manual connection into a container)
Secure secret variables unreachable (no remote connection, no shell, no exposition)

# Useful Tools
dive: image and layer size
ex: dive image_name 

# State of play
## Sizing comparison

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=12271636&single=true" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="500px" width="1000px" allowfullscreen></iframe>

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubchart?oid=1977800902&format=interactive" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="400px" width="1000px" allowfullscreen></iframe>

# Strategies
## Lightweight distributions 
### Base image comparison

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=1361748970&single=true" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="900px" width="1100px" allowfullscreen></iframe>

#### Distroless
* support of Java 8 and 11 
* minimalist distribution
* Custom packaging engine
* glibc (for compatibility with old kernel)
* debug image
* Minimal attack surface
* Quick update 
* Limited storage and networking

#### Alpine
* most common base image (possibility to use public image)
* musl glibc ⇾ compatibility issue
* java image add glibc

#### GLIBC vs musl c
* GLIBC: standard C library
* musl c
http://www.musl-libc.org/faq.html
Is musl compatible with glibc?
Yes and no. At both the source and binary level, musl aims for a degree of feature-compatibility, but not bug-compatibility, with glibc. When applications that work with glibc fail to compile against musl, the cause will usually be one of the following:

Assuming that including one header will cause symbols from another unrelated header to be exposed. This is an application bug, and fixing it is as simple as adding the missing #include directives.
Using implementation details from the glibc headers which were not meant to be exposed to applications. This is also an application bug, and it can usually be fixed by search-and-replace (e.g. replacing __pid_t with pid_t in the source).
Use of an interface that’s not implemented in musl. This can only be fixed by making the application’s use of the interface optional, or by extending musl to support the missing interface.
Binary compatibility is much more limited, but it will steadily increase with new versions of musl. At present, some glibc-linked shared libraries can be loaded with musl, but all but the simplest glibc-linked applications will fail if musl is dropped-in in place of /lib/ld-linux.so.2.

## Contains optimization
*New paradigm, Container becomes a part of project packaging.*

### Docker-slim: https://github.com/docker-slim/docker-slim
* Since December 2014
* 8 contributors
* Goal:  Don't change anything in your Docker container image and minify it by up to 30x (and for compiled languages even more) making it secure too! (free and open source) 
Examples
    Keystore
    Based on distroless
    quarkus-keystore-distroless 78.8MB

    quarkus-keystore-disroless.slim 65.3MB

    Based on CentOS
    quarkus-keystore-centos 248MB

    quarkus-keystore-centos.slim 65.7MB

    Same target image size.

    Keycloak Gatekeeper (Static binary)
    Based on CentOS
    rh-sso/keycloak-gatekeeper60 200 Mo

    rh-sso/keycloak-gatekeeper60.slim 20 Mo :  d run -v /D/Developpement/archi/idm/keycloak-gatekeeper-test/config.yaml:/etc/keycloak-gatekeeper/config.yaml  rh-sso/keycloak-gatekeeper60.slim


#### Security
* selinux ?
* seccomp
* a CVE on image base does not always consequences to redeploy child image (CVE for curl but it's removed by docker-slim => final image doesn't change => deployment is no necessary ?)

#### Automation
Run container for introspection on build. CI platform runs containers to containers

### Minicon https://github.com/grycap/minicon
* Since November 2017
* 3 contributors
* Goal: Reducing the footprint of one container is of special interest, to redistribute the container images.

*Not tested because it's the same tool as docker-slim but youngest and less supported.*