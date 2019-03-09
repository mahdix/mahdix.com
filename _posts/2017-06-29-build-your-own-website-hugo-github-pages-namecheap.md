---
id: 338
title: 'Build your own website: Hugo + Github Pages + Namecheap'
date: 2017-06-29T12:23:23+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=338
permalink: /blog/2017/06/29/build-your-own-website-hugo-github-pages-namecheap/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:2:"no";s:2:"id";s:12:"4b3b0b781f29";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:90:"https://medium.com/@mahdix/build-your-own-website-hugo-github-pages-namecheap-4b3b0b781f29";}'
categories:
  - Uncategorized
---
&nbsp;

Did you know that you can have your own website without paying for hosting?

Ok, the purpose of this post is not promoting a cost-saving method, but to show you how you can setup your website using a static content generator ([Hugo](https://gohugo.io/)) and hosting in on [GitHub.com](http://github.com) connected to your own domain. So you don&#8217;t need to pay for and manage a hosting service.

What you need for this:

  1. A registered domain name (You can still use this with a sub-domain on github.io website, but the process will be a bit different)
  2. An account in GitHub.com website
  3. A terminal application (Like [iTerm](https://www.iterm2.com/) on Mac or the command line on Windows)

Here is the list of steps you need to take:

  1. Setup DNS for your domain
  2. Setup your GitHub.com account
  3. Download Hugo on your computer and write something
  4. Update your GitHub.com account with the new content

And now the details!

## Setup DNS

Details of this step depend on your domain registrar. For me, it is [NameCheap](https://www.namecheap.com/).

You need to enter the address of GitHub.com as the hosting system for your domain. So if anyone types your domain name into their browser, they will be redirected to GitHub to show content.

First, you need to set &#8220;NAMESERVERS&#8221; option in the domain management page to &#8220;Namecheap BasicDNS&#8221;.

<a href="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.43.47.png" rel="attachment wp-att-345"><img class="alignnone size-full wp-image-345" src="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.43.47.png" alt="Screen Shot 2017-06-29 at 04.43.47" width="1044" height="226" /></a>

After that you can go to &#8220;Advanced DNS&#8221; page and update &#8220;HOST RECORDS&#8221; like below:

<a href="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.44.18.png" rel="attachment wp-att-346"><img class="alignnone size-full wp-image-346" src="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.44.18.png" alt="Screen Shot 2017-06-29 at 04.44.18" width="1926" height="652" /></a>

Here the numbers are specific to GitHub, so you will need to enter them exactly like this. &#8220;mahdix&#8221; is my username on the GitHub website. You will need to replace this with your own username.

## Set up GitHub

After setting up your DNS records, you need to tell GitHub about your domain. So when it is referred to about a domain name, it knows what to show.

Go to your GitHub.com account, add a new repository by clicking &#8220;+&#8221; button on the top right of the screen, and selecting &#8220;New repository&#8221;. It&#8217;s better if you name this new repository same as your domain name but you can choose whatever name you like.  Make sure &#8220;Initialise this repository with a README&#8221; is checked, so your repository will be created with some basic content.

<a href="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.47.32.png" rel="attachment wp-att-347"><img class="alignnone wp-image-347" src="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.47.32.png" alt="Screen Shot 2017-06-29 at 04.47.32" width="240" height="257" /></a>

## Get Hugo and write

First, you will need to open your console application. Make sure \`git\` is installed on your system. If it is not, follow instructions [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) to do the installation.

In the console, run \`git clone\` command to fetch a copy of your newly created repository.

Then you will need to run Hugo and write some content. You can follow instructions [here](https://gohugo.io/overview/quickstart/) as a starting guide.

When you are finished writing some content, you will need to push your changes to GitHub. This step informs GitHub about the new content you have just generated. This is done via \`git push origin\` command. Note that you may need to set up SSH keys if you are doing this for the first time (explained [here](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)).

## Finish GitHub set up

Now, you need to tell GitHub about your domain name. Go to your GitHub repository, and go to &#8220;Settings&#8221; tab. There is a section called &#8220;Custom Domain&#8221;. You will need to enter your domain name there:

<a href="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.56.25.png" rel="attachment wp-att-348"><img class="alignnone wp-image-348" src="http://www.mahdix.com/wp-content/uploads/2017/06/Screen-Shot-2017-06-29-at-04.56.25.png" alt="Screen Shot 2017-06-29 at 04.56.25" width="400" height="119" /></a>

After you have done this step, your new content will be visible in a few minutes (This takes a bit longer only for the first time). Note that you only need to do this step once.

# How to automate the publish process

There is a way to use some online services to automate the Hugo part. So you only need to make changes on GitHub website interface (write content, create new pages, &#8230;) and as soon as you submit your changes, they will be published for you. I will explain this in a follow-up post.

&nbsp;