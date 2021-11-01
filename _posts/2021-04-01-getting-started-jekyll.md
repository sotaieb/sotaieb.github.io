---
title: "Getting started Jekyll"
categories:
  - Blogging
tags:
  - Jekyll
---

## Introduction

Jekyll is a static site generator.

## Install

1- Install Ruby+Devkit.
<https://rubyinstaller.org/downloads/>
Install MSYS2 and MINGW dev tools on the last stage.

2- Open a new command prompt, then install, jekyll (last version supported by github-pages)
and bundler packages.
gem install bundler jekyll

3- Check if Jekyll has been installed properly.
jekyll -v

4- Hello World
jekyll new hellworldjekyll

5- Build the site and make it available on a local server.
bundle exec jekyll serve --watch // --force_polling

4- Add webrick package to Gemfile (Http Server for dev)
gem "webrick", "~> 1.7"
Or
bundle add webrick

5- Fetch and update bundled gems
( gh-pages supported dependency versions: https://pages.github.com/versions/)
bundle install

5- Build or Run local server
bundle exec jekyll build
OR
bundle exec jekyll serve
(bundle exec rake preview)

## Features

1- Sitemap
<https://sotaieb.github.io/sitemap>
2- Feeds
<https://sotaieb.github.io/feed.xml>
3- Analytics
google tracking
analytics.google.com
search.google.com/search-console
<https://cse.google.com/cse>

<https://developers.google.com/speed/pagespeed/insights/>
