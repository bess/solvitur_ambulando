---
layout: post
title:  Self hosting a kanban board
date:   2022-10-09 15:44:39 -0400
categories: documentation
---
## Self-hosting a Kanban solution

I use [kanban](https://en.wikipedia.org/wiki/Kanban_(development)) boards a lot, and I recently found myself needing to run several kanban boards that will also include other people. I started by signing up for Trello, but the pricing is prohibitive. I'm willing to pay $12 / month for myself, but not an additional $12 / month for every extra user I add. I decided to try hosting a Kanban board myself, and I quickly found [planka](https://github.com/plankanban/planka).

Planka recommends running it via its included docker-compose script, and indeed trying that out locally was straightforward. However, getting it going on a server took me a few hours, and I didn't find all the information I needed in one place, so I'm going to document it here. This is mostly for my own record keeping, but if it helps someone else then that's a bonus.

### 1. Finding a server
I work with AWS at work, but I'm not eager to give Amazon any more money than I already do, so I did some research and I was happy to find [Kamatera](https://www.kamatera.com/). I got an Ubuntu Linux server with 1CPU, 1G RAM, and 10G disk for $9 per month. Adding a daily backup brought the bill to $12 per month, which is the same as I was paying for a single Trello account.

It's only been a few days but so far I'm very happy with Kamatera. I found the account creation and server provisioning much easier than AWS.

Cost: $12 per month

### 2. Registering a domain name
I registered a domain name (solviturambulando.info) and pointed `kanban.solviturambulando.info` to the static IP address of the new server I had just spun up.

Cost: $3, plus annual renewal

### 3. Running planka
I hadn't run a docker container in production before, but I followed [this  guide](https://medium.codekoalas.com/getting-started-with-docker-for-self-hosting-all-the-things-7b3bcb987a5b) and it got me pointed in the right direction. I made a directory called `/opt/docker-compose`, put [the planka docker-compose.yml file](https://raw.githubusercontent.com/plankanban/planka/master/docker-compose.yml) in it, gave it a proper `SECRET_KEY` and `BASE_URL` and started it (`docker-compose up --build -d`).

Now I had planka running on port 3000, and I could reach it from the server via `curl http://localhost:3000.`

### 4. Reverse proxy and SSL
This was the part of the process that gave me the most trouble. I spent several hours trying to get nginx reverse proxy running in the same `docker-compose.yml` file, but I eventually gave up.

Instead, I went with a standard nginx on Ubuntu and used [let's encrypt](https://letsencrypt.org/) to install an SSL cert.

1. Install [certbot](https://certbot.eff.org/instructions)
2. Run `sudo certbot --nginx`

This failed the first time I ran it and I had to temporarily open port 80 long enough for certbot to talk to its mothership, but once I got past that it was smooth and by far the easiest SSL installation I've ever done (and I've done a lot of them).

Certbot not only got the cert downloaded and installed, it generated an nginx configuration file for me. 
