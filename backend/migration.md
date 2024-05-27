# Migration

Visual KARSYS currently works with two APIs : one outated implemented in Scala and a new one implemented in Java Spring. The application is, for the moment, in a state of migration between these two APIs.

## Objective
The objective here, is to translate the scala code into java spring and to transform the old static way (with the [project tree](project-tree.md)) into a RESTFul website with dynamic asynchronous requests (without the [project tree](project-tree.md)).

This work is done one resource at a time. For example, we migrate the tunnels, then the point of interests, etc.

Here is the [list of the migrations](https://trello.com/c/SM3vC4h3/1606-finish-migrating-backend-from-scala-to-java)

## Rules
To maintain correctly this migration, certain rules were put in place.

### Routes
The routes have to be structured like this :

```
/api/v2/projects/{projectId}/name_of_resources/ ...
```

In spring it would look like this, example with the FaultController :

```java
...

// Route endpoints
private final static String endpoint = WebSecurityConfig.API_URL + "projects/{projectId}/faults";

...

/*
  * REST : POST
  * Route : projects/{projectId}/faults/{faultId}
  * Update the values of a specific fault
  * Same as for the insert, uses MFault to create a temp object which contains all the fault data.
  */
@PutMapping(endpoint + "/{faultId}")
public ResponseEntity<?> update(@PathVariable long projectId, @PathVariable long faultId, @RequestBody MFiniteFault fault) {

...

```

### Abstraction
The different must use the [abstraction](abstraction.md) put in place.

The controller is a link to the resources, then the service calls the different daos manipulations and finally the daos calls the database and the different dtos with sql (using jooq) to execute the wanted request.

### Commenting
Each and every functions must be commented to explain their roles. For examples, the controller function, the routes, needs to be commented to show at least the REST verb and the complete route :

```java
/*
 * REST : DELETE
 * Route : projects/{projectId}/faults/{faultId}
 * Delete a specific fault and its data. If the geodata flag is set to true, then also delete all the fault's geodata (foliations and interfaces)
 */
```

### Return element
Each and every route *MUST* return the created, updated or get object

## Postman
To help you in the migration the tool PostMan is a must ! However, if you want to use PostMan, you need to change one line (temporarily) in the 'WebSecurityConfig.java' file.

The line 62 need to be decommented to allow a 'security breach' and disable the csrf

```java
http.csrf().disable(); // Remove comment to debug with postman
```

***IMPORTANT*** : Once the debug is done, comment the line once again !
