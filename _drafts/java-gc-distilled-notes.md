---
layout: post
tags:
  - java
title: Notes on Java Garbage Collection Distilled
---
[Java Garbage Collection Distilled](http://www.infoq.com/articles/Java_Garbage_Collection_Distilled){:target="_blank"}

### Heap Organization

  1. Eden and Survior (young or new generation)
  1. Tenured
  1. Perm

<!--more-->

![Hotspot Heap Organization]({{ site.url }}/images/posts/2014-10-09/heap-organization.jpg)

### Object Allocation

TLAB: Thread Local Allocation Buffer

### Minor Collections

In Hotspot minor collections are stop-the-world events.

### Types of Collectors

  1. Serial Collector
  1. Parallel Collector
  1. Concurrent Mark Sweep (CMS) Collector
