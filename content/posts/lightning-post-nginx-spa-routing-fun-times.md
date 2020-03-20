---
title: "Lightning Post: Nginx + SPA Routing Fun Times"
date: 2020-03-20T12:00:00+10:00
draft: false
---

This is a **Lightning Post** - something that I thought I'd try, a shorter form post on interesting problems or topics I come across. Credit to [Simon Cook](https://twitter.com/encodetalent) for dropping that term on the [Heaps Good Dev Slack](https://twitter.com/heapsgooddev) - hopefully by crediting him I won't owe royalties.

---

I have a pet project which comprises of a few components - a front end SPA, a back end API, and some worker processes. I've been packaging it all up in a few ways to test different development experiences outside of just "F5 the world" - docker, docker-compose, and Minikube, and how they then translate to automation come deployment time.

When containerising the SPA I reached for the official [Nginx Docker image](https://hub.docker.com/_/nginx). 

I knew I would need to configure Nginx to route all requests to my SPA - like most single page apps, mine uses a framework that relies on the [Html5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) for routing, removing the need for old-style hash-based routing. 

To accomplish this, your web server needs to pass all requests to your root page - usually `index.html`. This lets your framework of choice take in the route and construct your application appropriately based on its route definitions.

Given this is a _lightining post_, I won't go beleaguer you with too many tales of the yak-shaving I went through to arrive at a working configuration. I was going slowly crazy being continuously confronted with a 404 on all subroutes, regardless of what I was attempting to change in the configuration. 

What proved to be the turning point was when I went spelunking within the container with 

```cmd
docker run -it xxx /bin/bash.
```

The docker container contains a couple of configuration files. I had inadvertantly assumed there was only one. It contains _most_ of its configuration in `/etc/nginx/nginx.conf`, but also contains its `server` node configuration in `/etc/nginx/conf.d/default.conf`.

```bash
apt-get update && apt-get install nano
```

A quick `apt-get update` and `apt-get install nano` so that I can work in text files without questioning my sanity, and I was ready to dig into the root cause. 

What had happened to me is that I was replacing the parent `nginx.conf` with one that had appropriate routing rules for my SPA. These routing rules were then handily overwritten by those defined in the child configuration held in `default.conf`.

How does that work? Nginx supports an `include` directive in its configuration files. This allows you to compose configuration files like so:

```nginx
include conf.d/*.conf;
```

What this does is take all of the text in the referenced files, and pull it into the location you called `include`. This allows you to import at arbitrary levels of nesting. Nginx calls these [Feature-Specific Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/#feature-specific-configuration-files).

To verify the fix, I quickly updated the configuration in Nano and used an [Nginx Signal](https://docs.nginx.com/nginx/admin-guide/basic-functionality/runtime-control/) to reload the configuration.

```bash
nginx -s reload
```

And as if by magic, my application routing was working.

Postface
===

After the journey concluded, I encapsulated all of my nginx `server` config which defines the routing behaviour for my SPA in a single `server.conf` which I `include` into the main default config, which I leave as-is within the container. 

The real learning here though, and one that I've probably learned enough times that I should know better, is to [RTFM](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files) before playing with shiny new toys ⚽💎🔪.