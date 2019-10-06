---
title: "JRE Dependencies with host"
date: 2019-08-19
publishdate : 2019-08-19
tags: [jre,java]
image: "https://img.stackshare.io/service/995/K85ZWV2F.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

What are JRE depencies with host OS ?

## Dependencies 

```bash
ldd /usr/bin/java
        linux-vdso.so.1 =>  (0x00007ffff917a000) 
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f6fcb946000)
        libz.so.1 => /lib64/libz.so.1 (0x00007f6fcb730000)
        libjli.so => not found
        libdl.so.2 => /lib64/libdl.so.2 (0x00007f6fcb52c000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f6fcb15f000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f6fcbb62000)
```

## Definition

* vdso: a kernel mechanism for exporting a carefully selected set of kernel space routines to user space applications so that applications can call these kernel space routines in-process, without incurring the performance penalty of a context switch that is inherent when calling these same kernel space routines by means of the system call interface
* libpthread: multithreadinf
* libz: zlib (compression)
* libjli: JNI alternative (??)
* libdl: dynamic linkind support
* libc: C language standard library
* ld-linux: dynamic linker/loader
