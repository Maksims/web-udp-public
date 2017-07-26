# Web UDP

This repo is to **express public need** for potential **Web UDP** technology to enable server-client high-performance networking.  
And **shape the requirements** from existing needs and potential future appications.  
To motivate W3C Members and browser vendors to start discussion and propose spec as a solution.

PR's and discussions in [Issues](https://github.com/Maksims/web-udp-public/issues) are welcome.

## Table of Contents

- [Overview](#overview)
- [Problem](#problem)
- [Solution](#solution)
  - [Why not WebRTC](#why-not-webrtc)
- [Requirements](#requirements)
- [Public discussions and demand](#public-discussions-and-demand)
- [Potential first adapters](#potential-first-adapters)

## Overview

Web evolved over past years rapidly, improving networking capabilities using such technologies as: **WebSockets, WebRTC, HTTP/2, Server-Sent Events**.

Those technologies have own area of application and enabled web platform to have:
1. Voice and video communication between browsers (WebRTC)
2. Peer-to-peer data exchange (WebRTC)
3. Real-time server-client communication (WebSockets)
4. Faster website delivery (HTTP/2)
5. Efficient updating website content in real-time (Server-Sent Events)

*and many more rich and powerful experiences*

In recent years another medium have born: **HTML5 Games**. Adobe Flash is [going away](https://blogs.adobe.com/conversations/2017/07/adobe-flash-update.html), and HTML5 games industry is steadily growing.

Another new trend has born: IO Games - one of the most accessible multiplayer games out there. Some of popular examples: [agar.io](http://agar.io/), [slither.io](http://slither.io/) and [many more](http://iogames.space/). They all rely on currently the only available technology for real-time server-client communication - WebSockets.

But this limits network capabilities as **TCP for games has limits**.  
It's worth mentioning that it's **not only about the games** - there are plenty use cases for Web UDP.

## Problem

**Latency** and **congestion**. TCP has reliabile and ordered packet delivery and is connetion based protocol.

**Reliability** - this is not always desired by application logic, outdated information might not be relevant anymore and can be ignored. TCP will ensure redelivery of packets, which leads to congestion of packets delivery leading to latency spikes. UDP does not guarantees reliability out of the box, thus avoids congestion problem which can be used as benefits to ensure data is delivered ASAP.

**Ordered Delivery** - TCP uses sequencing number and acknowledgments to guarantee ordered *read* of packets, and if any packets are not delivered through sequence it will redeliver them. This leads to blocking reads, which leads to latency spikes and gets worse if network environment has higher packet loss.

**Connection** - is actually a good element for most developers, especially from security and web platform point of view.

## Solution

Protocol that does not guarantees reliability and ordered delivery out of the box, allowing developer to implement any of the techniques on top of it if required. This provides stable devlivery timing and low latency out of the box.

## Why not WebRTC

WebRTC is extremely complex and is made for peer-to-peer type communication.

## Requirements

1. **Security** - it has to benefit from SSL. Probing local ports shouldn't be a possibility.
2. **Connection based** - to prevent UDP probing as well as make it simpler to use.
3. **Server-client** - p2p is already solved by WebRTC, and due to nature of security p2p is not reliable for applications where decisions can't be trusted to clients. This affects monetisation and application logic, where decisions should be made by authoritative server and not cients.
4. **Simple to use** - WebSockets success is very much because of it's simplicity to implement server-side protocol, data framing and how simple it is to use in browser.
5. **Minimum header overhead** - to minimize traffic.
6. **Minimum opinion or tech requirements** - WebRTC suffers from complexity and requirements when WebSockets are minimal in that terms enabling it's adaption by wide variety of platforms and web developers.

## Public discussions and demand

- [Why can't I send UDP packets from a browser?](https://new.gafferongames.com/post/why_cant_i_send_udp_packets_from_a_browser/) - detailed analysis why on UDP in browsers and proposed alternative solution.
- [Hacker News: WebRTC: the future of web games (getkey.eu)](https://news.ycombinator.com/item?id=13264952) - discussion why WebRTC is not suitable alternative.
- [A comprehensive dive into WebRTC for client-server web games](http://blog.brkho.com/2017/03/15/dive-into-client-server-web-games-webrtc/) - from article it is apparent how complex WebRTC is, and how hard to implement. Due it's complexity [it is slow on adoption](http://caniuse.com/#feat=rtcpeerconnection) by major browser vendors. But networking benefits of UDP over TCP is clear from article.

## Potential first adapters

- http://agar.io/
- http://slither.io/
- Real-time data visualization (where timing is critical)
- Various HTML5 multiplayer games
- *Please PR your case*
