# Description
This document describes the structure of the 3D viewer used in Visual KARSYS, along with the processes required to use it. The aim is to clearly describe the purpose of each component and how to use them.

# Components and services
### Objects to 3D service
Given an object tree, transforms the geological entities in the tree into their 3D representations, resulting in a tree of 3D objects.

### Scene service
Manages the objects that are displayed in the 3D scene. Allows loading objects from a tree, which are converted using the Objects to 3D service before adding them to the 3D scene.

### 3D Viewer component
The 3D viewer component takes a scene and user interface service as input, and is responsible for providing DOM-level tasks such as providing a canvas and handling window resize events. It renders the scene provided by the scene service.

### Project 3D Viewer component
Responsible for providing a scene and UI service to the generic 3D viewer component, and loads the objects associated with the specified project.


# 3D User Interface
The user interface is managed by a dedicated service, which is responsible for maintaining the UI widgets and rendering them.

### Coordinates
UI coordinates follow the convention of screen coordinates, range 0..1, with "top-left" as (0, 0)
