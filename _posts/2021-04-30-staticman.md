---
layout: post
title: "Static Comments for Jekyll with GitHub Pages"
date: 2021-04-30
tags: jekyll staticman github githubpages
comments: true
summary: "Staticman is an open source static comment solution for your Jekyll site"
---

Disqus is the most popular comment solution for Jekyll at the moment, but it has one huge problem. It sucks. It's free proprietary software filled with tracking scripts that slow down your website.

Since Jekyll sites are static there was no way to store comments without such external service or database. Well, at least until [Staticman](https://staticman.net/).

Staticman is an open source static comment solution for your Jekyll site. It allows you to have user-generated content on a static website.

I just have finished setting it up on this blog. I have used [this amazing guide](https://travisdowns.github.io/blog/2020/02/05/now-with-comments.html) by Travis Downs. I'm really happy how it works.
Here's a quick summary:

1. Staticman can be hosted on any platform with NodeJS. I am using free Heroku app, but there are many other options. More details [here](https://staticman.net/docs/index.html).
1. Jekyll theme will need a simple HTML form that will post comments to Staticman API. I have borrowed most of HTML/CSS/JS from the [guide](https://travisdowns.github.io/blog/2020/02/05/now-with-comments.html) I mentioned earlier.
1. Staticman will create a pull request with the comment data. It can also commit directly without approval. GitHub access token is required.
1. You'll approve the comment and merge the pull request.
1. GitHub Pages (or your custom solution) will build your Jekyll site with the new commit.
1. Comment will appear on your website.

That's it. When you get it up and running, it works. Try it below.
