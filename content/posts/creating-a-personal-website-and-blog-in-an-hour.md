---
title: "Creating a Personal Website and Blog in an Hour"
date: 2018-07-08T19:54:20+10:00
draft: false
---

Recently, the inimitable [Geoff Huntley](https://ghuntley.com/about/), a colleague of mine, gave an internal presentation talking about how he manages his online persona and brand. One thing I was inspired by in his talk was how deliberate Geoff is in this space, and the value he gets from his brand, and his website.

I asked Geoff what he used for his website - half expecting it to be some crazy Rube Goldberg like technological marvel.

> I use Hugo for the static site, as it has a lot of built in bits. And I use Netlify for hosting.

That sounded reasonably palatable. So come Sunday, and a small window of spare time, I went tech-shopping.

* First I went to [Hugo](https://gohugo.io/getting-started/installing/) to retrieve its latest release in binary form
* Next I added `hugo` to my `PATH` variable
* I then followed the [Hugo quick start](https://gohugo.io/getting-started/quick-start/)

Now I had a functional blog platform running locally. A little bit of theme shopping (hot tip: don't use submodules as suggested for themes, they cause Netlify some angst), and a self-portrait and favicon later, I had something I could push out as the skeleton of my website.

* Next, I went and created [a new repository on my GitHub](https://github.com/andrewabest/andrewbestdotcom) to house my new blog
* `git init`, `git commit`, `git remote add origin`, `git push`

My website was now housed somewhere where I, and others, could contribute to it.

* I then went to [Netlify](https://www.netlify.com/) and signed up using my GitHub account
* I clicked the large, prominent `New site from Git` button
* Selected my [repository](https://github.com/andrewabest/andrewbestdotcom) from GitHub, which required no further authentication thanks to me having signed up with my GitHub account
* Set up Continuous Deployment of the site - Netlify correctly identified it was a hugo repository and added the correct settings for this, so there was nothing for me to actually do to enable this
* Enforced HTTPS for the site by clicking a button, which Netlify does via [LetsEncrypt](https://letsencrypt.org/)
* Set up custom DNS

Upon setting up DNS I paused and purchased [a domain name](https://www.andrew-best.com) via my account on [DNSimple](https://dnsimple.com). Netlify helpfully supplies you CNAME and ALIAS instructions for primary `www` and secondary `bare` urls, so without much thought I was able to plug those into DNSimple's domain management.

**Total time** was little more than an hour. It would have been less if I didn't have to stuff around with submodules.

**Total cost** was $14 for the domain name. _That is all_.

And it all. Just. Worked.

Welcome!