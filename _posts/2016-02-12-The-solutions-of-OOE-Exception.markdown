---
layout: post
title:  "The solutions about OOM Exception."
date:   2016-02-12 16:40:00
categories: Java
---

### OOM이 발생한 경우 해결책 중 하나에 대한 기록. 

hs_err_pid[NUM].log 파일에 기록된 내용

```
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (mmap) failed to map 12288 bytes for committing reserved memory.
# Possible reasons:
#   The system is out of physical RAM or swap space
#   In 32 bit mode, the process size limit was hit
# Possible solutions:
#   Reduce memory load on the system
#   Increase physical memory or swap space
#   Check if swap backing store is full
#   Use 64 bit Java on a 64 bit OS
#   Decrease Java heap size (-Xmx/-Xms)
#   Decrease number of Java threads
#   Decrease Java thread stack sizes (-Xss)
#   Set larger code cache with -XX:ReservedCodeCacheSize=
# This output file may be truncated or incomplete.
#
#  Out of Memory Error (os_linux.cpp:2677), pid=31805, tid=140339567539968
#
# JRE version: OpenJDK Runtime Environment (8.0_45-b13) (build 1.8.0_45-b13)
# Java VM: OpenJDK 64-Bit Server VM (25.45-b02 mixed mode linux-amd64 compressed oops)
# Core dump written. Default location: /home/ec2-user/core or core.31805
#

```

Java Thread는 Native Heap 공간에 Stack Trace를 저장할 공간을 필요로 한다. ThreadStack Space의 크기는 -Xss 옵션을 통해 지정된다. -Xss 옵션을 통해지정되는 공간은 개별 Thread가 사용하는 공간이다. 만일 N개의 Thread가 활성화되면 N* 만큼의메모리 공간이 필요하다.

대부분의 OS에서 Thread Stack Size는 512K ~ 1M 사이다. 따라서 많은 수의 Thread가 활성화되면 Thread Stack Space만으로도 큰 크기의 Native Heap 메모리 공간을 소모한다.

Thread Stack Space 문제에 의한 OOME를 해소하는 방법은 다음과 같다.

- Thread의 수를 줄인다. 동시에 수십개 이상의 Thread를 사용하는 것은 메모리의 문제 뿐만 아니라 지나친Context Switching으로 인해 성능을 저하시키는 요인이 된다. Thread Pool 기법을 사용해서 동시 Thread의 수를 줄인다. 대부분의 WAS들이 Thread Pool 기법을 사용하고 있다.
- Thread Stack Size를 줄인다. 대부분의 OS에서 Thread Stack Size는512K ~ 1M이다. 만일 많은 수의 Thread가 필요한 Application이라면 Thread Stack Size를줄임으로써 OOME를 방지할 수 있다. 많은 경우 -Xss128k 정도나 -Xss256k 정도의 크기에서도 문제없이 작동한다.단, Stack Size가 줄어든 만큼 Stack Overflow Error가 발생할 확률은 높아진다.
- Java Heap 크기를 줄인다. 32bit Process가 사용 가능한 메모리 공간은 OS에따라 2G ~ 4G로 제한된다. 하나의 Java Process가 사용 가능한 공간은 [Java Heap+PermanentSpace+Native Heap]으로 이루어진다. 따라서 Java Heap이 지나치게 큰 공간을 사용하는 경우 NativeHeap에서 사용 가능한 공간이 줄어들게 된다. 따라서 Java Heap 크기를 줄이면 Native Heap의 메모리 부족에의한 OOME 문제를 해결 할 수 있다. 하지마 Java Heap 크기를 지나치게 줄이면 Java Heap 부족에 의한 OOME현상이 발생할 수 있으므로 유의해야 한다. Java Heap 크기를 줄이는 방법은 Thread Stack Space의 부족 문제뿐 아니라 Native Heap 부족에 의한 OOME 문제를 줄이는 공통적인 해결 방법이다.
- 64bit JVM을 사용한다. 64bit JVM에서는 32bit JVM Process가 가지는2G ~ 4G의 제한이 없다. 따라서 Native Heap의 메모리 부족 문제가 줄어든다. 이 방법 또한 Native heap부족에 의한 OOME 문제를 줄이는 공통적인 해결 방안이다.

http://ukja.tistory.com/61

참고 : 현재 default stack trace size
```
#[ec2-user@ip ~]$ java -XX:+PrintFlagsFinal -version | grep ThreadStackSize
#     intx CompilerThreadStackSize                   = 0                                   {pd product}
#     intx ThreadStackSize                           = 1024                                {pd product}
#     intx VMThreadStackSize                         = 1024                                {pd product}
#openjdk version "1.8.0_45"
#OpenJDK Runtime Environment (build 1.8.0_45-b13)
#OpenJDK 64-Bit Server VM (build 25.45-b02, mixed mode)
```
