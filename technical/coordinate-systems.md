Coordinate Systems
==================

Object Coordinates
------------------

Object coordinates are stored in WGS84 format (longitute/latitude).
Object coordinates are independent of the project in which the object
occurs, and will simplify sharing objects between multiple projects.

Project Coordinates
-------------------

The project coordinate system is used internally for all project-level
models, simulations and visualisations. The project coordinates always
form a cartesian coordinate system, defined by the reference system
chosen by the user. The origin is assumed to be on the lower left edge.
The editing part of the frontend works mostly with project coordinates.

WebGL Model Coordinates
-------------------------

TODO move to code doc

The WebGL renderer uses project coordinates to place all geo-referenced
objects.

WebGL View Coordinates
----------------------

TODO move to code doc

The WebGL project coordinates are scaled so that the longest side has a
length of 1 and the project location is translated to the origin at
`(0,0,0)`.