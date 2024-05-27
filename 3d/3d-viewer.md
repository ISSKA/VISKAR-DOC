# 3D Viewer
The 3D Viewer is a component which displays a THREE JS Scene filled with Visual Karsys elements.

This viewer is designed to be adaptable and moudlable. This document will explain how it works

## Viewer
The viewer creates the scene and manage the base behaviours like the click in scene, camera movement, the drawing of objects, etc.

The particularity of this viewer, is that it doesn't know what will be drawn, it just takes objects given to it and draws them.

The same rule is applied to cameras, he doesn't know which camera is used, it receives a type of camera and apply it.

## Interfaces
To 'categorize' elements and add this logic of "not knowing" what is given to the drawer, interfaces were created :

### Drawable
The Drawable interface define an object to be drawn by the scene.

This interface is defined by 3 caracteristics :
- getObject() ; the method which describe the 3d object to be drawn in the THREE JS scene
- getLabel() ; the method which describe the label to be drawn in the THREE JS scene
- getLayer ; the field which tells in which layer the object is to be put

### LayerElement
The LayerElement interface define the 3D elements in the scene but for the visibility management.

This interface is defined by 5 caracteristics :
- object ; the 3d object itself
- label ; the label of the 3d object
- name ; the name of the object (used to search it easily in the scene)
- colour ; used for units (the colour token in the visibility hud)
- thumbnail ; used for objects with thumbnails (the thumbnail token in the visbility hud)

### VkCamera
The VkCamera interface define a camera and its behaviour.

This interface is defined by 5 caracteristics :
- getCamera() ; return the camera to be used in the scene
- getControls() ; return the specific controls for this camera
- getName() ; return the name of the camera (for the cameras hud)
- getIcon() ; return the icon of the camera (for the cameras hud)
- onRefresh(canvasWidth: number, canvasHeight: number) ; return the specific aspect change for the specific type of camera

## Services
The whole point of this component is that the scene doesn't know what type of object to draw, it just draws them by calling the getObject() function.

The thing is, the parent of the component prepares the objects and the cameras for the scene. The parent creates these objects by using services !

### Drawer service
The drawer service possess all the 'creation' method. For example, in the service, the "drillholeToDrawable" return a Drawable which contains all the data to draw a drillhole. The parent of the component 'just' need to call this functions and store it into a drawable list to be given to the scene.

### Camera service
The camera service has the same purpose as the drawer service. However, instead of drawables, the camera service creates VkCameras

### Scene utility service
The scene utility service possess functions which are used multiple times at different places. These functions are mainly utilities functions.

## Features
The viewer component has a HUD/UI to use specific features like layer visibility, label visibility, vertical exaggeration, etc.
This HUD is programatically created and is filled by the parent of the scene.

Wherever a viewer is created, the parent can chose which features it will possess. The hud is modulable !

Hence, multiple features are created and can be added to the scene

## Shaders
If the scene uses some shaders, there are created in this folder

## Finitions
Currently the 3D viewer can display a scene with basic objects of the Visual KARSYS web app and do possess the simple features the previous scene had !

However, the new 3D Viewer has not reached its full potential.
The point of this new component is to embrace all the possibilities and all the features the other scenes had ! Even the animation scene.

To completely finish the new 3D Viewer, we have to plug in the animation and the output pages features into the new component.
