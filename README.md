# WebSockets UDP (or alternative) Extension

This repo is to **express public need** for a technology to enable **server-client low-latency communication**, **without** mandatory reliability and/or ordered delivery mechanics of underlying transport protocol.

And **shape the requirements** from existing needs and potential future applications.
To motivate W3C Members, IETF Community and Browser Vendors to start discussion and propose RFC to fuel further development for a solution.

PR's and discussions in [Issues](https://github.com/Maksims/web-udp-public/issues) are welcome.
Spread the word, [RT's](https://twitter.com/mrmaxm/status/890256659607584768) are welcome.

## Table of Contents

- [Overview](#overview)
- [Problem](#problem)
- [Possible Solutions](#possible-solutions)
  - [1. WebSockets UDP (or alternative) Extension](#1-websockets-udp-or-alternative-extension)
  - [2. WebUDP (or alternative) - new simple API](#2-webudp-or-alternative---new-simple-api)
  - [3. WebRTC 2.0 - simplified](#3-webrtc-20---simplified)
- [Requirements](#requirements)
- [Security](#security)
- [Transport Protocols](#transport-protocols)
- [Public discussions and demand](#public-discussions-and-demand)
- [Potential first adapters](#potential-first-adapters)

## Overview

Web evolved over past years rapidly, improving networking capabilities using such technologies as: **WebSockets, WebRTC, HTTP/2, Server-Sent Events, QUIC**.

Those technologies have own area of application and enabled web platform to have:
1. Voice and video communication between browsers (WebRTC)
2. Peer-to-peer data exchange (WebRTC)
3. Real-time server-client communication (WebSockets)
4. Faster website delivery (HTTP/2)
5. Faster connection and transfer of data (QUIC)
6. Efficient updating website content in real-time (Server-Sent Events)

*and many more rich and powerful experiences*

In recent years another medium have born: **HTML5 Games**. Adobe Flash is [going away](https://blogs.adobe.com/conversations/2017/07/adobe-flash-update.html), and HTML5 games industry is steadily growing.

Which lead to birth of IO Games - one of the most accessible multiplayer games out there. Some of popular examples: [agar.io](http://agar.io/), [slither.io](http://slither.io/) and [many more](http://iogames.space/). They all rely on currently the only viable technology for real-time server-client communication - WebSockets. WebRTC for server-client is overly complex (read further).

But this limits network capabilities as **TCP for games has limits**.
It's worth mentioning that it's **not only about the games** - there are plenty use cases for low-latency unreliable and unordered server-client communications.

## Problem

**Latency** and **congestion**. TCP has reliable and ordered packet delivery and is connection based protocol.
The need of UDP (or alternative) over TCP is not under a question as it is widely discussed in many articles about benefits of UDP (or alternative) over TCP in different cases. Here we trying just to summarize it. Worth mentioning that UDP being the most popular, but not the only transport protocol option for this effort.

**Reliability** - this is not always desired by application logic, outdated information might not be relevant anymore and can be ignored. TCP will ensure delivery of packets, which leads to congestions that results in latency spikes due to need of acknowledgments and redelivery of packets. UDP (or alternative) does not guarantee reliability out of the box, thus avoids congestion problem which can be used as a benefit to ensure latest data is delivered ASAP.

**Ordered Delivery** - TCP uses sequencing and acknowledgments to guarantee ordered *read* and re-delivery of packets. This leads to blocking read, which leads to latency spikes and gets worse if network environment has higher packet loss.

## Possible Solutions

Protocol that does not guarantee reliability and ordered delivery out of the box, allowing developer to implement any of the techniques on top of it if required or use hybrid approaches. This provides stable delivery timings and low latency in less than perfect network environments. With congestion control to mitigate overwhelming the internet infrastructure to address security concern.

### 1. WebSockets UDP (or alternative) Extension

One of the option to solve this, is by adding new extension to existing WebSockets protocol using negotiation mechanics [described in RFC 6455](https://tools.ietf.org/html/rfc6455#section-9). Which would implement extra functionality to establish UDP (or alternative) protocol communication. This would benefit from existing origin-based security model of WebSockets and allow developers to follow progressive approach where they can fall-back to TCP logic if such extension is not supported by either side (server or client).

### 2. WebUDP (or alternative) - new simple API

Another approach would be by developing completely new API, very similar to WebSockets that would address unique features of UDP (or alternative) networking logic, as well as potentially provide extra features for developer with fine-grain options of how to send messages.

### 3. WebRTC 2.0 - simplified

Current state of WebRTC is overly complex and was designed primarily for peer-to-peer communication. Due to this is not well adopted for server-client communication and requires a lot of developer resources. Simply speaking, it is not solo-web-dev friendly like WebSockets are today. Potential option of extending the spec to modularize and simplify the requirements that would allow use of some parts of WebRTC making it more accessible for server-client scenarios and simplified API that would make it more accessible for web developers.

### 4. WebTransport

There's a [draft spec of the WebTransport API](https://wicg.github.io/web-transport/) that allows web applications to establish interactive, bidirectional, multiplexed network connections - with support for both reliable and unreliable communication.

## Requirements

1. [**Security**](#security)
2. **Origin-based security model** - to prevent UDP (or alternative) probing (port scanning) and suit server-client networking model.
3. **Server-client** - p2p is already solved by WebRTC, the goal of this effort is to find simple to adopt and use solution for server-client communication scenarios.
4. **Simple to use** - WebSockets success is very much because of its simplicity to implement on server-side and how easy it is to use in the browser.
5. **Minimum header overhead** - some of extra data framing over pure UDP (or alternative) will be potentially required but has to be kept at minimum.
6. **Minimum opinion or tech requirements** - WebRTC suffers from complexity and requirements when WebSockets are minimal by that enabling rapid adaption by wide variety of platforms and web developers.

## Security

Such API and underlying protocol implementation should address security concerns to make it web friendly:

1. **DDoS attacks** - not exposing raw protocol functionality will utilize security practices similar to WebSockets.
2. **Ports scanning** - using origin-based security model will require handshaking and validation of headers to prevent possibility to connect to any IPs and ports.
3. **Encryption** - utilize existing encryption mechanics of HTTP/HTTPS.
4. **Congestion control** - to avoid overflowing the internet infrastructure, the underlying transport protocol has to implement congestion control.

API should address those security concerns, without overcomplicating the API for front-end as well as implementation for the back-end.
That's why [UDPSocket](https://www.w3.org/TR/tcp-udp-sockets/) is not an option.

## Transport Protocols

List of potential underlying protocols that can be used for an implementation:

1. **DCCP** ([RFC-4340](https://tools.ietf.org/html/rfc4340)) - is connection based message protocol that implements feature negotiation and congestion control. It does not implement reliability or ordered delivery as part of a protocol leaving it to an application layer. This protocol also has DCCP-UDP ([RFC-6773](https://tools.ietf.org/html/rfc6773)) implementation path over UDP to utilize existing NAT with UDP capabilities before DCCP is natively supported by internet infrastructure.
2. **QUIC** ([IETF Draft](https://tools.ietf.org/html/draft-hamilton-early-deployment-quic-00)) - is fairly new connection-based transport layer protocol [implemented by Google](https://www.chromium.org/quic) and available already in Chrome. It is fast growing and evolving protocol which is implemented on top of UDP. It is designed to reduce connection and transport latency as well as bandwidth. Although it has reliability built in which might be undesirable for current effort.
3. **SCTP** ([RFC-4960](https://tools.ietf.org/html/rfc4960)) - is standardized message based protocol with optional reliability and ordered delivery built in, as well as congestion control. It is also used in WebRTC for DataChannel. Due to adoption by WebRTC it lead to better support by internet infrastructure and implementations on OS level.
4. **DTLS** ([RFC-6347](https://tools.ietf.org/html/rfc6347)) - is a layer that can be implemented on top of transport protocol to provide security to meet web requirements. It is also used by WebRTC implementation.

## Public discussions and demand

- [Hacker News](https://news.ycombinator.com/item?id=14859280) - discussion on this repository topic.
- [Why can't I send UDP packets from a browser?](https://new.gafferongames.com/post/why_cant_i_send_udp_packets_from_a_browser/) - detailed analysis why on UDP in browsers and proposed alternative solution: [netcode.io](http://netcode.io/). By Glenn Fiedler.
- [UDP vs TCP](https://gafferongames.com/post/udp_vs_tcp/) - Which protocol is best for games? By Glenn Fiedler
- [Hacker News: WebRTC: the future of web games (getkey.eu)](https://news.ycombinator.com/item?id=13264952) - discussion why WebRTC is not suitable alternative.
- [A comprehensive dive into WebRTC for client-server web games](http://blog.brkho.com/2017/03/15/dive-into-client-server-web-games-webrtc/) - from article it is apparent how complex WebRTC is, and how hard to implement. Due it's complexity [it is slow on adoption](http://caniuse.com/#feat=rtcpeerconnection) by major browser vendors. But networking benefits of UDP over TCP is clear from article.

## Potential first adapters

- http://agar.io/
- http://slither.io/
- Real-time data visualization (where timing is critical)
- Various HTML5 multiplayer games (e.g. http://iogames.space/)
- Facebook Instant Games
- *Please PR your case*
