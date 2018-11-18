---
layout: article
title: Designs - Site Reliability Engineering
key: d07-site-reliability-engineering
categories: Designs
tags: SRE
---

## What is SRE?

In general, an SRE team is responsible for the _availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning_ of their service(s).


Data is transferred to and from an RPC using protocol buffers,4 often abbreviated to “protobufs,” which are similar to Apache’s Thrift. Protocol buffers have many advantages over XML for serializing structured data: they are simpler to use, 3 to 10 times smaller, 20 to 100 times faster, and less ambiguous.

Extreme reliability comes at a cost: maximizing stability limits how fast new features can be developed and how quickly products can be delivered to users, and dramatically increases their cost, which in turn reduces the numbers of features a team can afford to offer. Further, users typically don’t notice the difference between high reliability and extreme reliability in a service, because the user experience is dominated by less reliable components like the cellular network or the device they are working with.



# Reference Resources
