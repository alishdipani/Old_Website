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
This blog is a guide for Developer installation of Rubyplot including both GR and ImageMagick backends.  
* TOC
{:toc}
  
# Installation
P.S.- Make sure you have installed Ruby and have updated it to the latest version.  
  
Installation steps:
1. Installing GR C Framework: Follow the instructions given [[here]](https://software.opensuse.org/download.html?project=science:gr-framework&package=python-gr) but change the command to sudo apt-get install gr (instead of sudo apt-get install python-gr).  
GR will be installed in the path : `/usr/gr`
2. Create a symbolic link for GR in the path `/usr/local/gr` using this command:  
```bash
sudo ln -s /usr/gr /usr/local/gr
```
3. Clone the Rubyplot repository using this command:
```
git clone https://github.com/SciRuby/rubyplot.git
```
4. Change the current directory to Rubyplot folder by using this command:
```bash
cd rubyplot
```
5. Now set the important environment variables by running this command:
```shell
export GRDIR="/usr/local/gr"
export GKS_FONTPATH="/usr/local/gr"
export RUBYPLOT_BACKEND="GR"
```
Alternatively you can set `GRDIR` and `GKS_FONTPATH` to the path where GR is installed if it's different.  
We have set the Rubyplot backend to GR.  
6. Replace line 3 and 4 in `ext/grruby/extconf.rb` by these lines:
```
$CFLAGS << ' -I/usr/local/gr/include '
$LDFLAGS << ' -L/usr/local/gr/lib -lGR -lm -Wl,-rpath,/usr/local/gr/lib '
```
Or change it to the path where GR is present if different.  
7. Change lines 13 and 14 in `spec/spec_helper.rb` to this:
```
ENV['GRDIR'] = "/usr/local/gr"
ENV['GKS_FONTPATH'] = "/usr/local/gr"
```
Or change it to the path where GR is present if different.  
8. Run these commands:
```bash
sudo gem install bundler
bundle install
rake compile
```
GR backend has been setup, now we need to set up the ImageMagick backend.
9. To install ImageMagick and rmagick follow the instructions [[here]](https://github.com/rmagick/rmagick).  
10. Now, we have setup both the backends and can change the backends at any time using this command:
```bash
export RUBYPLOT_BACKEND="GR" # For GR backend
export RUBYPLOT_BACKEND="MAGICK" # For ImageMagick backend
```
  
# Testing
For testing Rubyplot follow these instructions:

