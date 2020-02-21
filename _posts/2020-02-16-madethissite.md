---
title: "How I made this site"
layout: post
date: 2020-02-15 
categories: [github, jekyll, meta]
excerpt: This page serves as documentation and an explanation of how I made this site, all the gory details included. 
---

# The Goal

I set out to create a personal website using exclusively open source technologies hosted on github pages for completely free. I wanted to ensure a template that would allow for easy testing on my local machine, as well as seamless deployment to github for publishing. I also looked for a relatively simple structure that would allow for custom CSS or javascript future hosting future projects. 


# Github Pages Default "Personal Website"

To kick off the process, I started with forking the [personal-website](https://github.com/github/personal-website) github repo, which allows one dive right into the world of Jekyll. Modifying `config.yml` seemed simple enough at first. 

After successfully replacing some of the boilerplate, I decided I wanted to replace the Jekyll theme. I thought this would be simple...my impression was that adding a single line to my config file would whip up a completely different theme. This was completely wrong. 

What I did not understand at the time was that `personal-webiste` *was* a theme itself. Using a new theme involved a whole new suite of files and structure. This is despite the many pages and articles which appeared to instruct me to simply add `theme: your-theme-here` to my config file, or change my theme directly on github through the UI. [Like this one](https://help.github.com/en/github/working-with-github-pages/adding-a-theme-to-your-github-pages-site-with-the-theme-chooser). (If you actually read this article you'll notice they say that if you've added a certain theme before that this method won't work. What, you think I actually read that article top-to-bottom? Psh.)

# The "Hyde" Side of Jekyll - Installing it 

In the midst of the madness of trying to change themes, I was committing and pushing to github left-and-right to test out my changes. At some point this seemed to get out of hand, so I pivoted to trying to locally install Jekyll. 

I already had Ruby installed, but [Jekyll instructed me](https://jekyllrb.com/docs/installation/macos/) to add the the MacOS command-line tools to compile native extensions in ruby. Turns out this was a whole ordeal that involved a big update to MacOS? And I still don't know if it worked properly. 

...more details here... 

Eventually I just gave up on installing Jekyll entirely and just switched to using Docker. Docker eliminates this exhausting dev-ops work and allows you spin-up a container where everything just works. I'm using the [official jekyll docker image](https://hub.docker.com/r/jekyll/jekyll/) with help in deployment from [here](https://hub.docker.com/r/jekyll/jekyll/), but basically it's as easy as running 
```bash 
docker pull jekyll/jekyll 
```
to fetch the image, then running 
```bash 
docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -it jekyll/jekyll:4.0 \
  jekyll build
```
and replacing `4.0` with whatever the [latest stable version](https://jekyllrb.com/news/releases/) is (`4.0` is current as of time of writing; I'm running `3.8` on my own machine right now). 






