---
layout:     post
title:      GSoC 2019 project introduction
date:       2019-05-20 21:32:18
summary:    Introducing my project for GSoC 2019.
categories: GSoC2019
thumbnail: google
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---

Google Summer of Code (GSoC) is a prestigious global program which aims to bring more student developers into open source software development. This program is for 3 months during the summers.  
  
In order to improve my software development skills and to get myself famialirised with open source software development, I applied for GSoC 2019 with two organisations - **Biomedical Informatics, Emory University** and **Ruby Science Foundation**. Thankfully, I was selected for GSoC 2019 with Ruby Science Foundation to work on the plotting library Rubyplot. This is the first of many blogs which I will be writing for my project. This first blog is an introduction to Rubyplot, my project and what to expect at the end of GSoC 2019. Stay tuned for more blogs :)  
  
P.S. - My proposals can be found here Biomedical Informatics, Emory University [[Link]](https://docs.google.com/document/d/1wGPSaHtE7v-tGWbXaIkxzNwbvQ6cCp0R5lxFxI0JGak/edit?usp=sharing)  ||  Ruby Science Foundation [[Link]](https://docs.google.com/document/d/1UoAE2MQ0l67ZZ0UWjykmRZnCHO_u22FM2zlHfVYUyEQ/edit?usp=sharing) (Up-to-date proposal [[Link]](https://github.com/alishdipani/rubyplot/wiki/GSoC-2019-Proposal))  
  
## What is Rubyplot?  
RubyPlot is a plotting library in Ruby for scientific development inspired by the library Matplotlib for Python. Users can create various types of plots like scatter plot, bar plot, etc. and can also create subplots which combine various of these plots.The long-term goal of the library is to build an efficient, scalable and user friendly library with a backend agnostic frontend to support various backends so that library can be used on any device.  
Currently Rubyplot supports only GR backend and a limited number of plots.  

## My Project  
My project is to add Magick backend and additional functionalities to Rubyplot. The outcomes of my project would be :  
1. **ImageMagick support**:  Support for Imagemagick back-end will be added in addition to the currently supported back-end GR, the front-end of the library will be back-end agnostic and the current overall integrity of the library will be preserved.
2. **Plotting and show function**: A new **plot** function will be added which plots markers (for example circles) to form a scatter plot or a line plot with just the points as inputs. A new function **show** will be added which will allow viewing of a plot without saving it. This plot function will be back-end agnostic and hence will support both GR and Magick back-end.
3. **Integration with iruby notebooks**: Rubyplot will be integrated with iruby notebooks supporting all backends and allowing inline plotting.  
