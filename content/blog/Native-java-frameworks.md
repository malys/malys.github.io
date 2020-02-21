---
title: "Native Java frameworks"
date: 2019-09-10
publishdate : 2019-10-10
tags: [graalvm,java,framework,comparative]
image: "images/logo/java.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

Compare different Java framework to generate native application.


## Native

Static executable provides best performance on runtime because it's compiled for a specific platform.

![](https://img.stackshare.io/stack/443157/m1edT_tX_400x400.jpg) 

With Golang, it's possible to generate a minimalist executable. This solution is perfect for  architectures based on  microservices or functions.

![](https://miro.medium.com/max/655/1*eIW3gooOD5kApk7qw_82lg.png)

Before GraalVM (Ahead of time compilation), Java wasn't be able to compete with GoLang or NodeJS. Deploying and running Java application on cloud platforms were not natural and easy.

With the aim of proposing Java cloud solution, community developed [microframeworks](https://www.e4developer.com/2018/06/02/the-rise-of-java-microframeworks/).

## Candidates

We have selected 4 frameworks to evaluate:

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=675234315&single=true" style="border:0px #ffffff none;" name="Stream" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="700px" width="900px" allowfullscreen></iframe>

<script type="text/javascript" src="https://ssl.gstatic.com/trends_nrtr/1937_RC01/embed_loader.js"></script> <script type="text/javascript"> trends.embed.renderExploreWidget("TIMESERIES", {"comparisonItem":[{"keyword":"quarkus","geo":"","time":"today 12-m"},{"keyword":"helidon","geo":"","time":"today 12-m"},{"keyword":"micronaut","geo":"","time":"today 12-m"}],"category":5,"property":""}, {"exploreQuery":"cat=5&q=quarkus,helidon,micronaut&date=today 12-m,today 12-m,today 12-m","guestPath":"https://trends.google.com:443/trends/embed/"}); </script>

### Code comparative

* Micronaut vs Quarkus

```diff
-import io.micronaut.http.annotation.Controller;
+import javax.inject.Inject; 
 
-@Controller("/conferences") 
+@Path("/conferences")
+@Produces(MediaType.APPLICATION_JSON)
 public class ConferenceController {
-    private final ConferenceService conferenceService;
+    @Inject
+    private ConferenceService conferenceService;
-    public ConferenceController(ConferenceService conferenceService) {
-        this.conferenceService = conferenceService;
-    }
-    @Get("/random") 
-    public Conference randomConf() { 
+    @Path("/random")
+    @GET
+    public Conference randomConf() { 
         return conferenceService.randomConf();
     }
 }
```

* Quarkus vs Spring

```diff
-import io.micronaut.http.annotation.Controller;
+import org.springframework.web.bind.annotation.RequestMapping;
 
-@Controller("/conferences") 
+@RestController
 public class ConferenceController {
+    @Autowired
+    private ConferenceService conferenceService;
-    private final ConferenceService conferenceService;
-    public ConferenceController(ConferenceService conferenceService) { 
-        this.conferenceService = conferenceService;
-    }
-
-    @Get("/random") 
-    public Conference randomConf() { 
+    @RequestMapping("/conferences/random")
+    public Conference randomConf() {
         return conferenceService.randomConf();
     }
 }
```

There isn't a big difference about syntaxes. Each selected has been thought to be productive and make happy developers.

### Performance

*Spring* is a very popular technology and some frameworks propose partial compatibility with Spring annotation.

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=2096152561&single=true" style="border:0px #ffffff none;" name="Stream" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="600px" width="1100px" allowfullscreen></iframe>

At then end, *Spring* compatibility is always limited and sometimes unstable. We recommend to not use them.
*Helidon* is not yet mature and discarded.

*Spring* doesn't propose native image generation. Pivotal will integrate this feature in 5.3 release.

*Micronaut* is interesting but it isn't base on standard library. It's out of running.

*Quarkus* is young but it was quickly adopted. It's supported by RedHat and community and based on standard library (microprofile) and all RedHat stack (Vertx, ...).
In the opinion of performance, it's currently the best choice.

Deal with Quarkus in depth!

### Quarkus

Quarkus is an Supersonic Subatomic Java framework.

Quarkus rewrites library to be more efficient with AOT of *GraalVM*.

<img class="special-img-class" style="width:50%" src="/images/quarkus_how_to.png" />

This method improves performance drastically.

<img class="special-img-class" style="width:50%" src="/images/quarkus_perf.png" />

We have migrate a standard microprofile application to Quarkus.

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vTlum2-EkQbcQiR0xuJAatsmiub8ky3MH8ZIjfVT-ZI6Iw2rwisZ9yolP1HPWhLX22afu22EVUUVLOd/pubhtml?gid=1079046419&single=true" style="border:0px #ffffff none;" name="Stream" scrolling="no" frameborder="0" marginheight="0px" marginwidth="0px" height="800px" width="1100px" allowfullscreen></iframe>

The new application performance is record breaking time of:
* memory footprint
* CPU 
* Size
* Reactivity

#### Limitations


| Not Supported                                                                                                            | Limited                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| Dynamic Classloading                                                                                                     | [CDI](https://quarkus.io/guides/cdi-reference#limitations)                                             |
| [Invoke Dynamic](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/multiple-language-support.html#invokedynamic) | Reflection                                                                                             |
| [Finalizer](https://dzone.com/articles/javas-finalizer-is-still-there)                                                   | [Dymanic proxy](https://www.baeldung.com/java-dynamic-proxies)                                         |
| [Security Manager](https://docs.oracle.com/javase/tutorial/essential/environment/security.html)                          | JNI                                                                                                    |
| [JVM TI, JMX, ...](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html)                                      | [Static initializer](https://www.dummies.com/programming/java/how-to-use-static-initializers-in-java/) |
|                                                                                                                          | Native Windows                                                                                         |
|                                                                                                                          | Native debug                                                                                           |

## Conclusion

*Quarkus + Microcontainer* is the future of Java for microservices or function.