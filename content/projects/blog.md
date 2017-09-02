---
title: "Creating blog using hugo"
date: 2017-09-01T15:00:23-04:00
draft: false
---
I've been meaning to create a personal blog for a long time. I did some research
and decided to go with hugo. There's one main reason for using hugo over anything
else:

- amazing speed
- org-mode support out-of-the box
- :smiley: emojis?

As a matter of fact this post is written fully in org. I started with the
[quickstart guide](https://gohugo.io/getting-started/quick-start/). There's a
really great minimalistic theme
[cocoa-eh](https://github.com/fuegowolf/cocoa-eh-hugo-theme). After messing with
a config.toml and deploying to [github
pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/) it was time
for some personalization. You can see beautiful avatar on the front page showing
my deep appreciation for animated series([South Park Avatar
Creator](http://southpark.cc.com/avatar)). Mongolian hat is the most important piece,
I happen to be one russian born mongolian :laughing:

Don't forget to enable emojis

```toml
enableEmoji = true
```

If you want to host it at the root of your github pages you should follow
deploying on master branch guide. If you follow this guide, this script
simplifies the process of deployment

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public
# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back up to the Project Root
cd ..
```

If you did everything correctly, - your repo after deployment should look
something like this:
![repo](/img/repo.png "repo screen")

Look at that speed!!!
```bash
$ hugo
Started building sites ...
Built site for language en:
0 draft content
0 future content
0 expired content
4 regular pages created
10 other pages created
0 non-page files copied
0 paginator pages created
0 tags created
0 categories created
total in 17 ms
```


