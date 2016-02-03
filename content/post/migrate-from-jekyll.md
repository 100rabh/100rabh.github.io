+++
title = "How to host a static website/blog with Hugo, Github Pages and Wercker"
date = "2015-02-02T13:07:31+02:00"
tags = ["Github Pages", "Hugo", "Blog", "wercker"]
categories = ["Tutorials"]
menu = ""
banner = "banners/githugo.png"
+++

## What's up with `HUGO`
Hugo is a static site generator. Sites generated with Hugo tends to be [fast](https://youtu.be/CdiDYZ51a2o), easy to host on different providers like AWS, GitHub Pages, Google Cloud etc. Visit official Hugo wesite for [more](https://gohugo.io/overview/introduction/).

## Create a static site with Hugo
First of all you need to have hugo installed in your working Env. Basically you may want to refer [Hugo Installation documentation](https://gohugo.io/overview/installing/) for details. Which is pretty much straightforward.
After that follow these commands to create site with hugo:

      $ hugo new site path/to/site
      $ cd path/to/site
The new site will have the following structure:

      ▸ archetypes/
      ▸ content/
      ▸ data/
      ▸ layouts/
      ▸ static/
      config.toml

Now you have static site without any theme. If you want to have one you are in the right place Hugo has rich [theme](https://gohugo.io/themes/overview/) support.

## Run your site with Hugo server
       $ hugo server
       OR If on windows use:
       $ hugo server --renderToDisk

On console you will see a message like: `Web Server is available at http://localhost:1313`.
Go to the browser and verify the site.
You can have many [options](http://gohugo.io/overview/quickstart/#step-7-have-fun), with hugo to build the site as you want. Just play around with those options untill you have the site you want.

## Setup 'git'
Now that your site been setup locally as desired. You need to host it `live`. We will use [github pages](https://pages.github.com/) for that.
There are two option we have here to host with github pages:

      1:  User or Organization site (served from `master` branch)
      2:  Project site (served from `gh-pages` branch)

We are going with option 1 here. So just follow the inst. in github pages i.e. setup repo with name `username`.github.io.
Here username should be your/org's github username.
Create two branches one master whuch will have the public folder contents (from the site generated with hugo).
Other you can name anything and this will actually hold the source contents which will be used by hugo to create site for.

Now if you have read the hugo docs(which you should), you know that there will be a /public folder created by hugo and this contains actual contents of your site which will be hosted by github (if placed in master branch of repo). But since We are 
creating site with hugo we don't  nedd to commit this. So use .gitignore file to ignore /public folder.

So at last we have a repo in git with two branches one master containing `/public` folder contents and other branch(suppose source) containing source contents of hugo site. Now we just have to automated the things so that we don't have to commit to the both branch each time we made a change to our site.

## Automation with `wercker`

