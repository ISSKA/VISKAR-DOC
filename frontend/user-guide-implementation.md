User guide implementation
=========================

This describes how the user documentation content is stored and displayed. It also gives instructions on how it can be modified.

Documentation structure and storage
-----------------------------------
There are 3 types of elements in the documentation: models, tools and data. Each of those have a specific html template, which can be found in the files `model/tool/data-content.component` respectively. They all have in common a name, a description, some input elements and some output elements (although under various names). Finally, there is an additional field in the template: workflow, parameters or format, depending on the element nature.

The structure of the documentation is stored in a JSON file named `flowchart.json`. The latter contains the list of all elements as object with fields name, ID, element type and direct inputs/outputs. The details (description, workflow, parameters and/or format) are stored as HTML `<div>` in the respective template files.

Working principle
-----------------

The working principle is simple: parameters (element type and ID) are transmitted through the URL and the relevant content is displayed. In more details, the provided ID is used to fetch the element name and its inputs/outputs from `flowchart.json` (via `elements.service`). From there, a flowchart showing the elements and its previous/next steps is dynamically generated at the top of the page (through `graph.service` and `flowchart.component`).

Depending on the "element type" URL parameter, the relevant HTML template is displayed, with the exact content chosen via the "ID" parameter. Note that the list of elements in the left-hand size vertical menu is automatically generated, and so are the hyperlinks in the inputs/outputs fields of the template. If hyperlinks are to be present in the text, it is the developer responsibility to check them.

Content modification
--------------------

#### Adding/removing elements
In case of addition/suppression of an element, it must be done in the `flowchart.json` file first. Special care must be given to the ID field. One needs to make sure that all the references to the new/deleted ID are updated: lists of inputs/outputs must be checked and non-automatically generated hyperlinks too.

The details (description, workflow, parameters and/or format) must be directly edited in the template files. In case of a new element, a HTML `<div>` must be added with the relevant ID parameter, so that it is only displayed when needed, e.g.: `<div *ngIf="isElementActive('newElementId')">...</div>`. 

Note that the order of the elements in `flowchart.json` matters. Indeed, the dynamically generated menu on the left of the page follows the same order. It generally follows a chronological logic with respect to the KARSYS method.

### Adding images
Adding images can be simply done by updating the HTML code. The images themselves must be stored with the source code in the "models/images", "data/images" or "tools/images" folders, so that things are kept organized. Special HTML tags have been defined in the `custom.css` file, so that one can add images this way: `<figure style="width:55%"> <img style="width:90%" src="./images/Tool3_img1.png"> <figcaption>Figure 1: From surface DEM to ground corrected DEM</figcaption></figure>`. Here, the figure environement takes 55% of the available width (i.e. the caption is limited to that space too) and the image itself takes 90% of the latter. The heights are automatically adjusted. By default, there is only one centered figure by line. If one wants to change that (e.g. two figures side by side or not centered), this can be done by using the "style" property of <figure>. For all figures to have a consistent style, one should crop as much white space off the sides of the source images.