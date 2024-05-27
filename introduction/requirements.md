
Requirements
============

VisualKARSYS is a web application. It should run in every modern web browser without the need to install any additional plugin or extension. Its function is to guide and assist the licensed user through the KARSYS method. It does so by providing a description of and tools relevant to the method and a user interface.

Description of the KARSYS method
================================

The description of the KARSYS method is provided to the user via text and images displayed in the web page. The user should be able to easily access the parts of the description relevant to the tool he is using. The user should also be able to access a complete description of the method at any time in the form of a downloadable file, or directly online via a series of static pages. This should effectively be a user manual for the KARSYS method (and not directly for the VisualKARSYS application).

Tools relevant to the method
============================

The tools relevant to the method have been grouped according to five major steps in the KARSYS method called *models*. The creation of each model is done using import and data transformation tools. Then, data can be 'extracted' from a model using data transformation and export tools.

Hydrostratigraphic model tools
------------------------------

The tools relevant to the creation of the hydrostratigraphic model should be able to perform the following tasks.

### Import and store stratigraphic columns

The user should be able to store stratigraphic columns source files in their specific file format. This should not be a
necessity as the user could prefer working with local files. It is simply a tool designed to help the user keep track of the files linked to his projects.

### Input stratigraphic entities from files

The user should be provided an interface to input the stratigraphic entities from the stratigraphic columns in their image format. The stratigraphic entities are called *formations*, are grouped into units, which in turns are placed in series. The tool would ideally consist of a viewer for the image file, a viewer for the resulting stratigraphic column and forms to add or edit the column.

-   The image viewer should allow the user to display an image or pdf file in a container for reference. The source of the file can either be the user’s local folders or the Visual KARSYS servers.

-   The column viewer displays the current state of the stratigraphic column in an intuitive and clear way. Moreover, it should be helping with the editing with interactions such as click to select and drag and drop.

-   The editor allows the user to edit existing formations, units and series as well as create new ones. The form proposed depends on the currently selected element in the columm Formations are caracterized by a formation name, a chronostratigraphy, a code, a lithology and if it is aquiferous or not. The formation name is chosen freely by the user. The chronostratigraphy can be picked among a list of eons, eras, periods, epochs and ages agreed uppon beforehand by ISSKA. The interface to do so should mix a kind of auto-complete from string with a picker allowing users to precise their selection. We note that the chronostratigraphy is not neede for the KARSYS method to suceed but it can server as reference for the user and allow more accurate user guidance during the process.

### Export hydrostratigraphic entities

The user should be provided with a tool to extract and then export the hydrostratigraphic or the stratigraphic entities for further use in a geological modeling software.

3D geological model tools
-------------------------

The tools relevant to the creation of the 3D geological model should be able to perform the following tasks.

### Import hydrostratigraphic entities

The user should be provided with a tool to import hydrostratigraphic entities. These can be found in the form of an hydrostratigraphic column.

### Borehole logs

The user should be able to import borehole logs in their specific file format. This should not be necesary as the user could prefer working with local files. It is simply a tool designed to help the user keep track of the files linked to his projects.

### Import geological profiles

The user should be able to import geological profiles in their specific file format. This should not be necesary as the user could prefer working with local files. It is simply a tool designed to help the user keep track of the files linked to his projects.

### Import maps

The user should be able to import maps in their specific file format.

### Import digital elevation models

The user should be able to import digital elevation models in their specific formats.

### Transform / correct digital elevation models

The user should be provided with a tool allowing specific transformations of the digital elevation models to be performed.

### Transformation in 3D geological data

The input geological source data

### Export to Geomodeler

The user should be provided with the tools necessary to export their geological contacts and orientations and hydrostratigraphic pile for further use as input in Geomodeler.

### Import 3D geological data

The user should be able to import already existing 3D geological data from other projects or from local XML files.

### Export 2D geological grids

The user should be provided with a tool allowing the export of 2D geological grids. The types of grids that should be obtainable should be decided depending on the needs of the target market.

3D hydrogeological model tools
------------------------------

The user should be able to generate a 3D hydrogeological model using the Visual KARSYS platform.

### Import 3D geological shapes

The user should be able to import 3D geological models that he can use to perform KARSYS. The supported file formats have to be determined depending on the target market’s preferences.

### Import / input springs

The user should be able to import springs from different file formats (like CSV) or input them directly on the website using a dedicated graphic interface.

### Compute water base-level from borehole logs, cave maps and spring data

Using all the hydrogeological information inputted by the user, the application should be ale to find at least one water base-level.

### Create the 3D hydrogeological model

The application should create a 3D mesh of the flooded zone.

### Export / extract 3D shapes of the flooded zone

The application shoud allow the user to export the 3D shapes of the flooded zone. Formats will depend on the preferences of the target market.

