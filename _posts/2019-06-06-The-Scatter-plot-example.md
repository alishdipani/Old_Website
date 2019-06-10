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
**P.S. - This blog is targeted twoards new developers who want to get familiar with the codebase of the library or anyone who is exteremely interested in technical code details as this blog is very technical and requires some familiarity with Rubyplot.**
P.S. - The version of library used for this example is of date 9 June.  
  
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
  p.marker_border_color = :blue # defining border colour of the markers
  p.marker_fill_color = :blue # defining fill colour of the markers
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
First Rubyplot is included which is similar to import in Python, this allows us to use Rubyplot.  Next we define 2 arrays *xdata* and *ydata* which store the X and Y coordinates to be plotted. So, the points to be plotted are (1,11), (2,2), (3,33), (4,4) and (5,65).  
  
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
After creating the figure, we move to adding subplots. To add a subplot the function **add_subplot!** is called
```ruby
axes = @figure.add_subplot! 0,0
```
But first we need to call the function **add_subplots!** to create the space for subplots i.e. initialize the *subplots* array and define the number of subplots. In this example 0,0 specifies that only 1 subplot is to be created and we have already created the space for it(during initialization of the Figure) and so we don't need to do it again.  
Now, the function **add_subplot!** is called for our figure object:  
```ruby
def add_subplot!(nrow, ncol)
  plottable_width = (@max_x - (@left_spacing + @right_spacing)).to_f
  plottable_length = (@max_y - (@top_spacing + @bottom_spacing)).to_f
  @subplots[nrow][ncol] = Rubyplot::Artist::Axes.new(
    self,
    abs_x: @left_spacing + (plottable_width / @ncols) * ncol,
    abs_y: @bottom_spacing + (plottable_length / @nrows) * nrow,
    width: plottable_width / @ncols,
    height: plottable_length / @nrows
  )
  @subplots[nrow][ncol]
end
```
This function takes input the row and coloumn for the subplot which is 1,1 in our case but is stored as 0,0 in the *subplots* array. First, *plottable_width* and *plottable_length* are calculated in Rubyplot coordinate units which define the total space measurements in which plotting is allowed i.e. incorporating the spacing for the figure. Then a new Axes object is made and is stored in the *subplots* array at the index 0,0. The function then returns the *subplots* array at index 0,0 which is stored in *axes* variable.  
  
## Axes
Now the *axes* variable contains an initialized Axes object which is also stored in *subplots* array at index 0,0. The Axes initialization is as follows:
```ruby
def initialize(figure, abs_x:, abs_y:, width:, height:)
  @figure = figure
  @abs_x = abs_x
  @abs_y = abs_y
  @width = width
  @height = height

  @x_title = ''
  @y_title = ''
  @top_margin = 5.0
  @left_margin = 10.0
  @bottom_margin = 10.0
  @right_margin = 5.0
  @title = ''
  @title_shift = 0
  @title_margin = TITLE_MARGIN
  @text_font = :default
  @grid = true
  @bounding_box = true
  @plots = []
  @raw_rows = width * (height/width)
  @theme = Rubyplot::Themes::CLASSIC_WHITE
  @font = :times_roman
  @font_color = :black
  @font_size = 15.0
  @legend_font_size = 20.0
  @legend_margin = LEGEND_MARGIN
  @title_font_size = 25.0
  @plot_colors = []
  @legends = []
  @lines = []
  @texts = []
  @x_axis = Rubyplot::Artist::XAxis.new(self)
  @y_axis = Rubyplot::Artist::YAxis.new(self)
  @legend_box_position = :top
end
```
The inputs are the figure to which this axes belongs which was given by self, axs_x and abs_y are the X and Y coordinate of this axes's lower left corner in Rubyplot coordinates and the *width* and *height* are the allowed space for this subplot i.e. the area in which this subplot is to be drawn.  
Other important variables initialized margin variables which define the margin for this subplot(axes object), *plots* array which stores the plots to be drawn in this subplot, *xaxis* and *yaxis* which are an object of *XAxis* and *YAxis* respectively.  
So, after initializing this axes object is stored in *axes* variable and *subplots* array at index 0,0.  

