# Abstraction

## Logic
The backend implementation uses a certain abstraction logic.
In most restful webapp, the controller, which contains the different routes to access the resources, uses services. These services call the database manipulations from daos. And the daos manipulates directly the data from the database through the use of the dtos which are the representation of the database elements.

In short :
- Controllers <=> Services <=> Daos <=> Dtos

However, we have added a bit of complexity with a level of abstraction. In Visual KARSYS, every users work with projects. A project is a unit which will contain data, models and other form of information. A user possess multiple projects with each their specific datas : location, series, points of interests, etc.

We then added a level abstraction based on the depth of the project. This abstraction is used to prevent us from creating the same functions every time we have a new resources to implement.

For example, if we take the 'ProjectController' which allows us to get, set, update or even delete projects resources. Since the project is a the top of the 'depth', the 'ProjectController' inherit 'AbstractController' and the routes do not require much informations to be used : the project id and/or the project itself.

But if we take the 'CrossSectionController' which allows us to manipulate CrossSections, we increase in depth ! The CrossSections belong to a certain project, so a projectId is needed. The controller then inherit from the 'AbstractProjectSubpartController'.

This logic is also applied to services and daos.

We can finally take an extreme case : the formations. The formations are children to units which are children to series which are children to column which are children to project ! The abstraction is at level 4 !
