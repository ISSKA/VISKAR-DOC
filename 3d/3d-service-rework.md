# 3D Service rework
## Introduction
The current state of the Visual Karsys project has a problem. The 3D viewer, a component used to show 3D elements like meshes, drillholes, springs or cross-sections, is implemented as a static singleton.
The new feature added to Visual karsys, the meta project wizard, needs to show a 3d scene to preview elements. However, the use of the scene is restricted since it can be used only once at a time (behind the wizard and in the wizard)
Moreover, the implementation of this service is a bit if not very clunky. It lies on the use of the project tree and the project node and the majority of its functions and methods are way too dependant on other services.
The result of this problem shows particularly well in the animation module : the whole 3D viewer had to be copied and modified to be able to display animations.
To explain this rework, there will be some pseudo code displayed at different point of the explanation

## What to do
The idea is to rework the way the 3D viewer is implemented and to create an independent modular service which will simply draw what it is given.
The service will be able to accept additional features (such as the slice in the animation part) as "modules" simply added at its instanciation.

## How to do it
The solution is to create a generic and independent 3D viewer.
It will be seperated in different parts :
* The viewer
* The drawer Service
* The modular functionnalities

### The Viewer
The viewer element is "simply" a component which contains
* A canvas having the Three JS scene and its elements.
* A HUD component used to display actions (Vertical exaggeration, Views, etc...)

This component will receive a list of objects containing the necessary data for the scene to draw them and to add the correct action to the HUD.

```typescript
class 3DViewer {
    // List of elements to be drawn
    @Input() drawables: Drawable3D[]; 
    // Elements' 3D data
    objects: THREE.Object3D[];
    // Elements' states
    states: Map<string, State> = new Map()<string, State>;
    
    ngOnInit() {
        // Fill the 3D elements list
        this.drawables.forEach(drawable => this.objects.push(drawable.getObject()));
        // Create the states
        this.drawables.forEach(drawable => this.states.push(new State(drawable.getHUD().uuid, ...)));
        // Draw - pseudo code
        this.objects.forEach(object => THREE.draw(object));
    }
}
```

### The Drawer service
The drawer service will be the one to actually "draw" ; that is to use the Three JS functions to create the 3D elements.
In this service, multiple interfaces will be defined (Pseudo Code) :

#### State
The State interface is used to save the state of the 3D objects in the scene (the visibility, the opacity and other informations).
It will be stored in a map where the state is linked to an uuid of the object (since the object can change with the action of the user, we want the 3d data to be the same)

```typescript
export interface State {
  visibility: boolean;
  opacity: number;
    ...
}

const map = new Map<string, State>();
```

#### HUD
The HUD interface is used to store useful informations (UI, click on element, labels, ...) of the element

```typescript
export interface HUD {
  uuid: string;
  category: string;
  name: string;
}
```

#### Drawable3D
The Drawable3D is the interface which contains the important data for the viewer :
* How to draw it
* The informations needed for scene feature

```typescript
export interface Drawable3D {
  getObject(): THREE.Object3D;
  getHUD(): HUD;
}
```

#### Drawer-service
The drawer-service will be the one to know how to draw each element. The parent of the scene component will use this service to generate the wanted 3D objects

```typescript
export class Scene3dService {
    // Draw drillhole
    export function drillholeToDrawable(drillhole: Drillhole): Drawable3D {
      return {
        getObject() {
          return new THREE.Object3D()
        },
        getHUD() {
          return {
            uuid: 'xyz',
            category: 'Drillhole',
            name: drillhole.name,
          }
        }
      }
    }

    // Draw a simple cube
    export function cubeToDrawable(size: number): Drawable3D {
      return {
        getObject() {
          return new THREE.Cube
        },
        getHUD() {
          return {
            uuid: 'xyz',
            category: 'Drillhole',
            name: 'Cube',
          }
        }
      }
    }

    ...
}
```

### The modular functionnalities
The animation logic needs some small changes to be generic and modular. The next thing is to change the structure of the viewer component to be able to accept this logic and to add it dynamically.

The component will be able to receive a list of "functionnalities" which will be then added to the scene.

With this change, the animation viewer will be the same as the normal viewer ! Moreover, some specific functionnalities, such as the animation slice, will be usable in the normal viewers as well.

## Architecture
The new service will use the parts described above and it will be stored in a folder following this architecture :
- 3d
    - functionnalities
        - animation
            - shaders
        - hud
        - slice
        - ...
    - service
    - viewer3d
        - components

## Schemas
We can take a real example for the use of the 3DViewer.

![](https://i.imgur.com/n1u5Qk7.png)

Here we can see how the system is going to work. The parent, here "Setup Page / Output Page / ...", is going to give its objects to be drawn to the service to "transform" them into drawable items. The 3DViewer will simply receive this list and draw/use them correctly.

Furthermore, it is also here that the parent can define and give the additional functionnalities to the 3DViewer.

## Conclusion
Finally, by reworking the whole 3D service, Visual KarSys will be able to use and reuse the Three JS Scene for multiple case and even more.
The task is quite hard and might take some time, but is worth the trouble.
