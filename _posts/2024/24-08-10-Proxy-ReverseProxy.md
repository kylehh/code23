---
title: Proxy and Reverse Proxy
mathjax: true
toc: true
categories:
  - Study
tags:
  - Network
---

Every time I see the word "Proxy" I feel some kind of uneasy, not to mention how I feel when I see "Reverse Proxy". Now looked it up at this [intro](https://www.cloudflare.com/learning/cdn/glossary/reverse-proxy/)

## 1 Proxy 
A **forward proxy**, often called a proxy, is a server that sits in front of a group of *client* machines. The proxy server act like a middleman to commute with web servers on behalf of those clients.
![Alt text](/code23/assets/images/2024/24-08-10-Proxy-ReverseProxy_files/proxy.png)

## 2 Reverse-Proxy
A reverse proxy is a **server** that sits in front of one or more *web servers*, intercepting requests from clients. 

![Alt text](/code23/assets/images/2024/24-08-10-Proxy-ReverseProxy_files/reverseproxy.png)

## 3 Compare
A forward proxy sits in front of a client and ensures that no origin server ever communicates directly with that specific client. 

A reverse proxy sits in front of an origin server and ensures that no client ever communicates directly with that origin server.