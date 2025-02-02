+++
title = "dcache"
description = "*dcache* - enables a networked async cache using Redis Pub/Sub."
weight = 10
tags = [  "plugin" , "dcache" ]
categories = [ "plugin", "external" ]
date = "2022-01-01T20:40:00+09:00"
repo = "https://github.com/bootjp/dcache"
home = "https://github.com/bootjp/dcache/blob/main/README.md"
+++

## Description

Using dcache, you can use Redis Pub/Sub to asynchronously share name resolution resolved by other nodes.
This means that DNS queries do not use unnecessary communication to retrieve the cache, and it operates with very low latency.
It can be used in conjunction with the [CoreDNS standard cache plug-in](https://coredns.io/plugins/cache/).
The TTL of the cache is the smallest value in the response.

If this plugin is enabled and you cannot connect to Redis, it does nothing and does not interfere with CoreDNS operation.

### What is the difference from redisc?
[redisc](https://github.com/miekg/redis) takes the data to Redis after a DNS query,
but dcache uses Pub/Sub to keep the cache data in memory asynchronously.

dcache provides better latency than redisc in environments with very large DNS queries such as crawling.

## Syntax

~~~
dcache [RedisHost]:[Port]
~~~

## Example

all zone enable dcache.

```Corefile
. {
    forward . 1.1.1.1
    cache
    dcache example.org:6379
}
```

## Metrics

If monitoring is enabled (via the *prometheus* directive) then the following metrics are exported:

* `coredns_dcache_hits_total{server}` - Counter of cache hits.
* `coredns_dcache_misses_total{server}` - Counter of cache misses.
* `coredns_dcache_redis_errors_total{server}` - Counter of errors when connecting to Redis.
* `coredns_dcache_discard_cache_total{server}` - Counter of data that failed deserialization.