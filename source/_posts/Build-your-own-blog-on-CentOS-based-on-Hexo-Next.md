title: 'Build your own blog on CentOS, based on Hexo + Next'
tags:
  - Hexo
categories: []
date: 2017-11-02 10:21:00
---
## Prepare environment
### Add yum repo

```Bash
# yum install -y gcc-c++ make
# curl -sL https://rpm.nodesource.com/setup_6.x | sudo -E bash -
```

<!-- more -->
### Install node and npm

```Bash
# yum install nodejs
```

### Install git

```Bash
# yum install git
```

### Validate installation

```Bash
# node -v
# npm -v 
# git --version
```

## Hexo site
### Install Hexo

```Bash
$ npm install -g hexo-cli
```

### Create and init your site
Creating your site in your own folder

```Bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
*Mind the path when run npm install cmd*

### Site folder

```
.
├── _config.yml     # Configuration of site(!!)
├── package.json    # npm installed packages
├── scaffolds       # template files
├── source
|   ├── _drafts     # Draft pages, omit
|   └── _posts      # Website original pages
└── themes          # Website theme
```

List generated files to check result

### Hello world

```Bash
$ hexo generate     # Generate static files
$ hexo server       # Start server, default port 4000
```

Or:

```Bash
$ hexo g            # In short
$ hexo s -p <port>  # In short, specify port
```

### First blog
Create a new blog(in markdown format), if title contains white space, please use quotes

```Bash
$ hexo new <titile>
```
It will create a new post with basic tags in path <folder>/source/_post

*Mind: no need to restart server after new/delete/modify posts*

## One more step further
### Common cmd

```Bash
$ hexo new draft <title>    # Create a new draft(like private posts)
$ hexo publish <filename>   # Publish drafts

$ hexo clean    # Clean cache and static files
$ hexo deploy   # deploy the site

$ hexo list post/page/tag/category  # List types
```

### Front-matter
Defines variables of post

```YAML
---
title: Hello World          # Title of post(may differ from filename)
date: 2013/7/13 20:46:25    # Create date
tags:                       # Tags of post, in YAML format
- tag0
- tag1
categories:                 # Categories of post, in YAML format
- API
- Guide
---
```

### Multi-language support

```
new_post_name: :lang/:title.md
permalink: :lang/:title/
```

When you create a new post, the post will be saved to:


```
$ hexo new "Hello World" --lang tw
# => source/_posts/tw/Hello-World.md
```

and the URL will be:

```
http://localhost:4000/tw/hello-world/
```

## Themes
### New theme
Recommend *next*, or you can find other themes instead

Clone new theme to website folder

```Bash
$ cd <folder>
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

Enable this theme in site <folder>/_config.yml

```YAML
theme: next
```

### Config theme
Use *next* as example, others may be found on their website

```YAML
language: zh-Hans               # Set language
avatar: [URL] or under source/  # Set your own avata
favicon: [URL]                  # Use your own favicon
author: <nickname>              # Set your nickname, shown on page
```

## Plugins
Some common plugins may enhance your website

> [hexo-wordcount](https://github.com/willin/hexo-wordcount) Count total words, minutes to read
> 
> [hexo-admin](https://github.com/jaredly/hexo-admin) Provide admin pages to manage hexo site
> 
> [hexo-generator-search](https://github.com/flashlab/hexo-generator-search) Index pages and provide local search

## Reference
[Hexo Startup Guide](https://hexo.io/zh-cn/docs/index.html)
[Next Theme Getting Started](http://theme-next.iissnan.com/getting-started.html)
