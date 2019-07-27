---
layout:     post
title:      The show and the plot functions
date:       2019-07-26 21:32:18
summary:    Multi plots in Rubyplot
categories: GSoC2019
thumbnail: google
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---
**Table of Contents**
* TOC
{:toc}

# Introduction
This blog explains the working of the show and the plot functions and highlights the work done in Week 7 and 8 for GSoC 2019.  

# show
The **show** function is an alternative to the write function which is called on the `Rubyplot::Figure` object i.e. the Figure or the Canvas, it draws the figure which is stored temporarily and is shown on a pop-up window. This is used for quick view of the Figure and helps in debugging.  
  
Usage of this function is same as **write** function, suppose `figure = Rubyplot::Figure.new` then **write** function is called as `figure.write('filename.png')`, similarly the **show** function is called as `figure.show`. It takes no input.  
  
The **write** and the **show** functions are:
```ruby
def write(file_name, device: :file)
  Rubyplot.backend.canvas_height = @height
  Rubyplot.backend.canvas_width = @width
  Rubyplot.backend.figure = self
  Rubyplot.backend.init_output_device(file_name, device: :file)
  @subplots.each { |i| i.each(&:process_data) }
  @subplots.each { |i| i.each(&:draw) }
  Rubyplot.backend.write
  Rubyplot.backend.stop_output_device
end

def show
  Rubyplot.backend.canvas_height = @height
  Rubyplot.backend.canvas_width = @width
  Rubyplot.backend.figure = self
  Rubyplot.backend.init_output_device(nil, device: :window)
  @subplots.each { |i| i.each(&:process_data) }
  @subplots.each { |i| i.each(&:draw) }
  Rubyplot.backend.show
  Rubyplot.backend.stop_output_device
end
```
The only difference between both the functions is that the initialization is done differently i.e. **init_output_device** backend function, for **show** function there is no file name and hence `nil` is given and the device is `:window`, for the **write** function the device is `:file`. The main difference is that backend functions called are different.  
## Device
The `device` argument and backend variable(`output_device`) represent the device on which the Figure is to be drawn. The types are:
1. `:window` which represents a pop-up window on which the Figure will be shown for the **show** function.  
2. `:file` which represents a file on which the Figure will be saved for the **write** function.  
  
## Backend functions


# plot
