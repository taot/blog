---
title: Proxy with Tinc and Squid
tags:
  - linux
---

### Overview

For a reason that-must-not-be-said, one has to setup a proxy server on a VPS, and access the proxy server from a VPN. This artical describes the steps to setup Tinc for VPN and Squid for proxy.

<!--more-->

### Installing Tinc

Before installing and configuring Tinc, one should read the [Tinc document](http://www.tinc-vpn.org/docs/){:target="_blank"} to at least get a rough idea of how Tinc works.

The installation should be very straight forward. You can either follow the Tinc document, or use your Linux's package manager to install Tinc. In my case, I use AUR to install a pre-release version (I use Arch Linux).

### Configuring Tinc

Create initial configuration file and add subnet on each of your instance.

```bash
# Generate a directory named *netname* inside /etc/tinc/ with keys and other files.
# name is the tinc instance's name and it should be unique in your whole setup
tinc -n netname init name

# Add subnet
sudo tinc -n test add subnet 10.20.1.0/24
```

Add *Address* for the instances which you want to be connected from other instances.

```bash
# Replace the host name with the real IP or the hostname of your machine
tinc -n netname add address foo.example.org
```

Add *ConnectTo* for the instances that you want to connect to others.

```bash
# Replace bar with the name of the instance to be connected
tinc -n netname add connectto bar
```

### Exchanging Keys

You need to exchange the keys for all instances

```bash
tinc -n netname export
tinc -n netname import

# Or use tinc exchange
tinc -n netname export \
  | ssh bar.example.org tinc -n netname exchange \
  | tinc -n netname import
```

### Configuring Network Interfaces

Before you can start transmitting data through tunnel, you need to configure the network interfaces. This is usually done in tinc-up.

```bash
tinc -n netname edit tinc-up
```

A sample tinc-up file would look like:

```bash
#!/bin/sh
ifconfig $INTERFACE 10.20.1.2 netmask 255.255.255.0
```

### Swith Mode

In my case, I need to use *switch* mode in order for proxy to work. I don't know why.

```bash
# Add the following line to tinc.conf
Mode = switch
```

### Starting Tinc

Start and stop Tinc with the following commands. You can specify log file and debug level.

```bash
# Start with log file and debug level
tinc -n test start --logfile=/var/log/tinc.test.log --debug=5

# Stop
tinc -n test stop
```

### Squid

Install Squid with your package manager, and start it. The default configuration should work fine, and the default http port is 3128.
