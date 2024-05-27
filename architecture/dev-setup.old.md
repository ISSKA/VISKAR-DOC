# Development environment setup
This section describes the setup necessary to get started with developing Visual Karsys.

## Version control

The source code for Visual KARSYS is managed in a Git repository. To access the repository, you can use the Git command line, or alternatively a UI such as [SourceTree](http://sourcetreeapp.com).
* Repository URL: https://github.com/ISSKA/VISKAR.git
* Check out the **develop** branch before continuing.

## Code-Style
We use an [.editorconfig](../../.editorconfig) to specifying our Code-Style in the projects. 
It is supported in most IDEs see https://editorconfig.org/#download and it can be changed depending on the project or programming-languages used.  
For **IntelliJ** you do not have to do anything as it is already supported out of the box.  
For **Visual Studio Code** you need to install a plugin https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig.
You may also need to install the editorconfig globally. `<npm> install -g editorconfig` or `yarn global add editorconfig`

## PostgreSQL/PostGIS/plpython3u Setup<a name="db-setup"></a>

### On Windows
1. Install PostgreSQL 9-12 (x64) version >= 9.6.8 (see<https://www.enterprisedb.com/downloads/postgres-postgresql-downloads>)
1. A psql prompt should have been installed as well as PostgreSQL, this prompt should be used used for all database related command. The windows original prompt does not understand psql requests
1. Install PostGIS >=3.0.1  (Choose the version corresponding to your PostgreSQL version and windows <https://download.osgeo.org/postgis/windows/>)

### On Linux
1. Install PostgreSQL 9-12 version >= 9.6.8, PostGIS >=3.0.1 & plpython3u (postgresql-plpython3). You can use your favorite package manager.
1. To configure the database you can follow these steps https://wiki.archlinux.org/index.php/PostgreSQL#Initial_configuration & 
https://wiki.archlinux.org/index.php/PostgreSQL#Create_your_first_database/user

### Both
1. Within the psql prompt, as an admin user, execute the following command:`CREATE ROLE viskar WITH LOGIN PASSWORD '************' SUPERUSER;`
1. Currently, viskar doesn't have any DB; the creation of the developpment DB and the test DB will be discussed in [“DB Initialization”](#db-init)

### Useful tools
* Database Tool https://dbeaver.io/  

### Useful commands in psql
* Connect to a different database: `\c <database name>`
* Execute a SQL script: `\i <script name>`
* Show information about a table: `\d <table name>`

## Frontend Setup
**Recommended IDE: Visual Studio Code**  
**Debugging:** In VS Code, install the Debugger for Chrome to enable debugging the TypeScript code. Alternatively, the browser's developer tools may be used, but may not support source maps 
(mapping the JavaScript at runtime to the TypeScript source).  
We use Yarn for package management. The packages must be installed before running the front-end.
1.  Install yarn from <https://yarnpkg.com/en/docs/install>

2.  Select the “<REPO>/src/client” folder

3.  In the command line, run `yarn`. This installs the required node packages.

4.  In the command line, run `yarn start` to run the development server

## Backend Setup
**Recommended IDE: IntelliJ IDEA**

1.  Install the Java Development Kit (JDK) 11 or newer

2.  Install an integrated development environment (IDE). IntelliJ IDEA
    by Jet Brains is recommended. The Community Edition should suffice.
    All further steps assume IntelliJ IDEA.

    1.  Install the Scala plugin for IntelliJ. This can be done on first
        startup or from the splash window, clicking on “configuration”,
        then “plugins”.

3.  Click on “Import project”

4.  Select the “&lt;REPO&gt;/src/server” folder

5.  Select “Import project from external model” - “SBT”

6.  Click on “Finish”

7.  Click on “Ok” in the “SBT Project Data To Import” dialog

### Database Initialization<a name="db-init"></a>
#### Command line
1. from the src/server folder run `sbt "admin/run setup-dev-env"`

##### IntelliJ
All database setup actions are performed through the `viskar-admin` utility in the backend.
This includes database migrations and creating test users. To setup the backend development environment,
carry out the following steps in IntelliJ IDEA:

1.  Go to the menu “Run” - “Edit configurations”

2.  In the “Run/Debug Configurations” window, press the plus button and
    select “Application”.

3.  Optionally, give a name to the task to remember what it is

4.  Set “Main class” to “ch.isska.viskar.admin.Main”

5.  Set "Use classpath of module" to "viskar-admin"

6.  Set "Program arguments" to `setup-dev-env`

7.  Press “Ok” to save the configuration

8.  Run the task. This can be done from the “Run” menu.

### Running the REST API
#### Command line
1. from the src/server folder run `sbt "restAPI/run"` 

#### IntelliJ
1.  Go to the menu “Run” - “Edit configurations”

2.  In the “Run/Debug Configurations” window, press the plus button and
    select “Application”.

3.  Optionally, give a name to the task to remember what it is

4.  Set “Main class” to “ch.isska.viskar.restAPI.Main”

5.  Set "Use classpath of module" to "viskar-serve"

6.  Set no "Program arguments"

7.  Press “Ok” to save the configuration

8.  At the beginning of each session you need to run this application to start the server.

9. To test if it worked, navigate to “localhost:3145/v1.0/public/swagger.json” in a
    web browser. You should see the swagger API docs.

10. Optionally, set "Environment variables" to `LOG_LEVEL=debug` to enable more verbose messages.

### Updating the data-base
#### Command line
1. from the src/server folder run `sbt "admin/run migrate-db"`

#### IntelliJ
1.  After each update of the database schema, and on the first start, the database migration job
    needs to be run. To achieve this, run the `viskar-admin` task with the argument `migrate-db`.

2.  On the first start, and after each time the translation files have been updated, you
    need to execute the `sbt compile` task to compile the translations to scala. Do this
    by opening the "SBT projects" pane in IntelliJ Idea, select the
    task "VisKar > root > SBT Tasks > compile" and double click on it.

3.  By default, you can log in with the test user: `test@visualkarsys.isska.ch` and password `************`.
    You can also create an account by executing `viskar-admin` with the arguments `create-user --email test@test.ch --user-name test --password ************`.

4.  If you wish, you can load the pilot project by executing `viskar-admin` with the arguments
    `load-sample-project --owner-email test@test.ch`.

## Application Config
A number of properties must be defined for your development environment. A configuration file is used for the backend properties: `src/server/logic/src/main/resources/application.conf`

To begin, make a copy of the template file `example.application.conf`, named `application.conf`, and make your changes there. The config file must be edited for the algorithm utilities, detailed below.

## Algorithm utilities

### GeoCruncher
GeoCruncher is a Python package required for computing geological meshes and interfaces.

1. Downloaded the source from the repository: https://github.com/ISSKA/geocruncher

2. Follow the installation guide for GeoCruncher and its dependencies

3. Update the backend application config

3.1 Add `python-path` to the file and make it point to the python.exe location

3.2 Set `geocruncher-script-path` to point to GeoCruncher's main.py file

### GeoAlgo
The geo-algo directory contains C++ applications for running algorithms on geological meshes. Currently, this is limited to the VK-Aquifers application.

1. Use CMake to generate an IDE project. Set the source code directory to `src/geo-algo/VK-Aquifers`

2. Build the project using your IDE/compiler of choice

3. Update the backend application config. Set `geo-algo-path` to point to the executable file you built (build mode: release, not debug)

### Sec Export

The sec-export utility is required when exporting a project to GeoModeller. This step is therefore optional. The application must only be built once, unless its source code is modified.

These instructions are specific to Linux.

1.  To build, you will need a C++ compiler (which is usually shipped with your distribution) and CMake.
    You can install CMake by typing the following command to the terminal:

        sudo apt-get install cmake

2.  You'll also need the OpenCascade library.

        sudo apt-get install liboce-*

    (Note that this is the community edition; if you want the official one, you'll have to download the
    sources and compile them by yourself, but this shouldn't be a problem)

2.  Navigate to the `src/sec-export` folder.

3.  Execute the following commands:

        cmake .
        make

    This will create an executable named `viskar-sec-export`.

 4. Path configuration

    Edit the application config, as described above, and set the property `geomodeller-sec-export-helper` to the
    absolute path to the sec-export executable you just created (including the filename).

## Integration Tests

The integration tests require a fully setup Visual Karsys database to run. On the continous
integration server, this is handled automatically and fresh database is created from scratch
for each build.

If you want to run the integration tests locally, execute `sbt it:test`. You may need to re-run
the `sbt "admin/run migrate-test-db"` or `sbt "admin/run setup-dev-env"` command beforehand if the db schema has changed.
