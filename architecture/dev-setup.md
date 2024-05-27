# Development environment setup

This section describes the setup necessary to get started with developing Visual Karsys.

## Windows Disclaimers

1. Please read this as else *nothing* will work as the setup only works *correctly* on linux/docker
1. Please use a working package-manager for example https://chocolatey.org/ as a substitute of `snap`&`pacman`
1. For windows, it is recommended to use for [WSL 2](https://docs.microsoft.com/en-us/windows/wsl/install-win10) [WSL2 Docker](https://docs.docker.com/docker-for-windows/wsl/).
    1. For a local installation could also try the old documentation which however has not been tested on Windows in years.
1. please only execute command in a functionally working command line like git-bash, wsl-bash, cygwin, ash, bash, sh, fish,zsh etc. and not functionally broken ones like powershell,windows-cmd etc.
    1. for example, we depend on functionality like (but not limited to)`&& || $HOME $PWD > | folder/subfolder`

## Packages

### Required

Use your favorite package manager for example pacman: `pacman -S docker docker-compose jdk11-openjdk nodejs sbt yarn`
Depending on your distribution you may need to manually update your nodejs package to at least the same LTS version as the server (currently 14.x.y).  
**NOTE:** if you do not want to use sudo everytime you can add your user to the docker group using `sudo gpasswd -a $USER docker`

### Recommended

* `pacman -S dbeaver` Database GUI Tool- for visualization/debugging etc.
* Recommended IDEs
    * Frontend&Backend`snap install intellij-idea-ultimate --classic`(free for students)
    * Backend`snap install intellij-idea-community --classic`
    * Frontend `pacman -S code` or `snap install code --classic`
* Recommended IDE Plugins:
    * intellij-idea
        * .ignore (4.1.0)
        * Kotlin (211-1.5.10-release-891-IJ7142.45)
        * Prettier (211.7142.13)
        * Python (211.7442.45)
        * Requirements (2021.4-211)
        * Scala (2021.1.21)
        * Unit File Support (systemd) (0.3.2)
    * Visual Studio Code (code)
        * [editorconfig](https://editorconfig.org/#download)

## Version control

The source code for Visual KARSYS is managed in a Git repository.  
To access the repository, you can use the Git command line, the one integrated into your IDE or alternatively a UI such as [SourceTree](http://sourcetreeapp.com) .

1. `git clone git@github.com:ISSKA/VISKAR.git`
1. `git checkout develop`
1. `git submodule init`
1. `git submodule update` (this should be repeated when there was a change in the submodule, when switching branches)

## Code-Style

We use an [.editorconfig](../../.editorconfig) to specifying our Code-Style in the projects. It is supported in most [IDEs](https://editorconfig.org/#download) and it can be changed depending on the
project or programming-languages used.  
For **IntelliJ** you do not have to do anything as it is already supported out of the box.  
For **Visual Studio Code** you need to install a [plugin](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig). You may also need to install the editorconfig
globally. `npm install -g editorconfig` or `yarn global add editorconfig`

## Frontend

**Recommended IDE:** Visual Studio Code (free), IntelliJ IDEA/ Webstorm (free for students)
**Debugging:** In VS Code, install the Debugger for Chrome to enable debugging the TypeScript code. Alternatively, the browser's developer tools may be used, but may not support source maps
(mapping the JavaScript at runtime to the TypeScript source).  
We use Yarn for package management. The packages must be installed before running the front-end.

1. `cd src/client/`
1. `yarn` or `yarn install`. This installs the required node packages.
1. `yarn start` to run the development server

for more information you can about working with the frontend see [here](../../src/client/README.md)

## Backend Setup

**Recommended IDE: IntelliJ IDEA**  
*NOTE: depending on the version this may be done semi-automatically when opening the root folder.*

1. Install an integrated development environment (IDE). IntelliJ IDEA by Jet Brains is recommended. The Community Edition should suffice. All further steps assume IntelliJ IDEA.
    1. Install the Scala plugin for IntelliJ. This can be done on first startup or from the splash window, clicking on “configuration”, then “plugins”.
1. Click on "Import project"
1. Select the "/src/server folder
    1. If you are the Ultimate or want to also edit the markdown you can just select the root VISKAR folder
1. Select “Import project from external model” - “SBT”
1. Click on “Finish”
1. Click on “Ok” in the “SBT Project Data To Import” dialog

### Running the REST API

We run the REST API in docker container as the build is tested against the provided version of the docker-image.  
We also use the same database/database-plugin versions etc. as on the server, which should prevent incompatible versions bugs etc. There is quite a bit of setup involved otherwise:

1. `./run-server`
1. to restart sbt `docker restart docker_viskar-server-local_1`

The new spring boot restAPI can be run locally as it does only depend on java and nothing else. see also [Server](../../src/server/README.md)

1. run the `ApplicationStarter` over the IDE
1. run the gradle script`./gradlew spring-restAPI:bootRun`

For the first time startup the `bash -c "cd ...`command in the  `local.docker-compose.yaml` can be commented out.

### Submodule Updates

To test new changes made to a submodule create a new branch in viskar:

1. `git checkout -b feature/xxx`
1. `cd src/geocruncher && git checkout feature/xxx`
1. `cd .. && git commit -am "update geocruncher" `

**Disclaimer:** Currently we use pre-build wheels instead of building it as the geocruncher-dependencies are not expected to often change & do not contain tests anyway
