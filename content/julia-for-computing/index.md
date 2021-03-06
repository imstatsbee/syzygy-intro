---
date: 2016-03-09T19:56:50+01:00
title: "Julia for Computing"
weight: 40
---

## Julia for Computing

The Julia computing language is a relative newcomer to the compute world that is
proving to be very powerful and effective for scientific computation. It aims to
combine the flexibility and ease-of-use of am interpreted, dynamic language
(like Matlab) with the speed and efficiency of a compiled, statically typed
language (like C or C++). 

There are many online resources for learning the Julia language. A good place to
start is the documentation provided by the Julia developers at:
http://docs.julialang.org/

A good resource for new users is the [Introducing Julia
wikibook](https://en.wikibooks.org/wiki/Introducing_Julia)

There are many software packages that have been developed for Julia to do many
common computational tasks. The list of registered Julia packages can be found
at http://pkg.julialang.org. 

This chapter focuses on how to use Julia within a Jupyter notebook. Doing simple
calculations in Julia is very straightforward. We show how to make a few plots,
and how to build rich graphics files. 

 A couple of caveats: The Julia language is still under development, and so some
 functionality is a bit shaky. For instance, loading a new package
 (Pkg.add("whatever")) often will fail. Also, the "binder button" in this book
 will not run Julia code for you. 

## Plotting with PyPlot

In Julia, the PyPlot package will be most familiar to users of Python's PyPlot
package, as well Matlab users.  Unlike Python, no "magic" commands are needed to
get Julia to plot onto the Jupyter notebook page.

To learn more about the PyPlot package for Julia, refer to the Github
repo:https://github.com/JuliaPy/PyPlot.jl

To start, begin with the "using" command to inform Julia that you wish to use
the PyPlot package.

```julia
using PyPlot
```

To plot an array of numbers, simply use the "plot" command. 

```julia
plot([1,2,3,4])
```

![png](/img/output_3_0.png) 


To plot y versus x values, put them both into the plot command.

```julia
x = linspace(-3, 3)
y = x.^3 - 3*x
plot(x,y)
```


![png](/img/output_5_0.png)

To plot a surface, create your x and y variables, expand to a grid, and use this
to define the z values on the surface

```julia
n = 100
x = linspace(-3, 3, n)
y = linspace(-3,3,n)

xgrid = repmat(x',n,1)
ygrid = repmat(y,1,n)

z=exp(-xgrid.^2 - ygrid.^2)

plot_surface(x,y,z)
```


![png](/img/output_7_0.png)


You might like to color your surface plot with a color map:

```julia
plot_surface(x,y,z,cmap="jet")
```

![png](/img/output_9_0.png)


To plot this surface instead as an image, use the "imshow" command, which is somewhat like "imagesc" in Matlab.


```julia
imshow(z)
```


![png](/img/output_11_0.png)


## Plotting with Gadfly

The Gadfly package for Julia is a very nice plotting package that allows
interactive control of the plots from within the notebook. According to the
documentation, it is based largely on Hadley Wickhams's
[ggplot2](http://ggplot2.org/) for R and Leland Wilkinson's book [The Grammar of
Graphics](https://www.cs.uic.edu/~wilkinson/TheGrammarOfGraphics/GOG.html). It
was Daniel C. Jones' brainchild and is now maintained by the community.

Details on the package are here: http://gadflyjl.org/stable/index.html

To start using Gadfly, use the "using" command to inform Julia that you want to
use this package. 

```julia
using Gadfly
```

Use the plot command to plot x and y values. Clicking on the graph gives you
access to controls to pan and zoom.

 ```julia
plot(x=[1,2,3,4,5,6],y=[1,4,9,16,25,36])
```

![png](/img/Gadfly1.png)

A few details line lines and points can be added explicitly to the plot, as
follows. 

```julia
plot(x=[0,1,2,3,4,5,6], y=[0,1,4,9,16,25,36], Geom.point, Geom.line)
```
![png](/img/Gadfly2.png)

Gadfly even knows how to plot functions directly, as in this example. 

```julia
plot([sin, cos], 0, 30)
```
![png](/img/Gadfly3.png)

Gadfly excels at plotting data from large data sets. Here we load in some
standard R data sets and plot the information therein. 

```julia
using RDatasets
plot(dataset("datasets", "iris"), x="SepalLength", y="SepalWidth", Geom.point)
```

![png](/img/Gadfly4.png)

An alternate way is to assign a variable with a particular data set (the iris
data set, which is information about a collection of flowers) and plot. 

```julia
iris = dataset("datasets", "iris")
plot(iris, x=:SepalLength, y=:SepalWidth, color=:Species, Geom.point)
```

![png](/img/Gadfly5.png)



## High resolution graphics into files

Very often we need to create some high resolution graphics in a computing
environment and then export it in a file, to use within some other application.
For instance, a book may need a high quality diagram that is best created by
some algorithm.

This section demonstrates a simple technique to do this.

The basic idea is to create a Scalable Vector Graphics file (of type .svg) and
fill it with text commands that indicate what to draw for the graphics image.
You can read more details of all that can be draw here:
https://en.wikipedia.org/wiki/Scalable_Vector_Graphics

{{< warning title="Browser Caching" >}}
WARNING: images displayed are often "cached" by your browser (Firefox, Chrome,
Safari, etc), which means the file is pre-loaded for convenience. However, if
your Jupyter code changes the .svg file, the browser does not know to re-draw
the new image. One way to force it to draw the new image is to re-load the page.
(Remember to save first.)
{{< /warning >}}

### Example 1 - Two lines

In this example, we will simply draw a couple of lines. The file will have four
text entries. The first entry marks the  file as an svg file, the last entry
indicates the end of the svg file. Two entries in the middle draw two lines. The
text will look like this: 
```
<svg xmlns='http://www.w3.org/2000/svg' version='1.1' width='100%' height='100%' viewBox='-1  -1 2 2'>
<line x1='-1' y1='1'  x2='1' y2='-1' stroke='black' stroke-width='.01' />
<line x1='-1' y1='-1' x2='1' y2='1' stroke='red' stroke-width='.01' />
</svg>
```

We use the "open" command in Julia to open a file, and write into it with the "write command." Note we use a "do" look to ensure that the file is closed once the writing is all done. 


```julia
open("simplegraphics.svg","w") do f
    write(f,"<svg xmlns='http://www.w3.org/2000/svg' version='1.1' width='100%' height='100%' viewBox='-1  -1 2 2'> \n")
    write(f,"<line x1='-1' y1='1'  x2='1' y2='-1' stroke='black' stroke-width='.01' /> \n")
    write(f,"<line x1='-1' y1='-1' x2='1' y2='1' stroke='red' stroke-width='.01' /> \n")
    write(f,"</svg>")
end
```

To display the file, we use either of these two Markdown commands:
```
![The Simple Graphics](simplegraphics.svg)
<img src="simplegraphics.svg" width="500" height="500">
```
The second choice gives you a bit more control over the size of the output. 

![The Simple Graphics](/img/simplegraphics.png)

<img src="/img/simplegraphics.png" width="500" height="500">

### Example 2 - A star is born

Here is a slightly more interesting example. We draw a five-pointed star by
connecting lines across 5 evenly spaced points on a circle. This is probably
more fun to do algorithmically like this, rather than trying to do a freehand
drawing of a star. 


```julia
open("star.svg", "w") do f
    write(f,"<svg xmlns='http://www.w3.org/2000/svg' version='1.1' width='100%' height='100%' viewBox='-1  -1 2 2'> \n")
    for i in 1:5
        x1 = sin(i*2*pi/5)
        y1 = -cos(i*2*pi/5)
        x2 = sin((i+2)*2*pi/5)
        y2 = -cos((i+2)*2*pi/5)
        write(f,"<line x1='$x1' y1='$y1'  x2='$x2' y2='$y2' stroke='black' stroke-width='.01' /> \n")
    end
    write(f,"</svg>")
end
```
<img src="/img/star.png" width="200" height="200">

### Example 3 -  Mathieu Spectra

We construct spectra for the almost Mathieu operator, also known as the
Hofstadter Butterfly. https://en.wikipedia.org/wiki/Hofstadter's_butterfly

Personally, I've done this before using MATLAB, taking a lot of care to make it
fast. Here in Julia, it seems to be fast even without any special tricks. In
particular, I leave the matrices U,V, in their natural form, and compute
directly in their full matrix form.

This code creates a file mathieu.svg which can be opened directly in Jupyter Hub
using the browser. In fact, the image opened separately looks better than the
display here. 


### The Math

The almost Mathieu operator describes the energy levels of an electron moving in
a periodic crystal (2D), under the influence of a magnetic fields. This causes
only certain energy levels to be allowed, depending on a parameter

$$\mu = e^{2\pi i \theta}.$$

We take two universal unitaries $u,v$ which satisfy a commutation relation

$$ vu = \mu uv.$$

The related energy operator (almost Mathieu operator) is the linear, self
adjoint operator

$$h = u + u^* + v + v^*.$$

The curious thing is that the spectrum of $h$ is either a union of intervals,
or a Cantor set, depending on whether $\theta$ is rational or irrational.
Which seems odd in physics, that a tiny perturbation in one parameter should
give such a fundamental change in the nature of the spectra. 

In the case where $\theta = p/q$ is rational, we can compute the spectrum
exactly.

We define here two unitaries $U$ and $V$ that are $q\times q$ matrices.
$U$ is a diagonal matrix, while $V$ is a permutation matrix. They satisfy
the fundamental intertwining identity

$$ VU = e^{2\pi i \theta} UV, \mbox{ where } \theta = p/q.$$

Replacing $U,V$ by a scalar multiple $z_1U, z_2V$ will satisfy the same
commutation constraint. Setting $z_1 = z_2 = 1$ gives one extreme set of
spectral points, that form one-half of the interval endpoints that make up the
spectrum. Setting $z_1 = z_2 = e^{2\pi i/q}$ gives the other extreme set,
forming the other set of endpoints. 

### The code

The matrix $H$ defined below is the self adjoint sum of these unitaries, and
$L$ has constants thrown in to get the other extreme points of the spectra. 

We compute the eigenvalues, then throw them into a file that plots all the
lines. 


```julia
U(p,q) = diagm(exp(2*pi*1im*(1:q)*p/q))
V(p,q) = circshift(eye(q),(0,1))
H(p,q) = U(p,q)+U(p,q)' + V(p,q)+V(p,q)'
L(p,q) = exp(pi*1im/q)*U(p,q)+exp(-pi*1im/q)U(p,q)' + exp(pi*1im/q)*V(p,q)+exp(-pi*1im/q)*V(p,q)'
eigH(p,q) = eig(H(p,q))[1]
eigL(p,q) = eig(L(p,q))[1]


function printaline(f,xone,xtwo,y)
    y8 = y*8
    print(f,"<line x1='$xone' y1='$y8' x2='$xtwo' y2='$y8' stroke='black' stroke-width='.01' /> \n")
end

f = open("mathieu.svg","w")
print(f,"<svg xmlns='http://www.w3.org/2000/svg' version='1.1' width='100%' height='100%' viewBox='-4  0 8 8'> \n")
printaline(f,-4,4,0)
printaline(f,-4,4,1)
for q=2:50
    for p= 1:(q-1)
        if gcd(p,q)==1
            eig1 = eigH(p,q)
            eig2 = eigL(p,q)
            for k=1:q
                printaline(f,eig1[k],eig2[k],p/q)
            end
        end
    end
end
print(f,"</svg> \n")
close(f)
```

We now display the spectra using the image command in Markdown language.
```
<img src="/img/mathieu.svg" width="600" height="600">
```

<img src="/img/mathieu.png" width="600" height="600">


## Animation in Julia. 

Animated plots in Julia can be created using the matplotlib tools that we know
from Python.

The idea is the same. There are three steps to creating an object that will
contain the animation:

  - draw the empty figure
  - define an init function, and an animate function, that will draw the successive frames
  - create the animation using the FuncAnimation fucntion (which calls init and animate)

This object is then saved as an MP4 file, which can then be played in the
notebook using an html command.

For more ideas on animation, take a look here: 
 http://nbviewer.jupyter.org/github/tom26/JuliaFun/blob/master/2D%203-Body%20Problem.ipynb

Let's do a simple animation, drawing 3 lines across a plot. 

First, tell Julia the tools we need to load in:

```julia
using PyPlot
using PyCall
@pyimport matplotlib.animation as anim
```

We first construct the empty figure. For convenience, we define 3 global data
structures called lines. 


```julia
#Construct Figure and Plot Data
fig = figure("MyFigure",figsize=(10,10))
ax = axes(xlim = (0,10),ylim=(0,10))
global line1 = ax[:plot]([],[],"r-")[1]
global line2 = ax[:plot]([],[],"g-")[1]
global line3 = ax[:plot]([],[],"b-")[1]
```

![png](/img/J_Animation1.png)

We now define the init function, which sets the data for the first frame


```julia
# Define the init function, which draws the first frame (empty, in this case)
function init()
    global line1
    global line2
    global line3
    line1[:set_data]([],[])
    line2[:set_data]([],[])
    line3[:set_data]([],[])
    return (line1,line2,line3,Union{})  # Union{} is the new word for None
end
```

Next is the animation function, which will draw each frame of the animation
using index i.

In this case, we just draw three lines. The first is along the line x=y, going
from point (0,0) to (i/10,i/10).


```julia
# animate draws the i-th frame, where i starts at i=0 as in Python
function animate(i)
    global line1
    global line2
    global line3
    x = (0:i)/10.0
    line1[:set_data](x,x)
    line2[:set_data](1+x,x)
    line3[:set_data](2+x,x)
    return (line1,line2,line3,Union{})
end
```

Now we create the animation object by calling the Python function FuncAnimaton.


```julia
myanim = anim.FuncAnimation(fig, animate, init_func=init, frames=100, interval=20)
```

This is converted to an MP4 movie file and saved on disk in this format.

```julia
myanim[:save]("3Lines.mp4", bitrate=-1, extra_args=["-vcodec", "libx264", "-pix_fmt", "yuv420p"])
```

Finally, we display the movie in a Julia cell as follows. Note it has animation controls for the user. 

```julia
# Function for creating an embedded video given a filename
function html_video(filename)
    base64_video = base64encode(open(readbytes, filename))
	"""<video controls src="data:video/x-m4v;base64,$base64_video">"""
end

display("text/html", html_video("3Lines.mp4"))
```


<video controls src="data:video/x-m4v;base64,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">



## Seismic Julia Package

The Seismic Package for Julia is under development by SAIG (Signal Analysis and
Imaging Group) at the University of Alberta. This module provides tools to read,
write, process, and plot 3D reflection seismic data. 

We use this as an example of how to use a large, external Julia package in the
syzygy system.

To get started, you first notify Julia that you will be using the two packages
PyPlot and Seismic. Enter the following command:


```julia
using PyPlot, Seismic
```


If all goes well, this will load in the required packages.

However, a few common problems may arise. 

First, the packages might not be found in the current path. In this case, you
need to run `Pkg.add("PyPlot")` to install the PyPlot package. You may alos need
to run `Pkg.add("Seismic")` to install the Seismic package.

These commands are entered like this (but don't use them unless you got the
error message):

```julia
Pkg.add("PyPlot")
Pkg.add("Seismic")
```
Once you've added the packages, you should again use the command
```julia
using PyPlot, Seismic
```

The second problem you may have is that during the "using" command execution,
Julia may have trouble precompiling the modules (you will get an error message).
Typically this is due to some incompatibility with the "Compat" module. This is
a known problem which hopefully will be fixed soon. A work-around is to save
your Notebook, close the Jupyter Server, then restart the Server. This seems to
load in the compiled packages appropriately.

A third problem you may have is that these are large packages, and the 1
GigaByte of filespace you have been allotted may not be enough for all this. One
solution is to get rid of any excess files you no longer need. (Just delete
them.) Another solution is to request from your administrator a larger filespace
allocation. 

You are now ready to use Seismic Julia.


First, we use the "download command" to grab some data from the United States
Geological Survey webserver, as follows:


```julia
download("http://certmapper.cr.usgs.gov/nersl/NPRA/seismic/1979/616_79/PROCESSED/616_79_PR.SGY", "616_79_PR.SGY");

      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
      0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    100 11.3M  100 11.3M    0     0   189k      0  0:01:01  0:01:01 --:--:--  113k


    number of traces: 1908
    number of samples per trace: 1500
```

This creates a new file on you server, named "616_79_PR.SGY" which is 11
Megabytes of data. 

Use the "SegyToSeis" command to convert this SEGY-style file into a .seis file
(the native file type for the Seismic Julia package). 


```julia
SegyToSeis("616_79_PR.SGY", "616_79_PR.seis");

    number of traces: 1908
    number of samples per trace: 1500
```


This creates a new file called "616_79_PR.seis" which also is about 11 Megabytes large. 

We next read the file, and extract three data structures:


```julia
d, h, e = SeisRead("616_79_PR.seis");
```

Finally, we plot the data in these data structures using "SeisPlot":


```julia
SeisPlot(d[1:500, :], e, cmap="PuOr", wbox=9);
```

![png](/img/J_Seismic1.png)


This view is a cross-sectional image of the earth showing some interesting
geological layers sloping down to the right. It was constructed from a seismic
experiment using vibrational waves to explore the earth's subsurface. 

To learn more about the many utilities available in Seismic Julia, refer to the
source pages at: https://github.com/SeismicJulia
