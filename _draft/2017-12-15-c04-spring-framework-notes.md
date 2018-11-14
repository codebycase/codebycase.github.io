---
layout: article
title: Spring Framework Notes
key: c04-spring-framework-notes
categories: Collections
tags: Spring
---

# HashiCorp Consul

- Consul provides first-class support for service discovery, health checking, K/V storage, and multiple data centers. By using client notes, Consul provides a simple API that only requires think clients, or the API can be avoided entirely by using configuration files and the DNS interface.
- Consul is an agent-based tool, which means it should be installed in each and every node of the cluster with servers and a client agent nodes. To install consul to all the nodes,

### Server Config Sample

/etc/consul.d/server/config.json

```json
{
    "bootstrap": false,
    "server": true,
    "datacenter": "nyc2",
    "data_dir": "/var/consul",
    "encrypt": "X4SYOinf2pTAcAHRhpj7dA==",
    "log_level": "INFO",
    "enable_syslog": true,
    "start_join": ["192.0.2.2", "192.0.2.3"]
}
```

Upstart Script within /etc/init/consul.conf

```shell
description "Consul server process"

start on (local-filesystems and net-device-up IFACE=eth0)
stop on runlevel [!12345]

respawn

setuid consul
setgid consul

exec consul agent -config-dir /etc/consul.d/server
```

### Client Config Sample

/etc/consul.d/client/config.json

```json
{
    "server": false,
    "datacenter": "nyc2",
    "data_dir": "/var/consul",
    "ui_dir": "/home/consul/dist",
    "encrypt": "X4SYOinf2pTAcAHRhpj7dA==",
    "log_level": "INFO",
    "enable_syslog": true,
    "start_join": ["192.0.2.1", "192.0.2.2", "192.0.2.3"]
}
```

Upstart Script within /etc/init/consul.conf

```shell
description "Consul client process"

start on (local-filesystems and net-device-up IFACE=eth0)
stop on runlevel [!12345]

respawn

setuid consul
setgid consul

exec consul agent -config-dir /etc/consul.d/client
```

### Connecting to the Web UI

http://192.0.2.50:8500

To get access to the web UI locally, we can create an SSH tunnel to the client machine that holds the UI files. Consul serves the HTTP interface on port 8500. We will tunnel our local port 8500 to the client machines' port 8500.

> ssh -N -f -L 8500:localhost:8500 root@192.0.2.50

### Secure with TLS Encryption

We will focus on creating a TLS certificate authority in order to sign certificates for each of our servers.


### TTL (Time To Live)

Time to live (TTL) or hop limit is a mechanism that limits the lifespan or lifetime of data in a computer or network. TTL may be implemented as a counter or timestamp attached to or embedded in the data. Once the prescribed event count or timespan has elapsed, data is discarded.

# Spring Cloud Consul
