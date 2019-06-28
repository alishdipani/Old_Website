---
layout:     post
title:      Rubyplot installation guide
date:       2019-06-09 21:32:18
summary:    Installation guide for Rubyplot
categories: GSoC2019
thumbnail: google
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---
This blog is a guide for installation of Rubyplot including both GR and ImageMagick backends.

Installation steps:
1. Installing GR C Framework: Follow the instructions given [[here]](https://software.opensuse.org/download.html?project=science:gr-framework&package=python-gr) but change the command to sudo apt-get install gr (instead of sudo apt-get install python-gr).  
GR will be installed in the path : `/usr/gr`
2. Create a symbolic link for GR in the path `/usr/local/gr` using this command:  
```bash
sudo ln -s /usr/gr /usr/local/gr
```
3. Now set the important environment variables by running this command:
```shell
export GRDIR="/usr/local/gr"
export GKS_FONTPATH="/usr/local/gr"
export RUBYPLOT_BACKEND="GR"
```
Alternatively you can set `GRDIR` and `GKS_FONTPATH` to the path where GR is installed if it's different.  
We have set the Rubyplot backend to GR
4. 
