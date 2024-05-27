# Angular workflow

Visual KARSYS is implemented with the framework Angular and in the language typescript.

If you know how to work with Angular, then this document is not for you. Otherwise, the rules listed below will help you to understand the basics of Angular structure.

The way to implement an Angular based web application is easily learned in the documentation given by Angular themselves. Here is only taught the structure of the files and how we work for Visual KARSYS implementation.

## Components
Angular works with what we call a component. A component is a reusable HTML5/Angular part with javascript logic linked to it. For example, in the trello website, component would be this :
- List ; A column with alterable caracteristics which can be created at will. Furthermore a list possess cards components to display
- Card ; The card is present multiple times but with different values.

A component is a template with a logic bound to it.

In Angular, every website page is a component and possess multiple component.

### Creation
In Visual KARSYS a component has :
- A javascript/typescript file named "componentName.component.ts" which holds the logic of the component
- A html file name "componentName.component.html" which holds the visual template of the component

## Services
To access backend resources, services are created. A service is an accessible element which possess functions linked to APIs routes and return or manipulate the wanted resources.

Like the [backend abstraction](../backend/abstraction.md), the front end services do have an abstraction but a bit different. The point is to use it to simplify the creation of a service (we don't want to implement the same methods again and again).

## Modules and routing

### Modules
Each component needs to be defined in a module. For each 'part', such as a whole page or a specific component, a module is a hub where all the components, packages and imports are written. If you want to use a component or show a components to other parts, it must be written in the module.

### Routing
To define the front end routes, you need to tell the front end routers which component are shown to which routes. A given route will be linked to a specific component which will act as the web page.
