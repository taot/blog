---
layout: post
title: Some Thoughs on IAAS and PAAS
---
Recently, I am looking for the techniques for the company's future
PAAS platform, I've been focused on WS2, but also browsed some other
products.

Here's some of my thoughts of IAAS, PAAS and WSO2.

<!--more-->

### IAAS vs PAAS

IAAS (Infrastructure As A Service) provides IT infrastructures to
those who need them. IT infrastructures mainly include hosts (virtual
machines), network, operating system, disk storage, etc. It might also
provide some facilities for deploying and managing software
installation and lifecycle. For example, OpenStack provide the core
functionality of creatings, managing and migrating virtual machines,
and some other functionalities such as cloud storage, virtual LAN,
through plugins. (The functionalities of OpenStack are heard from a
colleage, haven't confirmed them myself, but you get the idea). To
manage the softwares, you can use managing tools like Chef or Puppet
etc.

As for PAAS (Platform As A Service), I'm not quite sure where it
stands, and whether we need it or not. PAAS usually provide an SDK, on
which user can develop applications, like Google App Engine. Maybe one
of the important feature is multi-tenancy, or whatever you call it,
the idea is to be able to run many applications from many users on one
virtual machine.

Here's an article from web
http://www.it.com.cn/server/news/2010/07/12/08/840403.html, comparing
PAAS and IAAS from seven aspects.

So which does our company need? In short term, I think IAAS is good
enough. Low learning curve, familiar development model and development
environment, mature techniques from web industry, so IAAS is a
suitable choice for our current situation. In the long run, as our
business goes to PAAS phase, we will need a PAAS platform.

### What do we need from a PAAS platform?

#### Multi-tenancy

By definition, multi-tenancy means a single instance of software runs
on a server, serving multiple client organizations (tenants). It's
contrasted with multi-instance architecture. The good thing about
multi-tenancy is the hardware utilization rate would be much
higher.

I don't think we need multi-tenancy on PAAS level currently. I'm not
saying we don't need it, but not on the PAAS level, and right now. Our
applications should know the multi-tenancy by itself, even if PAAS
supports multi-tenancy, our apps still would have to know about the
tenants.

#### API Management

As is multi-tenancy, API management is not urgent either. We provide
SAAS first, and later we can move to PAAS. On the way to PAAS, we will
make mistakes, gain experience, and find other better solutions. And
maybe the private PAAS platform will evolve to better meet our
requirements.

#### Auto Scaling

This capability is definitely required. We need to be able to
automatically scale out when the load is high. And the scale-out
should be able to happend on both virtual machine level, and on
application level, which gives us a challenge.

#### Pricing

Not urgent for now, but needed later as explained above. Xu Chen's
idea is that we don't even need pricing functionality from PAAS
platform. In his mind, we can use logs as a way to calculate the
price. While I am not sure about that.

#### Monitoring

We need this. We need the ability to see the CPU usage, memory usage,
and disk usage of the virtual machines (and the physical
machines). Well, those metrics should be provided by the IAAS
platform. PAAS platform should provide monitoring on application
level, such as the CPU usage, memory usage, and all kinds of stuff of
applications.

#### Other

There are other functionalities that PAAS platform could offer, and of
course there will be many requirements coming from our development.