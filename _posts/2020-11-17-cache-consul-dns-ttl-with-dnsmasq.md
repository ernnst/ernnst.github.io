---
layout: post
title: "Cache Consul DNS TTL With Dnsmasq"
date: 2020-11-17
tags: hashicorp consul dnsmasq dns
comments: true
twitter: true
---
While testing [Consul DNS caching](https://learn.hashicorp.com/tutorials/consul/dns-caching),
I have noticed that Dnsmasq does not cache Consul responses. If you are also using
[Dnsmasq to forward DNS](https://learn.hashicorp.com/tutorials/consul/dns-forwarding#dnsmasq-setup)
queries to Consul, you may run into the same issue.
This guide will walk though all required steps to enable Consul DNS TTL cache with Dnsmasq.

## TL;DR
**Problem**: Dnsmasq won't cache responses from non recursive name servers.
Consul refuses recursive queries without recursor configuration.

**Solution**: Configure fake [recursor](https://www.consul.io/docs/agent/options.html#recursors)
in Consul by adding `"recursors": [ "127.0.0.2" ]` to `/etc/consul/consul.json`.

## Detailed

### Enable Consul DNS Caching

Edit `/etc/consul/consul.json` and add the config below.
It should enable TTL values of 1 minute for all services and nodes, 5 minutes for test service.
You can replace it with any other service you prefer.
```json
{
  "dns_config": {
    "service_ttl": {
      "*": "60s",
      "test": "5m"
    },
    "node_ttl": "60s"
  }
}
```

Reload Consul with `consul reload`.\

Verify Consul returns DNS response with TTL value. Note the Consul DNS port `8600`.
```bash
$ dig +nocmd +noall +answer test.service.consul @127.0.0.1 -p 8600
test.service.consul.	300	IN	A	10.135.205.232
test.service.consul.	300	IN	A	10.135.174.59
```

We can see that Consul DNS cache is working, we have `300` seconds TTL in the second column for
test service.\
Now that Consul is responding with TTL values, we need to cache them on client level.
That's where Dnsmasq comes in.

### Enable Dnsmasq Caching

We use Dnsmasq for forwarding client DNS requests to Consul.
It also forwards non Consul requests to other DNS servers. We need to enable caching for Dnsmasq,
in order to cache Consul replies with TTL.

Edit Dnsmasq config file `/etc/dnsmasq.conf` and add/uncomment `cache-size` parameter
(change `150` to the size you need, but for this example it's enough):

```bash
cache-size=150
```

Also, enable logging so we can check which records Dnsmasq cached:

```bash
log-facility=/var/log/dnsmasq.log
log-queries
```

Restart Dnsmasq service:

```bash
$ systemctl restart dnsmasq
```

Follow Dnsmasq logs:

```bash
$ tail -f /var/log/dnsmasq.log | grep cached
```

Verify that Dnsmasq caches requested domains. Note the Dnsmasq DNS port `53`.\
First response should contain the IP address and TTL value in the second column.
```bash
$ dig +nocmd +noall +answer google.com @127.0.0.1 -p 53
google.com.		118	IN	A	216.58.206.78
```

Second response should show lower TTL if the cache is working:
```bash
$ dig +nocmd +noall +answer google.com @127.0.0.1 -p 53
google.com.		114	IN	A	216.58.206.78
```

After the second command, you should also see in Dnsmasq logs that cached response was returned
for `google.com`:

```bash
Nov 16 20:49:04 dnsmasq[22925]: cached google.com is 216.58.206.78
```

### Problem

Dnsmasq doesn't cache Consul domain records. No matter how many times I lookup the address,
TTL doesn't seem to change. Dnsmasq logs are also not showing any cached responses for
`.consul` domain.

After some head banging I found out the answer
[here](https://discuss.hashicorp.com/t/dnsmasq-problem-caching-responses-from-consul/14673).
Turns out Dnsmasq won’t cache responses from non recursive name servers.
Consul refuses recursive queries without recursor configuration.
From Dnsmasq [man page](http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html):

> Dnsmasq accepts DNS queries and either answers them from a small, local,
  cache or forwards them to a real, recursive, DNS server.

Here is the [source code](https://github.com/imp/dnsmasq/blob/4e7694d7107d2299f4aaededf8917fceb5dfb924/src/rfc1035.c#L847-L857)
for caching in Dnsmasq:
```c
/* Don't put stuff from a truncated packet into the cache.
   Don't cache replies from non-recursive nameservers, since we may get a
   reply containing a CNAME but not its target, even though the target
   does exist. */
if (!(header->hb3 & HB3_TC) &&
    !(header->hb4 & HB4_CD) &&
    (header->hb4 & HB4_RA) &&
    !no_cache_dnssec)
  cache_end_insert();

return 0;
```

### Solution

Configure dummy [recursor](https://www.consul.io/docs/agent/options.html#recursors) in Consul.\
Edit `/etc/consul/consul.json` and add `recursors` entry with dead address (in your environment):

```json
{
  "recursors": [ "127.0.0.2" ]
}
```

After `consul reload` Dnsmasq should cache responses from `.consul` domain.

First query:
```bash
$ dig +nocmd +noall +answer test.service.consul @127.0.0.1 -p 53
test.service.consul.	300	IN	A	10.135.205.232
test.service.consul.	300	IN	A	10.135.174.59
```

Second query:
```bash
$ dig +nocmd +noall +answer test.service.consul @127.0.0.1 -p 53
test.service.consul.	297	IN	A	10.135.174.59
test.service.consul.	297	IN	A	10.135.205.232
```

Dnsmasq log:
```bash
Nov 16 20:49:04 dnsmasq[22925]: cached google.com is 216.58.206.78
Nov 16 21:53:49 dnsmasq[22925]: cached test.service.consul is 10.135.174.59
Nov 16 21:53:49 dnsmasq[22925]: cached test.service.consul is 10.135.205.232
```
---
**Note**

If any of your servers will send a request to Consul DNS server for non `.consul` domain, Consul will try to forward it to non existing recursor. By default, it will time out in 2s, but you can adjust that setting with `recursor_timeout` option and set to lower value.

```json
{
  "dns_config": {
    "recursor_timeout": "1ms"
  }
}
```
