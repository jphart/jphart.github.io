---
layout: post
title:  "Dockerizing Jeykll"
date:   2021-03-28 16:00:00 +0000
categories: 
    - project
    - docker
---

This seems a bit meta to start off with. [Github](http://github.com) where this site is hosted allows you to build websites using the [Jeykll](https://jekyllrb.com) blogging tool. To build and setup the site on my laptop before pushing live I wanted a local Jeykll installation.

On MacOS you can use [brew](http://brew.sh) to install Ruby and then Jekyll, however rather install a load of Ruby cruft, I'd rather have a simple docker container that I can easily tear down, remove and keep updated. 

At the time of writing there isn't an official image, so I had to make my own. 

You can checkout the Dockerfile at: [https://github.com/jphart/jekyll-docker](https://github.com/jphart/jekyll-docker).

To build the image:
```bash
$ docker build . -t jeykll
```

To view a jeykll site, expose port 4000 and mount your Jeykll files at /data.
```bash
$ docker run -p 4000:4000 -v /data:../path/to/site jeykll
```

Or, edit the docker-compose.yml to and run:

```bash
$ docker-compose up -d
```

Open your web browser to localhost:4000 & you're good to go.