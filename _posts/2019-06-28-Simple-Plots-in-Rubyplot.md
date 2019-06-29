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
Each of these lambdas take the properties of the marker and draw it depending on the type of marker, for example the `circle` type draws a circle with size as its radius, the `plus` type draws two perpendicular lines in the shape of a plus sign (+) with the size as length of the lines.  
A hash of lambdas is used instead of making a module or a class to avoid inconsistencies in backends as GR does not require functions for creating marker types and only requires the type of the marker, i.e. its internal functions take care of drawing the marker types. Also, generally Hashes are much faster than functions and hence hashes are preferred over a module or a class.  
  
Marker types implemented till now for ImageMagick bacckend are: circle, plus, dot, diagonal_cross, solid_circle, traingle_down, solid_traingle_down, traingle_up, solid_traingle_up, square, solid_square, bowtie, solid_bowtie, hglass, solid_hglass, diamond, solid_diamond, solid_tri_right, solid_tri_left, hollow_plus, solid_plus, vline, hline, omark.  
  
Marker types not yet implemented for ImageMagick backend are: star, solid_star, asterisk, tri_up_down, pentagon, hexagon, heptagon, octagon, star_4, star_5, star_6, star_7, star_8.  
  
# Area plot
An example of Area plot with code is:
```ruby
@figure = Rubyplot::Figure.new
axes = @figure.add_subplot! 0,0
axes.area! do |p|
  p.data [30, 36, 86, 39, 27, 31, 79, 88]
  p.label = "Jimmy"
end
axes.title = "Area plot"
@figure.write('areaplot.png')
```
![area plot](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/Simple_Plots_in_Rubyplot/areaplot.png)
  
The area plot draws a line passing through the points given as the input and colours the area covered under this line.  
If only one set of values is given to the area plot as the data, for example only [30, 36, 86, 39, 27, 31, 79, 88] is given then this set is considered as a set of Y coordinates of the points and X coordinates are set as o, 1, 2 ... (size of y values - 1) i.e. the coordinates are (0, 30),(1, 36),(2, 86),(3, 39),(4, 27),(5, 31),(6, 79),(7, 88).  
  
Now, this is the list of coordinates through which the line has to pass and the area under the curve is to be filled, but to draw this a polygon is needed in which the colour will be filled. TO create this polygon, we just need to append the starting point and end point of the X axis so that the polygon is completed. So, here in the example taken above the starting point of X axis is (minimum x value, minimum y value) i.e. (0, 27) and the end point of X axis is (maximum x value, minimum y value) i.e. (7, 27).  
So, finally the coordinates for the polygon are (0, 30),(1, 36),(2, 86),(3, 39),(4, 27),(5, 31),(6, 79),(7, 88),(0, 27),(7, 27).  
The code for appending these points is: 
```ruby
x_poly_points = @data[:x_values].concat([@axes.x_axis.max_val, @axes.x_axis.min_val])
y_poly_points = @data[:y_values].concat([@axes.y_axis.min_val, @axes.y_axis.min_val])
```
  
The opacity of the polygon i.e. the area under the curve is set to **0.3** to make the areas behind an area visible.  
After this, a `Rubyplot::Artist::Polygon` object is created whhich uses the `draw_polygon` backend function to draw the polygon, the `draw_polygon` function is:
```ruby
def draw_polygon(x:, y:, border_width:, border_type:, border_color:, fill_color:,
  fill_opacity:)
  within_window do
    x = x.map! { |ix| transform_x(x: ix, abs: false) }
    y = y.map! { |iy| transform_y(y: iy, abs: false) }
    coords = x.zip(y)
    @draw.stroke_width border_width
    @draw.stroke Rubyplot::Color::COLOR_INDEX[border_color]
    @draw.fill Rubyplot::Color::COLOR_INDEX[fill_color]
    @draw.fill_opacity fill_opacity
    @draw.polygon *coords.flatten
    @draw.fill_opacity 1
  end
end
```
Thsi function first transforms the coordiantes and then combines the coordiantes which were two different sets before (x values = [0, 1, 2, 3, 4, 5, 6, 7, 0, 7] and y values = [30, 36, 86, 39, 27, 31, 79, 88, 27, 27]), the `zip` Ruby function is used to combine the points (now coords = [[0, 30], [1, 36], [2, 86], [3, 39], [4, 27], [5, 31], [6, 79], [7, 88], [0, 27], [7, 27]]). After this the properties of the polygon are set and the coordinates are given as an input to the polygon function of rmagick, but first the coordiantes array is flattened (now coords = [0, 30, 1, 36, 2, 86, 3, 39, 4, 27, 5, 31, 6, 79, 7, 88, 0, 27, 7, 27]) so that input is converted into the way that polygon function accepts, the splat operator (\*) is used to give the coords array as an argument to the function. Finally, the opacity of `Magick::Draw` object is set again to 1 (initial value).

# Bar plot
# Bubble plot
# Candle-stick plot
# Histogram
# Line plot
# Error-bar plot
# Box plot
