Client development tips
========================

# Package management
We use Yarn for managing node packages. Such packages add functionality and can be found at https://www.npmjs.com/. Refer to the [Yarn docs](https://yarnpkg.com/en/docs) for details.

- Add a package: `yarn add [packageName]@[versionNumber]` (e.g. `yarn add jquery@1.3.4`) (automatically adds an entry to package.json)

- Generally specify completely fixed version numbers, i.e. not just fixed major/minor

- Always commit the yarn lockfile. It ensures everyone installs the same package versions.

- If *package.json* has been edited by someone else, run `yarn install` on your client to install the right npm package versions.

- To update an npm package individually, run yarn add with the desired version number (see above)

# Application structure

Useful Directories
------------

- `vk/models`: Definitions of "business logic" objects for Visual KARSYS, e.g. projects, maps

- `vk/services`: Most REST services are located here, for client-server communication

- `vk/project`: Contains submodules for projects, such as the project list, 3d viewer, wizard

Useful Classes - OLD
---------------

- `ProjectDataStore`: Load and update an individual project, which is represented as a tree.


Angular Modules
----------------
- Modules are used to group related functionality. This allows to define dependencies and to lazily load modules on-demand. Refer to the Angular documentation and existing modules for details.

- Modules should always be lazy-loaded when possible

- Routing is defined in each module.

# Code conventions

- Angular components selectors must have the `vk-` prefix to avoid naming conflicts.

# Testing

- Unit tests are placed in a `.spec.ts` file in the same directory as the class under test.

- Tests can be executed by running `yarn test` from the command line.

# Angular router debug

When an exception occurs during Angular routing, which includes loading a page component or clicking a link, the error is often not visible in the browser javascript console.

To see the detailed error, [`enableTracing`](https://v2.angular.io/docs/ts/latest/api/router/index/ExtraOptions-interface.html#!#enableTracing-anchor) must be enabled in the router.

Temporarily change `RouterModule.forRoot(routes)` to `RouterModule.forRoot(routes, { enableTracing: true })` in `src/client/app/root/app.routing.ts`. **Do not commit the change!**

# CSS Styling - OLD

## Bootstrap
We use Bootstrap 3 for layout and styling. The framework has many useful components too, such as alerts and dialogs. https://getbootstrap.com/docs/3.3/components/

## Hershel
Hershel is a Bootstrap Theme. It also adds some styling of its own. Most Hershel-specific styling is used in the welcome page. The CSS files are included in our web application, and must not be modified directly.

## Custom CSS for Visual KARSYS
If Bootstrap and Hershel cannot provide the desired appearance, we may add additional classes to our CSS files. It's important to reuse existing styles whenever possible and to maintain the same look throughout the application.

* info.css: For the info/welcome website
* vk.css: For the KARSYS web application
