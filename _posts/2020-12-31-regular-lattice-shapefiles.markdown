---
layout: post
title:  "Regular lattice shapefiles with geopandas"
date:   2020-12-31
categories: Python Geopandas Geospatial Visualisations
---

For a project a while ago I wanted to create shapefiles of regular lattices (as opposed to the SA1 / SA2 boundaries that often are used in Austraila). I created three notebooks for these: 
- The first two to create the shapefiles
- The third to do the transformation of data in one geometry to another (by taking a weighted average over intersecting polygons). This is useful, for example, when calculating population estimates in a new geometry.

They are available [here](https://github.com/alex2718/python_learning/blob/master/HexGrid.ipynb), [here](https://github.com/alex2718/python_learning/blob/master/RegularGridShapefiles.ipynb) and [here](https://github.com/alex2718/python_learning/blob/master/UniformPopulationEstimates.ipynb).

And here is a nice picture of a hexgrid you can create:

![A hexgrid covering Victoria](/assets/images/hexgrid.png)