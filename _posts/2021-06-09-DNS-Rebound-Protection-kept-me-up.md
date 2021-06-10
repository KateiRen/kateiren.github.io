---
layout: post
title:  "How DNS Rebound Protection gets into your way for local HTTPS secured services"
author: karsten
categories: [ tutorial, raspi, docker ]
image: assets/images/photo-1605745341112-85968b19335b.jpg
beforetoc: "Photo by Ian Taylor on Unsplash"
---
## What this is about
For some while I was looking for a convinient solution to leave behind *1Password* and *KeePassXC* as my central password managers.
The soon to be star on the horizon was *bitwarden_rs* selfhosting option, now renamed to *vaultwarden*.

The goal - a convienient and secure password vault without any connectivity from outside while retaining automatic synchronization with all devices setup.

## What is the issue here
Modern browsers disallow using Web Crypto APIs in insecure contexts - that means without HTTPS.

That's why all tutorials to setup a selfhosted *vaultwarden* service include documentation to spin up another docker instance proving a reverse proxy to establish the HTTPS connection.

To get HTTPS working we are lacking two things right now 
- a valid domain name and 
- the certificates required.

Tutorials introduced me to [DuckDNS](https://www.duckdns.org/domains) an easy and free to use service to register subdomains to [duckdns.org](https://www.duckdns.org/domains).

The easiest way to obtain certificates nowadays is [Let's Encrypt](https://letsencrypt.org/).

**Going the private network way introduces two obstacles, one documented in all tutorials, the other not.**

The first obstacle is, that given the fact that no traffic from outside should get through to the local service, we will not be able to use HTTP validation (putting a specific file on our webspace to prove we own the domain).

That is documented and workarounds using DNS challenge (adding a specific TXT record in the DNS zone of the domain to prove it's ours) are introduced.

However, here the touble started and I lost several evenings trying to fix it.

None of the options (caddy with DNS auth module, nginx with separately fetched certificates, not recommended rocket option) did work and created strange results.

Trying to ping, nslookup, traceroute my newly created subdomain from inside and outside my network produced varying results and I thought I found my enemy in [pi-hole](https://pi-hole.net/). So I spent several evenings trying to tweek [pi-hole](https://pi-hole.net/)'s settings, whitelist, update the engine, try other docker parameters, disabling [pi-hole](https://pi-hole.net/) alltogether...

After flushing all DNS chaches and making sure that the upstream DNS server of my [FRITZ!Box](https://avm.de/produkte/fritzbox/) was being used and still - not able to resolve the domain name!

While trying again to change my [FRITZ!Box](https://avm.de/produkte/fritzbox/) DNS settings I stumbled upon a feature I hadn't heard about yet: DNS Rebound Protection!

*It's not a bug, it's a feature!*

To prevent [DNS Rebind attacks](https://www.ceilers-news.de/serendipity/37-DNS-Rebinding-Ein-altbekannter-Angriff-kompromittiert-Router.html), the router does not allow resolving domains to local IP addresses!

To get going, we just need to enter our domain including subdomain as an exception and everything works like magic!

Wow! Easy as that! As this is a security feature, other routers should show the same behaviour. So why did none of the tutoprials mention this?

**Anyway, at least now that's resolved for me.**
