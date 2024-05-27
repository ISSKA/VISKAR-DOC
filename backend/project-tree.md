# Project Tree
Currently, the web application Visual KARSYS is not RESTFul. Everytime the website is loaded, a huge structure named project tree is loaded. This project tree consists of all the datas possible. The projects the user can access, theirs datas, their functionnalities and so on.

We load everything at one point, and for every changes, we update the backend AND the frontend. The point is to get rid of this structure and use a RESTFul approach with asynchronous request to the backend.

To create a link with previous chapters, the project tree uses [M classes](mclasses.md) so its content can be used by the front end.

The project tree is, like the name suggests, a tree structure with node created by type. DrillholeNode will contain all the drillholes and their datas.