## X Axis and Y axis
We have initialized the variables *x_axis* and *y_axis* with new XAxis and YAxis objects, so when these objects are created, their constructor is called i.e. initialize function:
```ruby
# X Axis constructor
def initialize axes
  super
end

# Y axis constructor
def initialize(*)
  super
end
```
So, both these classes inherit the Base of Axis and in constructor of both of these classes, the constructor of Base is called:
```ruby
def initialize axes
  @axes = axes
  @title = ''
  @min_val = nil
  @max_val = nil
  @major_ticks_count = 5
  @minor_ticks_count = 2
  @texts = []
  @lines = []
  @major_ticks = nil
  @minor_ticks = nil
  @title_font_size = 25.0
end
```
This constructor sets various useful properties of X and Y axes to their default values. It takes the axes object as an input to store it into *axes* variable to store the axes object to which it belongs, next the *title* is set to empty and the *max_val* and the *min_val* are stored to nil which store the maximum and minimum value of the X and the Y axes, next the number of major and minor ticks (between two major ticks) are stored, then two arrays are initialized *texts* and *lines* (not actually used) to store the texts and lines (tick lines not actual axis lines) for the X and Y axes, then the variables *major_ticks* and *minor_ticks* are set to nil which are used as arrays to store XTick and YTick objects corresponding to major(and minor) ticks of X axis and major(and minor) ticks of Y axis respectively. Finally the font size of the title is set to 25 points.
  
## The Plot
Now, we have defined the subplot i.e. the axes object in which we want to draw our plot. The next lines of code are:
```ruby
axes.scatter! do |p| # Setting scatter as the type of subplot
  p.data @x1, @y1 # setting data to be plotted
  p.label = "data1" # defining label for the data
  p.marker_border_color = :blue # defining border colour of the markers
  p.marker_fill_color = :blue # defining fill colour of the markers
  p.marker_type = :circle # defining marker type
end
```
So, the function **scatter!** is called for the axes object. The block associated with the **scatter!** function is:
```ruby
p.data @x1, @y1 # setting data to be plotted
p.label = "data1" # defining label for the data
p.marker_border_color = :blue # defining border colour of the markers
p.marker_fill_color = :blue # defining fill colour of the markers
p.marker_type = :circle # defining marker type
```
The block is a block of lines having commands which in this example are used for specifying properties of the plot in the subplot. This block is given to the **scatter!** function as an input:
  
```ruby
def scatter!(*_args, &block)
  add_plot! :Scatter, &block
end
```
This function calls the private **add_plot!** function:
```ruby
def add_plot! klass, &block
  plot = Kernel.const_get("Rubyplot::Artist::Plot::#{klass}").new self
  yield(plot) if block_given?
  @plots << plot
end
```
The **add_plot!** function takes input as the class name for the plot which is Scatter in this example and the block is passed, then using Kernel a new object of the input class is created and is stored in *plot* variable which in this example is Scatter plot and so a *Scatter* object is created and stored in *plot* variable.  
Then yield executes the block if block is given i.e. block_given?==true. After executing the block i.e. creating the plot and setting up the characteristics of the blog, the *plot* is appended in *plots* array.  
  
