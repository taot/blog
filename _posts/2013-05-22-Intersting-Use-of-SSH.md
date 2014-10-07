---
layout: post
title: Interesting Use of SSH
tags:
  - linux
---
After reading the "Tracing an SSH Request" part of GitHub's "How We
Made GitHub Fast" blog, I was wondering how this is done through ssh
(at least in an operational level).

<!--more-->

So I tried it out on my computer, read the ssh man page, and discover
some interesting use of ssh.

### Execute Commands Remotely

This looks pretty simple:

{% highlight bash %}
ssh $REMOTE_MACHINE ls -al
{% endhighlight %}

The above command will execute 'ls -al' on the remote machine, and ssh
will connect the STDIN and STDOUT of remote machine with local
ones. It's just an extension of how I use ssh normally.

### Virtual Private Network

Being a Chinese programmer, we alway think how we can fan-qiang. I was
thinking about using ssh as a kind of proxy or VPN, and today, I
finally made it work, and it is simpler than I though it would
be. It's really a simplified version of SSH-BASED VIRTUAL PRIVATE
NETWORKS section in ssh man page.

For this to work, you need to first edit /etc/ssh/sshd_config, and
enable PermitTunnel.

Then, on the client machine:

{% highlight bash %}
ssh -f -w 0:1 $REMOTE_MACHINE true
ifconfig tun0 10.1.1.1 10.1.1.2 netmask 255.255.255.252
{% endhighlight %}

On the remote machine:

{% highlight bash %}
ifconfig tun1 10.1.1.2 10.1.1.1 netmask 255.255.255.252
{% endhighlight %}

Then your client machine will be able to access the proxy server on
10.1.1.1. It worked but was very slow, so it's not suitable for
long-time use.

After finished using the VPN, you can remove the dummy interfaces by:

{% highlight bash %}
ifconfig tun0 down
{% endhighlight %}

### Reverse Remote Login to a Machine behind Firewall

I don't know the technical term of this one, but the idea is this:

A is on a computer, which is behind a Firewall (or in a DMZ), and B is
on another computer accessible from public network. Now B wants to ssh
onto A's computer, but the problem is A's computer is not accessible
from B's network.

I was told this can be done with ssh, but I haven't yet figured out
how.
