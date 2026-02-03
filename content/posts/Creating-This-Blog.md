+++
date = '2026-02-01T22:25:07-05:00'
draft = false
tags = ["meta"]
title = 'Creating This Blog'
+++
## Why Hugo?
Hugo takes a markdown-first approach. For the inverse reason, I *fled* WordPress. As an avid Obsidian user, I'm quite attached to Markdown. Markdown drives a lot of static content that we engage with online. It was created to be easy to use and facilitates a more natural writing flow than an HTML-first approach.

There are a few solid choices for markdown-first blogging platforms. Hugo blogs and sites deploy as a single binary, whereas platforms like Pelican and Jekyll require Python and Ruby environments, respectively. I happen to prefer that single-binary deployment. I also wanted to engage with Golang's template language. Other than that, there isn't a serious reason I chose Hugo over other options.

## Choosing a Theme
I created a few themes and found myself wasting a lot of time nitpicking the aesthetics. I wanted to blog to write about my projects. I didn't want my blog's style to be *the* project. So, I decided to consider third-party themes created by the Hugo community.

There are reasonable concerns when it comes to using someone else's theme, the same as with using any third-party code.

### Security issues
Themes should be treated as "untrusted" code with the potential to piggyback malicious code. To provide a brief, real-world example, **CVE-2025-48384** describes a vulnerability that allows a malicious repo to perform arbitrary file write and RCE when it is cloned with the `--recursive` flag. The vulnerability exists in the way git parses carriage returns in config values. The recommendations are to read the `.gitmodule` file of any untrusted repo, clone normally as opposed to recursively, and update Git to a patched version.

I'm not worried about this particular CVE, and I don't don my tinfoil hat at all hours, but I'm still going to read the code of any theme I import.

### Simplicity
The modern internet experience is weighted by burdens of script that once-upon-a-time existed as an enhancement. JavaScript was supposed to make sites feel responsive and smooth, but now it's usually working hardest at tasks that don't benefit the user and detract from the experience. It's making our browsers *sweat* with effort to support ads, tracking, and analytics.

While my own themes used some light JavaScript, and I'm not closed to adding some in the future, I'd just rather not start off with loads of someone else's JavaScript. It's not that I set out for a "Zero JS" approach, but the movement to avoid JavaScript for things CSS can do was looking more reasonable the more I perused other people's JavaScript. 

### Privacy
Some themes use fonts hosted by Google, but it's possible to self-host these fonts and there is a compelling reason to do so. Essentially, since Google-hosted fonts send some user information to a third party (Google) without consent *and* without functional necessity, they run afoul of GDPR and other privacy regulations. 

In addition to Google fonts, other themes have support for Analytics baked in.

### Shibui concerns
This theme is called Shibui, and it considers itself a "Zero JS", minimalist theme. *Despite* calling itself a "Zero JS" theme, Shibui is actually more of a "Minimal JS" theme because it supports Mermaid diagrams. I feel alright leaving the Mermaid support as-is because it is a useful, well-known, open-source project with a paper trail. The people behind it have public faces and names. They have proven themselves responsive to security issues that affect their project.

With GDPR having such a strict ruling for Google-hosted fonts, Shibui's choice to include Mermaid with jsdelivr had me doing a little bit of research to discern if this would be a compliance issue. Jsdelivr is on top of this. To keep it short, since the service performed by jsdelivr's CDN significantly enables the functional quality of websites (not just the aesthetic quality), it enjoys protection under Article 6 of GDPR. Jsdelivr's full article is here: https://www.jsdelivr.com/blog/how-the-german-courts-ruling-on-google-fonts-affects-jsdelivr-and-why-it-is-safe-to-use/

## What's next?
A few features I am currently considering adding:
- ~~Light/dark mode with pure CSS, maybe.~~ Completed!
- A "Tags" page
- A Search feature (would require JavaScript)





