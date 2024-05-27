# Migration
The migration is currently the biggest and most important task in the Visual KARSYS Project. The change between two apis is no small task.

Furthermore, when the migration itself will be complete, the work won't totally be finished. Here is what is needed to be done once the resources are all translated on the Spring API.

## Removal of bad code
Earlier in this documentation, bad concepts and approach were announced.
[M classes](../backend/mclasses.md) and [project-tree](../backend/project-tree.md) were explained. These concepts need to be removed and the API need to be RESTFul.

This include the use of jooq natural dtos and the cleanse of the code.

But most importantly, the scala code need to be removed to have a fully Spring API.

## Optimisation
Go through all the migrations done and homogenize the way it is implemented, optimise every small bit of the code to have a full fledged REST API.

## Adapt Frontend
When the M classes will be removed, the front end will have to adapt its logic to the new backend, the objects and methods present of the front end will have to change to honor the database and the backend. In a RESTFul relation, the front end bend the knee for the backend, not otherwise.

The whole project-tree structure also need to be removed from the frontend.
