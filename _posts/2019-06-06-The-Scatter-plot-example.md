---
layout:     post
title:      The Scatter plot example
date:       2019-06-06 21:32:18
summary:    Explaining the scatter plot example.
categories: GSoC2019
thumbnail: google
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---
In the project introduction blog a scatter plot example was given, I will be explaining the technical details for that example which involves explaining the code for the library using **Magick backend**.  
P.S. - Thr version of library used for this example is of date 6 June.  
  
The example code : 
```ruby
require 'Rubyplot'
@xdata = [1, 2, 3, 4, 5] # Defining X data
@ydata = [11, 2, 33, 4, 65] # Defining Y data
@figure = Rubyplot::Figure.new # Creating the canvas
axes = @figure.add_subplot! 0,0 # Adding a subplot (0,0 as only one subplot is to be plotted)
axes.scatter! do |p| # Setting scatter as the type of subplot
  p.data @x1, @y1 # setting data to be plotted
  p.label = "data1" # defining label for the data
  p.marker_fill_color = :blue # defining colour of the markers
  p.marker_type = :circle # defining marker type
end
axes.title = "Nice plot" # defining title of the plot
axes.x_title = "X data" # defining title of X axis
axes.y_title = "Y data" # defining title of Y axis
@figure.write("scatterplot.png") # Drawing the figure and saving it
```
  
The output using Magick backend is:
![Scatter-Plot with Magick backend](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/GSoC_2019_project_introduction/scatter_Magick.png)

# Code Explanation

So to go through the code let's start with the first 3 lines
```ruby
require 'Rubyplot'
@xdata = [1, 2, 3, 4, 5] # Defining X data
@ydata = [11, 2, 33, 4, 65] # Defining Y data
```
First Rubyplot is included which is similar to import in Python, this allows us to use Rubyplot.  Next we define 2 arrays xdata and ydata which store the X and Y coordinates to be plotted.  
## Figure
After this we create a new Figure object which will act as the canvas on which image will be drawn.
```ruby
@figure = Rubyplot::Figure.new # Creating the canvas
```
Creating a figure object calls the constructor of the figure class.
```ruby
def initialize(height: nil, width: nil, figsize_unit: :cm)
    super(0, 0)
    @title = ''
    @nrows = 1
    @ncols = 1
    @width = (width || DEFAULT_CANVAS_DIM).to_f
    @height = (height || DEFAULT_CANVAS_DIM).to_f
    @top_spacing = 5
    @bottom_spacing = 5
    @left_spacing = 5
    @right_spacing = 5
    @subplots = nil
    @figsize_unit = figsize_unit
    set_rubyplot_artist_coords!
    setup_default_theme
    add_subplots! @nrows, @ncols
end
```
Here, *height* and *width* represent the height and width of the canvas to be made respectively. *figsize_unit* is for saving the unit for measurement which are pixel, centimeter or inch (default is cm).  
Now, the Figure class inherits the Base class which initializes the origin for the figure in Rubyplot Coordinates which is set to 0,0.  
After this default values are given to the required variables which include *width* and *height* set at 40 cm, spacing variables are for the margin for the figure (in pixels), *nrows* and *ncols* are the number of rows and coloumns in which each (row, coloumn) represents a subplot, *subplots* variable is an 2-D array for storing subplots(which are axes objects), *title* is the title of the figure. Now, the function **set_rubyplot_artist_coords!** is called:
```ruby
def set_rubyplot_artist_coords!
  @max_x = 100.0
  @max_y = 100.0
  @min_x = 0.0
  @min_y = 0.0
  if @height > @width
    aspect_ratio = @height / @width
    @max_y = @max_y * aspect_ratio
  elsif @height < @width
    aspect_ratio = @width / @height
    @max_x = @max_x * aspect_ratio
  end
end
```
This sets the maximum and minimum coordinates for Rubyplot in Rubyplot coordinates and so the figure has an X and Y range as [0,max] for both X and Y coordinates in Rubyplot coordinates. Then this function defines the *aspect_ratio* and then changes the maximum value and the range for X or Y depending on the canvas height and width.  
  
After this, the function **setup_default_theme** is called which initializes some useful variables for colour of the figure:
```ruby
def setup_default_theme
  defaults = {
    marker_color: :white,
    font_color: :black,
    background_image: nil
  }
  @theme_options = defaults.merge Themes::CLASSIC_WHITE
  @marker_color = @theme_options[:marker_color]
  @font_color = @theme_options[:font_color] || @marker_color
end
```
  
Now the function **add_subplots!** is called which creates and initializes the *subplots* array:
```ruby
def add_subplots!(nrows, ncols)
  @nrows = nrows
  @ncols = ncols
  @subplots = Array.new(@nrows) { Array.new(@ncols) { nil } }
end
```
The *subplots* array is of dimension *nrows* x *ncols*.  

## Adding a subplot
After creating the figure, we move to adding subplots.