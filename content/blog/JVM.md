---
title: "JVM"
date: 2019-08-15
publishdate : 2019-08-15
tags: [optimization,java,jvm,comparative, keycloak,openJ9,hotspot]
image: "https://img.stackshare.io/service/995/K85ZWV2F.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

# State of play
In our case, we will be based on AdoptOpenJDK distributions. 

Given that some difference could be found in OpenJDK  packaging (Redhat, Zulu, ..), our tests will be based on AdoptOpenJDK pacjaging.  This page provides OpenJDK distributions for Linux and Windows platforms.

It could be easier to compare results with the same base.

## OpenJDK Hotspot
OpenJDK (Open Java Development Kit) is a free and open-source implementation of the Java Platform, Standard Edition (Java SE).

## OpenJ9 Hotspot
Eclipse OpenJ9 is a high performance, scalable, Java virtual machine (JVM) implementation that is fully compliant with the Java Virtual Machine Specification.

# Performance
## Streams

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=338344574&single=true" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="400px" width="900px" allowfullscreen></iframe>


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubchart?oid=2060366115&format=interactive" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="400px" width="1000px" allowfullscreen></iframe>


## Microprofile

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=898376284&single=true" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="300px" width="1000px" allowfullscreen></iframe>


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubchart?oid=1610324122&format=interactive" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="400px" width="1100px" allowfullscreen></iframe>


## RH-SSO 7.3.0

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=166042169&single=true" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="300px" width="900px" allowfullscreen></iframe>


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubchart?oid=443597512&format=interactive" style="border:0px #ffffff none;" name="Distribution" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="400px" width="800px" allowfullscreen></iframe>


# Custom JRE distribution
*New paradigm, JRE becomes a part of project packaging.*

## JPMS (Java Platform Module System)
### Standard modules
* List used standard modules
```
jdeps --list-deps CountUppercase
```
* JRE custom generation
```
jlink --no-header-files --no-man-pages --compress=2 --strip-debug --add-modules  $(jdeps --print-module-deps CountUppercase) --output java-base
```

For a no dependency application (CountUppercase.java), we generate a JRE distribution 30MB instead of 116MB ( JRE size) and 320MB for a full JDK.

### Business modules
For a business application with business modules and dependencies as modules , it 's possible to apply the same mechanism and embed only useful modules (business and standard).

