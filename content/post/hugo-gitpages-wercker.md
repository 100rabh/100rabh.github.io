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

We are going with option 1 here. 
So just follow the inst. in github pages i.e. setup repo with name <username>.github.io.

Here username should be your/org's github username.
Create two branches one master which will have the public folder contents (from the site generated with hugo).
Other you can name anything and this will actually hold the source contents which will be used by hugo to create site for.

Now if you have read the hugo docs(which you should), you know that there will be a /public folder created by hugo and this contains actual contents of your site which will be hosted by github (if placed in master branch of repo). But since We are 
creating site with hugo we don't  nedd to commit this. So use .gitignore file to ignore /public folder.

So at last we have a repo in git with two branches one master containing `/public` folder contents and other branch(suppose source) containing source contents of hugo site. 
If all setup good, you will be able to see your site at http://<username>.github.io/
Now we just have to automated the things so that we don't have to commit to the both branch each time we made a change to our site.

## Automation with `wercker`
Up to now you have a site hosted from github pages. 
Repo on github with branches master and source.
To make any change in site you will be commiting changes to source branch (just to maintain your hugo source code).
and changes of /public folder to master branch.

Now we will automate this process using wercker. 
But before that just push wercker.yml file with following code in `source` branch.
      
      box: debian
      build:
        steps:
          - arjen/hugo-build:
              version: "0.15"
              flags: --buildDrafts=true

      deploy:
        steps:
          - install-packages:
              packages: git ssh-client
          - leipert/git-push:
              gh_oauth: $GIT_TOKEN
              basedir: public
              clean_removed_files: true
              branch: master
              repo: <Your github repo>

No go to [wercker](http://wercker.com) and follow steps:

      1: Create wercker account using github or else.
      2: Select github repo (in this case <username>.github.io) 
      3: Configure access (choose recommended)
      4: Setup your wercker.yml (will be done automatically as we already have it in our repo branch)
      5: You can OR can't make this wercker public
      6: Build it it should be GREEN
      7: Go to settings and for option `Ignored branches` add `master` branch.

Now since wercker build step is GREEN, We will try to make it deploy to master branch:

      1: Got to newly build wercker application -> settings (right top)
      2: Add deploy target (choose custom deploy)
      3: Git deploy target a nice name (offcource anything)
      4: Select auto deploy and add branch name `source` (where your source code it in git repo)
      5: Add a new variable `GIT_TOKEN` (as specified in wercker.yml file)
      6: For the text value of `GIT_TOKEN`, You need to goto your github profile setting page.
      7: Select Personal Access Tocken there and generate one (name it anything and copy the value)
      8: Now goto wercker and paste it as `GIT_TOKEN` text value.
      9: Save everything

Now got to make some change to your local hugo site/repo and push those changes to source branch.
You will see your wercker building your changes from source and pushing them to master so that you can see them LIVE !!

### SOS screenshots

Following screenshots will help you to understand automation process.
<p></p>
{{< gallery "/gal/hugo-wercker/Git Branches.jpg" "/gal/hugo-wercker/gitTocken.jpg" "/gal/hugo-wercker/PA-token.jpg" "/gal/hugo-wercker/wercker-app-setting.jpg" "/gal/hugo-wercker/wercker-ignored.jpg" "/gal/hugo-wercker/wercker-git.jpg" "/gal/hugo-wercker/wercker-config.jpg" "/gal/hugo-wercker/wercker-customTarget.jpg" "/gal/hugo-wercker/wercker-target.jpg" >}}