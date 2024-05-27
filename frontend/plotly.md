Plotly technical documentation
===============================
This section describes the way Plotly is used within Visual Karsys. For a more in depth description or for a plotly tutorial please visit: https://plot.ly/javascript/ .

### Introduction

Plotly is an open-source library for many platform (python, js, etc..) that allow the display of data with usual plotting tool like surface, contour, scatter plot etc..  This tool is used within visual karsys to both display data directly but also to produce result and export them as images.

### The two usage of plotly

1. Slice display
The slice are a represented on the 2D map (so from above) by a line within the project boundaries. The final display is the slice seen by the side in a fashion akin to the cross-section themselves within geoPicker. To be able to display the slice a request is sent to the server with two coordinates: the start of the line and the end of the line. Then a python script compute the geological unit id of a predetermined (usually 500) number of points sampling both the x and y axis (so a 500 X 500 images). The output is a 500X500 matrix containing the geological unit composition.

To display this matrix in a good looking manner plotly is used. A contour plot is the best suited, however there is two things to change in addition to simply plotting the matrix.
    
* Adapting the x and y axis to correspond not to the sampling point index (0-500) but to height and width.

* Creating a custom colour scale so that the rank value correspond to the right colour (a precise comment on how is within the code itself).

Once this is done the image appear immediately, just remember to add a ChangeDetectorRef because otherwise plotly does not detect the data modifications. The slices are computed at each new session (within the output page) and never stored.

2. Geological unit jpg production
The second use of plotly is to directly produce jpg while never displaying them directly. The thing is that to produce an image plotly still need to display it on the exact same style and then save it.  This is achieved with a small trick, a html div is still devoted to plotly for displaying the geological units before saving them. However it is reduced to one pixel and immediately destroyed after image are saved. This means that there will although we will never notice this one pixel changing for a few seconds the html div have to be visible (it can't even be hidden by an ngIf).

The pltoly graphic is produced in a different way than the slices. Slices are produced directly with a 2D matrix. However the geological units are meshes and have to be preprocessed before obtaining a 2D image. The way it is done is through the Three library (the library is used to handle the mesh within the 3D viewer). With Three we set the camera to be on top of the mesh and then read every pixel that can be seen.  Moreover, since the height of the pixel is needed and not the colour we have to replace it by the height so the right value is retrieved. Once this is done we end up with a 2D matrix and the exact same procedure than for slice is applied.

Since the geological units will change each time geoData is added or deleted it is not possible to save them. Thus every time a new output page is started every image is produced and used during this very session. Note that since we can choose not to display the units, they are all computed but they are not all used. The image are produced and wait to be used during the session.