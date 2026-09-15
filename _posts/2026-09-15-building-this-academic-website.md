---
title: 'How I Built This Academic Website with Jekyll, GitHub Pages and a Custom Domain'
date: 2026-09-15
permalink: /posts/2026/09/building-this-academic-website/
tags:
  - jekyll
  - github-pages
  - academic-website
  - dns
---

This site runs on [AcademicPages](https://github.com/academicpages/academicpages.github.io), a Jekyll template built for researchers, hosted for free on GitHub Pages and served from my own domain. Setting it up took an afternoon of work and one long wait for DNS to propagate. Since a lot of colleagues in astronomy ask me how to put together a personal academic page without paying for hosting or learning a CMS, I am writing down exactly what I did, including the parts that went wrong.

## Why this stack

An academic homepage has modest requirements: a publication list, a CV, talks, teaching, and somewhere to write. It needs to survive for years without maintenance, load quickly from anywhere, and cost nothing to keep online. A static site generator fits that shape well. Jekyll turns a folder of Markdown files into plain HTML, GitHub Pages builds and serves it automatically on every push, and the whole site lives in a Git repository, which means it is versioned, backed up, and easy to move somewhere else if GitHub ever stops being a good host. There is no database to migrate and no plugin to update.

AcademicPages goes one step further than a generic Jekyll theme. It ships with collections that match how academics actually organise their work — `_publications`, `_talks`, `_teaching`, `_portfolio` — so a new paper is a new Markdown file rather than a layout problem.

## Getting the site online

The starting point is forking the AcademicPages repository and renaming the fork to `username.github.io`. GitHub treats a repository with that exact name as a user site and publishes it at `https://username.github.io` automatically, with no configuration beyond choosing the branch under Settings → Pages. Mine deploys from `main` at the root of the repository.

Almost everything identifying the site lives in `_config.yml`: the site title, the author block that feeds the sidebar (name, position, institution, location, and every profile link), and the `url` field, which matters more than it looks — I will come back to it. The navigation bar is a separate file, `_data/navigation.yml`, where each entry is a title and a URL. Deleting an entry removes it from the header without deleting the page itself, which is convenient for pages you want to keep but not advertise. I removed the template's built-in Markdown guide page this way.

Content is Markdown with YAML front matter. A publication is a file in `_publications` with fields for the venue, date, citation and PDF link; a blog post is a file in `_posts` named `YYYY-MM-DD-slug.md`. The template ships with sample posts and sample publications, and clearing those out is the first real editing task — leaving "Blog Post number 1" online is the clearest sign of a site nobody has touched.

To preview changes before pushing, run the site locally:

```bash
bundle install
bundle exec jekyll serve --livereload
```

It builds into `_site` and serves at `http://localhost:4000`. GitHub runs the same build after every push, so if it works locally it will almost certainly work in production.

## Pointing a custom domain at GitHub Pages

I registered `mustafaturansaglam.com` through a Turkish registrar and wanted it to serve the GitHub Pages site directly, not through a redirect. This is where most of the time went, so here is the configuration that works.

For an apex domain (the bare `example.com`, with no subdomain), GitHub requires four A records pointing at their servers:

| Type | Name | Value |
|------|------|-------|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| CNAME | www | username.github.io |

All four A records are needed; they are separate load-balanced endpoints, not alternatives. The `www` entry is a CNAME rather than an A record, and it can point either at your GitHub Pages subdomain or at the apex domain — GitHub accepts both, and it exists so that visitors who type `www.` in front of the address are not met with an error.

On the GitHub side, the domain goes into Settings → Pages → Custom domain. Saving it writes a file called `CNAME` at the root of the repository containing the domain name. That file is part of the site: if a later commit deletes it, the custom domain silently detaches, so it should be left alone. GitHub then runs a DNS check, and once that passes, **Enforce HTTPS** becomes available. It issues a Let's Encrypt certificate automatically, which usually takes a few minutes after the DNS check succeeds.

### What went wrong

Three things, all of them mundane, and all of them worth knowing about because they produce error messages that point in the wrong direction.

**A typo in one IP address.** I entered `195.199.111.153` instead of `185.199.111.153`. Three records were correct and one was not, so the site sometimes loaded and sometimes did not, depending on which endpoint the resolver picked. Worth checking each digit rather than trusting a glance.

**Too many CNAME records.** My registrar had pre-populated CNAME entries for `mail` and `ftp` alongside the one I added. GitHub's DNS check rejected the configuration outright. Removing the records I was not using resolved it. A domain used only for a static site does not need mail or FTP entries.

**Impatience with propagation.** For several hours, `nslookup mustafaturansaglam.com 8.8.8.8` returned `SERVFAIL`, and GitHub reported the DNS check as unsuccessful. Nothing was wrong; the records simply had not propagated. DNS changes can take up to 48 hours, and a record's TTL — 14400 seconds, four hours, in my case — is a lower bound on how long stale answers keep circulating. The check I kept running was:

```bash
nslookup mustafaturansaglam.com 8.8.8.8
```

Once it returns the four GitHub IP addresses instead of `SERVFAIL`, the configuration is live and GitHub's check will pass on the next attempt. [whatsmydns.net](https://www.whatsmydns.net/) is useful for the same question from multiple locations at once, since propagation is uneven across resolvers.

### One subtle mistake worth avoiding

After the domain was working, the theme's JavaScript stopped running: the light/dark toggle did nothing and the mobile menu would not open. The cause was in `_config.yml`, where `url` was still set to the old `https://username.github.io`. Jekyll uses that value to build absolute paths for assets, so the browser was loading the site from one origin and its JavaScript module from another. Modules are subject to CORS, the request was blocked, and the console showed:

```
Access to script at 'https://username.github.io/assets/js/main.min.js'
from origin 'https://example.com' has been blocked by CORS policy
```

Updating `url` to the custom domain fixed it. If something on a Jekyll site breaks immediately after moving to a custom domain, that field is the first place to look — and the browser console will usually name the problem outright.

## What it costs

The domain is the only recurring expense, roughly the price of a coffee per month depending on the TLD. GitHub Pages is free for public repositories, includes the TLS certificate, and has been reliable enough that I have not thought about uptime since launch. For a personal academic site, that is a reasonable trade: a small annual fee for an address you own and can point anywhere later.

If you are building something similar and get stuck, the two places worth checking first are GitHub's own [custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) and your registrar's DNS panel — in my experience the problem is almost always a record that is subtly wrong rather than anything to do with Jekyll.
