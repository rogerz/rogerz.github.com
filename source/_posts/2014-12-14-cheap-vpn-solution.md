---
layout: post
published: true
title: Cheap VPN solution
comments: true
categories: misc
---

I was using some VPN services to access the blocked sites in China before. It cost 180 CNY per year. It provide multiple lines and detail instructions for how to use it. Easy and stable for beginners. If you take my ref link http://refyunti.com/?r=0e6802542f99958e , you will get 10 CNY discount.

Recently I found another solution. It is not as easy as a ready-to-use VPN server, but much cheaper and not to difficult to setup

## A cheap VPS on bandwagon

```
Micro-64 
Self-managed
HDD: 1.5 GB
RAM: 64 MB
CPU: 1x Intel Xeon 
BW: 100 GB/mo
$3.99 USD Annually
```

It comes with a pre-installed OS and one-click install for OpenVPN. In spite of very limited RAM, it is good enough for a personal VPN server.

## A smart router supports SSH tunnel plugin

I bought a [HiWiFi](http://www.hiwifi.com) router and installed [SSH tunnel](https://app.hiwifi.com/store.php?m=plugins&a=info&rid=564371&sid=19). The good thing is that it handles routes smartly so that only the blocked sites are accessed through the tunnel. When you access sites hosted in China, it uses direct link.

To use the SSH tunnel, you may want to create a new user on the VPS

```shell
# useradd rogerz
# passwd rogerz
```

With such a smart router, all devices at home can access the internet smoothly. No additional setup required.

## OpenVPN Clients

When I am not at home, OpenVPN will be the solution. It is easy to install OpenVPN server on bandwagon VPS. Just go to the KiWiVM control panel and click the button. Everything will be ready for you.

### iOS devices

1. Install the [OpenVPN Connect](https://itunes.apple.com/us/app/openvpn-connect/id590379981?mt=8) app
2. Download the OpenVPN profile and related keys from KiWiVM control panel
3. Copy all the files to iPhone via iTunes