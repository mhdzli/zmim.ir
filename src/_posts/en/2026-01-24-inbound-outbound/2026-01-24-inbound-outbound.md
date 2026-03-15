---
title: Why Do Messages from Inside Iran Reach Outside More Easily?
date: 2026-01-24 13:10:00 +04:30
tags: [network, internet, freedom]
description: During internet outages or severe restrictions in Iran, many people experience that messages sent from inside the country usually reach outside more easily, while messages sent from outside to inside either arrive late or not at all. This behavior is not accidental or random — it is rooted in the design of the network, the type of filtering, and differences in internet protocols.
image:
toc: true
mastodon:
  host: mas.to
  username: mz
  id: 115950118322464173
lang: en
---

Unfortunately, Iran's internet connection has been cut for about two weeks and distressing news has left all of us in a bad state. Free access to the internet is a fundamental and basic right for all people. Here's hoping for better days.

Here I want to explain why, after a brief and limited restoration of connectivity, messages sent from inside Iran are more easily received outside the country, while the reverse path — messages sent from outside Iran — typically have more difficulty reaching their destination.

# Asymmetric Routing

During a crisis or period of restrictions, internet routes typically become asymmetric. This means the outbound path from Iran (sending data to the outside) is less restricted, while the inbound path to Iran (receiving data from outside) is more heavily filtered or blocked. The reason is that controlling incoming information is more important to surveillance systems.

Simply put, "sending" becomes easier than "receiving." The result of this asymmetry is that a request leaves from inside the country, but the response from outside may never return.

Internet routes become asymmetric (Asymmetric Routing) during outages. The outbound path from Iran is usually more open while the inbound path is more restricted. On the other hand, VPNs and proxies have a harder time re-establishing inbound connections on an unstable internet, especially if they use UDP or have weak keep-alive mechanisms. Also, DNS and external servers sometimes send responses via a route whose return path to Iran is blocked.

# Stateless Firewall

A stateless firewall checks each internet packet completely independently. It doesn't care what was exchanged before this packet. It only looks at whether this packet matches the defined rules.

## Advantages

Simplicity is the most important advantage of this type of firewall. It is easy to implement, consumes few resources, and has high processing speed. For this reason, it is suitable for simple routers or networks with very high traffic volumes.

## Disadvantages

The main problem with this firewall is that it has no "context." It cannot understand what connection a packet is part of. For this reason, its rules must either be very permissive (which reduces security) or very strict (which causes legitimate connections to be dropped), and it is not well-suited for precise control of inbound traffic.

# Stateful Firewall

A stateful firewall, unlike a stateless one, has memory. When a connection is initiated from inside the network, the firewall records it in a table called the state table. After that, only packets that are a continuation of that recorded connection are allowed through.

If a packet arrives from outside for which no prior connection exists, the firewall treats it as suspicious and typically blocks it.

## Advantages

Higher security is the main advantage of a stateful firewall, because it only allows the continuation of connections that started "legitimately." Control of inbound traffic is much more precise and the risk of abuse is reduced.

## Disadvantages

This type of firewall is more complex and consumes more resources. Additionally, it is sensitive to network instability. If a connection is interrupted and reconnects, or a packet is lost, the state may be cleared and subsequent packets may be incorrectly blocked.

# Summary Comparison of Stateless and Stateful

In short, stateless is simple and fast but has no awareness of connections. Stateful is smarter and more secure but can drop legitimate connections on an unstable internet. Iran's internet restrictions more likely use stateful firewalls, because controlling inbound traffic is their priority.

In Iran's internet restrictions, stateful firewalls are probably used more — meaning if a connection is initiated from inside the country, the response from outside comes back more easily, but if someone tries to send a message from outside without a prior connection from inside, the firewall typically blocks it.

That is why messages sent from inside to outside have a better chance of arriving than messages sent from outside to inside.

# TCP and Its Key Messages

Most internet communications (web, messaging apps, email) run on a protocol called TCP. Before sending data, TCP has a specific process for establishing a connection.

First, a packet called SYN is sent, meaning "I want to establish a connection." The other party responds with SYN-ACK, meaning "I received your request and I'm ready." The sender then confirms with ACK and the connection is established. After that, the actual DATA is exchanged. Finally, one of the two parties announces with FIN that the connection is over and it is closed.

This defined structure allows stateful firewalls to determine which connections are legitimate.

<div style="text-align: center;">
    <img src="/inbound-outbound/udp-tcp.jpg" style="max-width: 80%; margin: 10px;" alt="Comparison of UDP and TCP">
</div>

# UDP and the Concept of Pseudo-State

UDP, unlike TCP, has none of these steps. It has no defined start, no acknowledgment, no end. Each UDP packet is sent independently, without the other party necessarily sending a response.

To avoid completely blocking UDP, stateful firewalls create something called a pseudo-state. This means if a UDP packet is sent from inside the network to a specific destination, the firewall temporarily allows a response from that same destination to return. If this time window expires or the internet is unstable, subsequent packets arriving from outside are no longer recognized as legitimate and are dropped.

For this reason, UDP-based connections — such as some VPNs, voice and video calls — become unstable very quickly when internet connectivity is intermittent.

# Summary

The fact that messages from inside Iran reach outside more easily but not vice versa is the result of a combination of factors: the asymmetric nature of internet routes, the widespread use of stateful firewalls, the sensitivity of these firewalls to network instability, and the inherent differences between TCP and UDP. This behavior is neither a bug nor accidental — it is the product of design and policy decisions at the network infrastructure level.