## Scatter Plot
Now, before executing to the block, a Scatter object is initialized:
```ruby
def initialize(*)
  super
  @marker_size = 1.0
  @marker_type = :circle
  @marker_border_color = :black
  # set fill to nil for the benefit of hollow markers so that legend
  # color defaults to :black in case user does not specify.
  @marker_fill_color = nil
end
```
This first calls the base class which is inherited by Scatter, the initialize function is called. After that, different properties of the plot are set to default values which are stored in variables like marker size (set to 1), marker type (set to circle) which can be of different types like, traingle, plus, star, etc. (around 35 types available), marker border colour (set to black) and marker fill colour (set to nil). Now, the initialize function is:
```ruby
def initialize axes
super(axes.abs_x, axes.abs_y)
  @axes = axes
  @data = {
    label: '',
    color: :default
  }
  @normalized_data = {
    y_values: nil,
    x_values: nil
  }
  @stroke_width = 4.0
  @stroke_opacity = 0.0
end
```
This again calls the base class of artist which is inherited by this base class, the initialize function is called. After that, an *axes* variable is initialized to store the axes pointer, a *data* hash is initialized which stores different properties of the subplot like label and colour for the legend, a *normalized_data* hash is initialized to store normalised data (not used), stroke width and stroke opacity (i.e. border width and opacity) are initialized with values 4 and 0 respectively. The initialize function is:
```ruby
def initialize(abs_x, abs_y)
  @abs_x = abs_x
  @abs_y = abs_y
end
```
This initialized function sets the absolute values (i.e. in Rubyplot coordinates) of the origin of this axes object i.e. of this subplot (which is upper left corner for Magick backend).  
  
## The Block
Now, the scatter object has been initialized and the block will be executed which sets the data to be plotted and other properties of the scatter plot. The block is:  
```ruby
p.data @x1, @y1 # setting data to be plotted
p.label = "data1" # defining label for the data
p.marker_border_color = :blue # defining border colour of the markers
p.marker_fill_color = :blue # defining fill colour of the markers
p.marker_type = :circle # defining marker type
```
Here p is the scatter plot object. First the data is set to the *x1* and the *y1* arrays defined earlier:
```ruby
# Function in base of plots
def data(x_values, y_values)
  @data[:x_values] = x_values
  @data[:y_values] = y_values
end
```
This stores the *x_vales* and *y_values* in the *data* hash. Then the label of the scatter plot is set:
```ruby
# Function in base of plots
def label=(label)
  @data[:label] = label
end
```
This overwrites the value of *label* in *data* hash.  
Next, marker border colour is set which is a variable of scatter plot which describes the colour of thr border of the markers. So, in this example the colour is set to blue i.e. the variable *marker_border_color* is set to the symbol *:blue*.  
Next, marker fill colour is set which is a variable of scatter plot which describes the colour to be filled in the markers. So, in this example the colour is set to blue i.e. the variable *marker_fill_color* is set to the symbol *:blue*.  
Finally the marker type is set to circle i.e. the variable *marker_type* is set to the symbol *:circle*.  
  
## Axes properties
Finally after setting the scatter plot properties, the properties of the subplot i.e. the axes object are set:
```ruby
axes.title = "Nice plot" # defining title of the plot
axes.x_title = "X data" # defining title of X axis
axes.y_title = "Y data" # defining title of Y axis
```
Axes object has these variables *title*, *x_title*, *y_title* which store a string which defines the title of the subplot, title of X axis and title of Y axis respectively.  
  
## Writing the Figure
Till now we have only defined the properties and the data of the figure and have not actually plotted anything. Also notice that till now we haven't actually made of use of backend and have only used the frontend, one aim of Rubyplot is having a backend agnostic frontend i.e. frontend should not be dependent on backend.  
Now the we finally call the write function of the figure which actually draws everything and stores the figure as an Image:
```ruby
@figure.write("scatterplot.png") # Drawing the figure and saving it
```
Now, the write function of figure is:
```ruby
def write(file_name, device: :file)
  Rubyplot.backend.canvas_height = @height
  Rubyplot.backend.canvas_width = @width
  Rubyplot.backend.figure = self
  if ENV["RUBYPLOT_BACKEND"] == "MAGICK"
    set_background_gradient
  end
  Rubyplot.backend.init_output_device(file_name, device: :file)
  @subplots.each { |i| i.each(&:process_data) }
  @subplots.each { |i| i.each(&:draw) }
  Rubyplot.backend.write
  Rubyplot.backend.stop_output_device
end
```
