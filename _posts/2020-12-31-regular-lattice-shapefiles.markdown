---
layout: post
title:  "Regular lattice shapefiles with geopandas"
date:   2020-12-31
categories: Python Geopandas Geospatial Visualisations
---

For a project a while ago I wanted to create shapefiles of regular lattices (as opposed to the SA1 / SA2 boundaries that often are used in Austraila). For example a lattice of hexagons where each hexagon has side length 1km. Or alternatively a lattice of squares. Furthermore, it is often useful to be able to estimate a quantity in a new geometry, based on the quantity is another geometry. For example, we may want to estimate the population in each hexagon based on the values in the intersecting SA1 or SA2 polygons.

I created three Jupyter notebooks to carry out these tasks:
1. The [first](https://github.com/alex2718/python_learning/blob/master/HexGrid.ipynb) generates a shapefile consisting of regular hexagons covering a particular area of interest;
1. The [second](https://github.com/alex2718/python_learning/blob/master/RegularGridShapefiles.ipynb) is similar to the above but contains code for generating both hexagonal and square lattices;
1. The [third](https://github.com/alex2718/python_learning/blob/master/UniformPopulationEstimates.ipynb) one shows how to estimate the population contained in each of the hexagons based on the 2016 Census counts at the SA1 level.

And here is a nice picture of a hexgrid, which always tend to look quite nice:

![A hexgrid covering Victoria](/assets/images/hexgrid.png)