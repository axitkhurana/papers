
+++
date = "2014-10-13 07:39:20"
draft = false
title = "Introducing Buster"
slug = "introducing-ghost"
tags = ['Blogging', 'Ghost', 'Buster', 'Github']
banner = ""
aliases = ['/introducing-ghost/']
+++

            

![Ghost](/images/2014/10/ghost.png)

[Buster](https://github.com/axitkhurana/buster) is a Super simple, Totally awesome, Brute force **static site generator for [Ghost](http://ghost.org "The Open Source Blogging Platform")** inspired by Octopress.

_Generate Static Pages. Preview. Deploy to Github Pages._

## The interface

`setup [--gh-repo=<repo-url>]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Creates a GIT repository inside `static/` directory. If you don't specify the `repo-url`, buster will ask you the clone URL of your new Github Pages repository.

`generate [--domain=<local-address>]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Generates static pages from locally running Ghost instance.

`preview`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Preview what's generated on `localhost:9000`.

`deploy`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Commits and deploys changes static files to Github repository.

`add-domain <domain-name>`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Adds CNAME file with custom domain name as required by Github Pages.

Buster assumes you have `static/` folder in your current directory (or creates one during `setup` command). You can specify custom directory path using `[--dir=<path>]` option to any of the above commands.

## Ghost. What?

[Ghost](http://ghost.org/features/ "Ghost Features.") is a beautifully designed, completely customisable and completely [Open Source](https://github.com/TryGhost/Ghost "Ghost on Github") **Blogging Platform**. If you haven't tried it out yet, check it out. You'll love it.

The Ghost Foundation is not-for-profit organization funding open source software and trying to completely change the world of online publishing. Consider [donating to Ghost](http://ghost.org/about/donate/ "You are awesome!")

### Buster?

Inspired by THE GhostBusters.

![Ghost Buster Movie](http://upload.wikimedia.org/wikipedia/en/c/c7/Ghostbusters_cover.png)

## Contributing

Source is available on [Github](https://github.com/axitkhurana/buster/). Checkout the existing [issues](https://github.com/axitkhurana/buster/issues) or create a new one. Pull requests welcome!

* * *

_Made with [jugaad](http://en.wikipedia.org/wiki/Jugaad) in [Dilli](http://en.wikipedia.org/wiki/Delhi)._

<sub>Ghost icon designed by [Paulo Volkova](http://thenounproject.com/noun/ghost/#icon-No3941), 2012</sub>

