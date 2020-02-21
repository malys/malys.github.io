---
title: "JVM"
date: 2019-10-10
publishdate : 2019-10-10
tags: [optimization,java,jvm,comparative, keycloak,openJ9,hotspot]
image:  "images/logo/java.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

Compare different JVM to improve container performance.

## State of play

We will focus on OpenJDK because it's the reference  for JDK, it's an open project and many companies are contributors (Oracle, RedHat, ...).

In our case, we will be based on AdoptOpenJDK distributions.

Given that some difference could be found in OpenJDK  packaging (RedHat, Zulu, ..), our tests will be based on AdoptOpenJDK packaging.  

This page provides OpenJDK distributions for Linux and Windows platforms.
It could be easier to compare results with the same base.

### OpenJDK HotSpot

![](https://developers.redhat.com/blog/wp-content/uploads/2018/09/OpenJDK.png)

OpenJDK (Open Java Development Kit) is a free and open-source implementation of the Java Platform, Standard Edition (Java SE).

*OpenJDK 11* provides new interesting features:

* [Class data sharing](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/class-data-sharing.html) or [CDS](https://gist.github.com/malys/278259d25be9326572b06e51f24f206b#file-bench-sh-L179)
    
    Class data sharing (CDS) helps reduce the startup time for Java programming language applications, in particular smaller applications, as well as reduce footprint. When the JRE is installed using the installer, the installer loads a set of classes from the system jar file into a private internal representation, and dumps that representation to a file, called a "shared archive". If the JRE installer is not being used, this can be done manually, as explained below. During subsequent JVM invocations, the shared archive is memory-mapped in, saving the cost of loading those classes and allowing much of the JVM's metadata for these classes to be shared among multiple JVM processes.

{{< gist malys278259d25be9326572b06e51f24f206b  >}}

* [Java Platform Module System](https://en.wikipedia.org/wiki/Java_Platform_Module_System) (Jigsaw)
    
    The Java Platform Module System[1] specifies a distribution format for collections of Java code and associated resources. It also specifies a repository for storing these collections, or modules, and identifies how they can be discovered, loaded and checked for integrity. It includes features such as namespaces with the aim of fixing some of the shortcomings in the existing JAR format, especially the JAR Hell, which can lead to issues such as classpath and class loading problems.

* Low GC pause by default: G1

#### Container friendly

Before OpenJDK *8u131*, JRE embedded in a container didn't detect allocated resources to a container but it managed host resources. Sometimes, system killed Java process because it passed allocated resources limits.

The solution to detect container resources  was developed for JDK 9 but it was retro-featured in *8u131* as an option.
Since *8u391*, was enabled by default. You can disable it using this parameter *-XX:-UseContainerSupport*.
Obviously, OpenJDK 11 integrates this feature.

### Eclipse OpenJ9 HotSpot

![](https://pbs.twimg.com/profile_images/910853900181364736/bmWkKoLn_400x400.jpg)

Eclipse OpenJ9 is a high performance, scalable, Java virtual machine implementation that is fully compliant with the Java Virtual Machine Specification.

*OpenJ9* provides new interesting features:
* JDK 8-11 supports
* Container friendly by default
* Class Data Sharing
* Dynamic Ahead-Of-Time
* Startup mode (quick, virtualized)

### GraalVM by Oracle

![](https://miro.medium.com/max/655/1*eIW3gooOD5kApk7qw_82lg.png)

GraalVM is a universal virtual machine for running applications written in JavaScript, Python, Ruby, R, JVM-based languages like Java, Scala, Groovy, Kotlin, Clojure, and LLVM-based languages such as C and C++.
It can be considered like the next generation of HotSport JVM.

*GraalVM* provides new interesting features:
* Full JVM Platform 
* Polyglot : Python, Java, Rust, NodeJS over GraalVM
* Graal compiler: full Java compiler, successor of C2 compiler for long time high performance 
* Community edition
* Enterprise edition
  * optimization
  *  debugging
  *  ...
* Native image generation

#### Native compilation

 ![](https://miro.medium.com/max/710/1*0gfk2o71BRI8ny8HI4_Wlg.png)

To improve performance on runtime, *GraalVM* will processing and apply optimization during compilation phase. This mechanism called *Ahead Of Time Compilation* provides a static executable of your application corresponding to a snapshot:
* embedded minimal dependencies VM: Substract VM 
* optimized application
  
It's possible to apply this process to an library or a full Java application.
Static executable is self-sufficient with high performance.

### LTS / Production Ready

For our usage, we want stability and production ready JRE that's why we will use only *[LTS](https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=1089879229&single=true)* of JRE.
*OpenJ9* follows the same lifetime of OpenJDK.
Currently, *GraalVM* supports only JDK 8 but 11's support is in progress.


| JDK        | 1st release | last release |
| :---------: | :-----------: |:-----------: |
| OpenJDK 8  |             |      Q3 2023 |
| OpenJDK 11 |             |      Q4 2024 |
| OpenJDK 17 | Q3 2021     |              |
| GraalVM    | Q2 2019     |              |

### Performance comparative

#### Method

Tests are based on:

* different use cases:
  * Stream only: new Java 8 feature  
  * Microprofile: new Jakarta standard
  * SpringBoot: standard framework  
  * RH-SSO 7.3: AppServer compatible JDK 8 & 11 

* metrics (two [automatic](https://gist.github.com/malys/278259d25be9326572b06e51f24f206b) measures)
  * Start time o process time
  * Memory: RAM usage
  * Latency (mean, 50%, 90%): closed test by thread
  * Throughput: efficiency

#### Comparative

##### Java Streams

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=338344574&single=true" style="border:0px #ffffff none;" name="Stream" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="500px" width="700px" allowfullscreen></iframe>

##### Jakarta Microprofile

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=898376284&single=true" style="border:0px #ffffff none;" name="Microprofile" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="800px" width="1200px" allowfullscreen></iframe>

##### SpringBoot

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=1814932109&single=true" style="border:0px #ffffff none;" name="Spring Boot" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="800px" width="1200px" allowfullscreen></iframe>

##### RH-SSO 7.3

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=166042169&single=true" style="border:0px #ffffff none;" name="RH-SSO7.3" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="500px" width="700px" allowfullscreen></iframe>

##### Results

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=2071947112&single=true" style="border:0px #ffffff none;" name="Results" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="500px" width="800px" allowfullscreen></iframe>

Currently, *OpenJDK 11* is the most polyvalent JRE and provides good performance in most of cases. We recommend to use it by default.

*OpenJ9* could be used for high memory use case because it' s very efficient to minimize memory footprint.
As soon as possible,*OpenJDK8* should be replaced by *OpenJDK11*.

*GraalVM* as JRE is too young and currently, it's not enough efficient.

Some public posts confirms my results and my conclusion. ([1](https://www.optaplanner.org/blog/2019/01/17/HowMuchFasterIsJava11.html), [2](https://royvanrijn.com/blog/2018/05/openj9-jvm-shootout/), [3](https://technology.amis.nl/2018/11/23/comparing-jvm-performance-zulu-openjdk-openjdk-oracle-jdk-graalvm-ce/#prettyPhoto),...) 

### Java Platform Module System

![](https://docs.toradex.com/102686-modularity-icon.jpg)

Modularity can be applied to application with dependencies but also to JRE distribution. 

#### Custom JRE

It's possible to generate a custom and minimal JRE to cover only JRE application needs.

```bash
# JDK11
sdk use java 11.0.4.j9-adpt && du -hcs  $JAVA_HOME
# 300M    /java/11.0.4.j9-adpt
# Compile
javac CountUppercase.java
# Deps
jdeps --print-module-deps CountUppercase.class
# Generate distribution
jlink --no-header-files --no-man-pages --compress=2 --strip-debug \
 --add-modules  $(jdeps --print-module-deps CountUppercase.class) \
 --output java-base
# Custom distribution
ls java-base/ && du -hcs
# bin  conf  legal  lib  release -> java-base/ 39M
JAVA_HOME=$PWD/java-base
```

In the case of an *hello world* with no dependency, the custom distribution is *30MB instead of 116MB for a full JRE*.