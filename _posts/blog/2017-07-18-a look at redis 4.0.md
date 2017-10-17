---
layout: post
title: A Look at Redis 4.0
description: "With the release of 4.0, Redis comes with a long list of much sought-after improvements, let's take a look at the major items in the improvements list and know what it means"
categories: blog
comments: true
tags: "Redis NewReleases Improvements"
excerpt: ""
share: true
readtime: true
---

I have been using Redis in production for almost seven years and it has never let me down, I can vouch by its reliability and performance. But few things I wished were part of Redis, have now been introduced in the release 4.0 and that makes it even more useful for us.

With the release of 4.0, Redis comes with a long list of much sought-after improvements, let's take a look at the major items in the improvements list and know what it means:

- [Active Memory Defragmentation](#active-memory-defragmentation)

 - [Improvements to Eviction Policies](#improvements-to-eviction-policies)

 - [Mixed RDB+AOF Format](#mixed-rdb-aof-format)

-  [Modules System](#modules-system)

-  [Much Better Replication (psync2)](#much-better-replication-psync2)

-  [Raspberry Pi Support As Primary Platform](#raspberry-pi-support-as-primary-platform)

-  [Redis Cluster Support for Nat/docker](#redis-cluster-support-for-nat-docker)

-  [The New Memory Command](#the-new-memory-command)

-  [Threaded Del/Flush](#threaded-del-flush)

## Active Memory Defragmentation

Once I observed Redis taking up all the memory and then crash, I had `v2.8.2` running on a Ubuntu `14.04` VPS with 8 GB RAM and 16 GB swap space on a SSD drive, `redis-server` eventually crashed due to out of memory.

The reason, I figured out was, not using the `maxmemory` to set a limit to how much Redis database can grow up to. Failing to do so, Redis grew until the OS killed it due to low memory.

But since Redis is well-known, widely used and loved in production, it couldn't be a memory leak for sure. Further investigation revealed that it was memory fragmentation problem.

Memory fragmentation is largely related to the allocator you are using e.g. `glibc` `malloc`, `jemalloc`, `tcmalloc` and freeing up unused memory was up to the OS.

Now with active memory defragmentation things are a bit more in control, specifically with high delete load actually freeing up unused memory in a timely manner without impacting performance too much.

Now the question is - is Redis managing this itself using simple calls to `jemalloc`, or is `jemalloc` doing it on its own because it has better algorithms than the OS-

As per the [discussion](https://github.com/jemalloc/jemalloc/issues/566) between Redis and jemalloc devs regarding this -- jemalloc is not quite there yet.

Redis will perform its own housekeeping, hence the usage of the term 'active'. [Seems](https://github.com/antirez/redis/pull/3720), rather than metadata being stored for this, there is a periodic active scan and manual measurement.

Previously, to fully recover unused memory, you would have to restart the Redis server. Obviously, it is not feasible but when Redis is using more than 50% memory on a 120GB machine, you were forced to consider restarting as an occasional housekeeping option -- now this ridiculous task is no longer necessary.


## Improvements to Eviction Policies

Redis is widely used for caching, they say most difficult problems in programming are naming things and invalidating cache. A [write up](http://antirez.com/news/109) by Salvatore Sanfilippo, if you want to get into the details.

In short, Redis now has LFU (Last Frequently Used) as a new eviction algorithm, and all the other eviction policies have been boosted to a more robust, fast and precise implementation.

As the existing algorithms also see improvements in terms of functionality, performances and precision, it is a big news for caching use cases.

>LRU + LFU: Better Eviction = Better Caches


## Mixed RDB AOF Format

This feature was talked about for a long time and now finally has found it's place in this release, once enable, AOF rewrites are performed by prefixing the AOF file with an RDB file, which is both faster to generate and to load.

But still it is as an optional thing for now because even though very useful in certain environments, it makes AOF file less transparent.

## Modules System

Modules System leverages Redis capability as a framework to build things and it's one of the biggest and long-awaited features in Release 4.0.0. The modules system, originally announced at RedisConf 2016,  provides an API for extending Redis with dynamically loaded modules primarily in C.  The modules API provides multiple levels of API for developers to add new features and functionality to Redis.

With Redis Modules, you can add new operations to existing data types, introduce new data types, such as JSON, or extend Redis with new processes like Search or Neural Network. Try adding sprinkle of AI to Redis. Salvatore wrote [Neural Redis](https://github.com/antirez/neural-redis), a neural network data type that can be trained inside Redis itself.

The new API enables you to build extensions that were either impractical or not performant enough when built with Lua.  As mentioned in the documentation for modules on Redis.io:

>Redis modules make it possible to extend Redis functionality using external modules, implementing new Redis commands at a speed and with features similar to what can be done inside the core itself.

Modules system can be used to do pretty interesting things like implementing new data types that are RDB/AOF persisted, create non blocking commands, rate limiting commands, Graph DBs on top of Redis, secondary indexes, time series modules, full text indexing, and so forth. As an added benefit, all this is done with a higher level abstract API completely separated from the core, so the modules you write are going to work with future releases of Redis. This allows Redis to grow and cover new things, the core is guarded, but the playground is open for new experiments.


## Much Better Replication (psync2)

Sysadmins are going to love this feature because it's very useful from operations perspective.

Sometime back `PSYNC` was introduced to Redis, it was a new master-slave protocol that allowed master and salve to continue from where they were, if the connection between the two broke.

Before that, every single connection break in the replication link between master and slave would require a full synchronization- generate an RDB file in the master, transfer it, load it in the slave, it was hell of a prosaic task. So `PSYNC` was a savior.

But `PSYNC` was not good enough when there was a failover configuration in place. If a slave is promoted to master, the slaves that replicated with the old master were not able to connect to the new promoted slave and `PSYNC` with it, yet again a full resynchronization was needed. This still was a limitation, and was not good for Redis Cluster either.

Fixing this required change in how *chained replication* works, that is, slaves of slaves of slaves … How do they work? Like, A is the master of B but B is the master of C and so forth:

>A —> B —> C —> D

Prior to Redis 4.0, every master was handing down it's own replication protocol, which normally, is a stream of write commands to their slaves. E.g. when B received the replication protocol from A. B acted as a master for C just doing, internally, what A was doing, at every write it was generating again a suitable replication protocol to pass to C, and so on.

Now `PSYNC2`, instead B just proxies, and directs, what it receives from A to C, and C will do the same for D. Ensuring that all the sub slaves now receive an identical stream of bytes, they can *tag* a given history, and use the tag and the offset to always try to continue if they have something in common.

Now if a master is turned into a slave, it can `PSYNC` with the new master. The replication tags and offsets are stored in the RDB file, slaves can usually `PSYNC` with the master even after a *clean* restart, using tag and offsets information from RDB file.

## Raspberry Pi Support As Primary Platform

Raspberry Pi are now officially supported platforms, and every new release of Redis is tested on the Pi environment. It opens up a plethora of opportunities for IoT and Embedded devices for several reasons:

- Redis has a very small memory footprint and CPU requirements. With 4.0 Redis now uses less memory in order to store the same amount of data.

- The data structures of Redis are often a good way to model IoT/embedded use cases.
E.g. accumulate time series data, receive or queue commands to execute or send responses back to the remote servers.

- Data modelled in Redis, that is residing in device can be harnessed to make quick decisions even in offline mode, when device is not connected to remote servers.

- Redis can be used as an inter-process communication system between the processes running in the device.

- The append only file storage of Redis is well suited for the SSD cards.


## Redis Cluster Support for Nat Docker

Redis Cluster is now NAT / Docker compatible. There are new functionalities to force cluster instances to announce specific sets of IP address, client and bus ports, to the rest of the cluster, regardless of the auto detected IP. This required a bus protocol change and then the bad news is that it will force users to mass-restart all the nodes of a Redis 3.2 installation for upgrading to 4.0.


## The New Memory Command

Apart from active memory defragmentation to improve memory usage, there is addition of a new command for memory introspection.

The new MEMORY command gives you information into the memory consumption of a Redis instance.  The MEMORY USAGE command provides precise amount of memory being used by a given key, while `MEMORY DOCTOR` provides a framework (similar to the `LATENCY DOCTOR` command) for observing the overall memory consumption of an instance.

`LATENCY DOCTOR` was used to diagnose the complaints of slowness. Now we have a DOCTOR for the memory issues as well.

```
127.0.0.1:6379> MEMORY DOCTOR
```

MEMORY does a lot more than that.
```code
127.0.0.1:6379> MEMORY HELP
1) "MEMORY USAGE <key> [SAMPLES <count>] - Estimate memory usage of key"
2) "MEMORY STATS                         - Show memory usage details"
3) "MEMORY PURGE                         - Ask the allocator to release memory"
4) "MEMORY MALLOC-STATS                  - Show allocator internal stats"
```
The memory usage reporting of the `USAGE` subcommand is going to be very useful, and also the in depth information provided by `STATS`.


## Threaded Del Flush

Redis now supports threaded non blocking delete, a new command `UNLINK`, just deletes a key reference in the database, and does the actual clean up of the allocations in a different thread, so if you use `UNLINK` instead of `DEL` against a heavily populated key the server will not block.

But that's not all, with the `ASYNC` options of `FLUSHALL` and `FLUSHDB`, you can clean up all DBs or all the data inside an instance. Yet another new `SWAPDB` command, swaps two Redis databases content, it can be combined with `FLUSHDB ASYNC`. E.g., once you populated `DB 1` with the new version of the data, you can `SWAPDB 0 1` and `FLUSHDB ASYNC` the database with the old data, and create yet a newer version and reiterate. This is only possible now because flushing a whole DB is no longer blocking.

------
