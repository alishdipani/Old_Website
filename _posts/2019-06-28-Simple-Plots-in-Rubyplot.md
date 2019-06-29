---
layout:     post
title:      Simple Plots in Rubyplot
date:       2019-06-28 21:32:18
summary:    Simple plots in Rubyplot
categories: GSoC2019
thumbnail: google
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---
This blog lists down the simple plots in Rubyplot with ImageMagick as backend and highlights the work done in Week 3 and 4 for GSoC 2019.  
  
**Table of Contents**
* TOC
{:toc}

# Scatter plot
The scatter plot draws markers at the position specified by the user, the inputs taken are the X and Y coordinates for the markers (`data`), size of the marker (`marker_size`), type of the marker (`marker_type`), colour of the border of the marker (`marker_border_color`) and colour to be filled inside the marker (`marker_fill_color`).  
If the marker does not have a fill colour (example - plus, diagonal cross, dot) then the fill colour is set as the colour of the colour of the marker.  
Scatter plot uses the backend function `draw_markers`:
```ruby
def draw_markers(x:, y:, type: :default, fill_color: :default, border_color: :default, size: nil)
  y.each_with_index do |iy, idx_y|
    ix = transform_x(x: x[idx_y],abs: false)
    iy = transform_y(y: iy, abs: false)
    # in GR backend size is multiplied by
    # nominal size generated on the graphics device
    # so set the nominal_factor to 15
    within_window do
      size[idx_y] *= NOMINAL_FACTOR_MARKERS
      MARKER_TYPES[type].call(@draw, ix, iy, fill_color, border_color, size[idx_y])
    end
  end
end
```
The coordinates are first transformed, then the size of the marker is multiplied by a nominal factor (hardcoded to 15) to match the marker sizes in GR backend as GR backend multiplies its marker sizes by a nominal factor which is not mentioned.  
To draw the markers, a hash is used which has lambdas for drawing different types of markers, an example of the hash is:
```ruby
MARKER_TYPES = {
  # Default type is circle
  # Stroke width is set to 1
  default: ->(draw, x, y, fill_color, border_color, size) {
    draw.stroke Rubyplot::Color::COLOR_INDEX[border_color]
    draw.fill Rubyplot::Color::COLOR_INDEX[fill_color]
    draw.circle(x,y, x + size,y)
  },
  circle: ->(draw, x, y, fill_color, border_color, size) {
    draw.stroke Rubyplot::Color::COLOR_INDEX[border_color]
    draw.fill Rubyplot::Color::COLOR_INDEX[fill_color]
    draw.circle(x,y, x + size,y)
  },
  plus: ->(draw, x, y, fill_color, border_color, size) {
    # size is length of one line
    draw.stroke Rubyplot::Color::COLOR_INDEX[fill_color]
    draw.line(x - size/2, y, x + size/2, y)
    draw.line(x, y - size/2, x, y + size/2)
  }
  # Rest of the code omitted due to space constraint
}
```
Each of these lambdas take the properties of the marker and draw it, for example the `circle` type draws a circle with size as its radius, the `plus` type draws two perpendicular lines in the shape of a plus sign (+) with the size as length of the lines.  
A hash of lambdas is used instead of making a module or a class to avoid inconsistencies in backends as GR does not require functions for creating marker types and only requires the type of the marker, i.e. its internal functions take care of drawing the marker types. Also, generally Hashes are much faster than functions and hence hashes are preferred over a module or a class.  
  
An example of scatter plot with code is:
```ruby
@x1 = [1, 2, 3, 4, 5]
@y1 = [11, 2, 33, 4, 65]
@figure = Rubyplot::Figure.new
axes = @figure.add_subplot! 0,0
axes.scatter! do |p|
  p.data @x1, @y1
  p.label = "data1"
  p.marker_size = 3
  p.marker_fill_color = :blue
  p.marker_type = :diagonal_cross
end
axes.title = "Scatter plot"
axes.x_title = "X data"
axes.y_title = "Y data"
@figure.write('scatterplot.png')
```
![scatter plot](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/Simple_Plots_in_Rubyplot/scatterplot.png)
# Area plot
The area plot draws a line passing through the points given as the input and colours the area under this line.  
If only one set of values is given to the area plot as the data, for example [23, ]
# Bar plot
# Bubble plot
# Candle-stick plot
# Histogram
# Line plot
# Error-bar plot
# Box plot
