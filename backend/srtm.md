# Managing the local SRTM data

See the [related task](https://trello.com/c/HIOsp2uM/1581-fetch-srtm-is-broken-again-think-about-downloading-and-storing-tiles-on-our-side) on Trello.

SRTM (Shuttle Radar Topography Mission) was a NASA mission that captured (most of) the earth's digital elevation.  
The resulting data is made available for free on various mirrors, some of which are hosted by NASA themselves.  

In an effort to improve uptime reliability of this data source, we decided to download all the data on our server, and process it from there, instead of downloading SRTM tiles on demand from an online mirror, whose availability has proven to be insufficient over the years.

The data source we chose has a total of 14297 tiles, which represents approx. 14.7 GiB of compressed data in `.hgt.zip` format.  
Alternate data sources were considered, but in order to keep things simple, we downloaded the exact same data we had been using previously.  
See discussions on the Trello card for details on these decisions.

## Implementation

### SRTM Puller

To this end, a Data Pulling Tool has been implemented.  
The project is available here: https://github.com/ISSKA/srtm-puller  
A [README](https://github.com/ISSKA/srtm-puller/blob/master/README.md) explains how to use the tool.

The tool downloads all tiles, and also creates a `not-found.json` file that contains a list of tile names that do not exist (returned 404).  
This is mainly used in the cache mechanism, but could also be reused as part of an implementation (to avoid checking on disk if a tile exists).

### SRTM Mirror

The downloaded data was uploaded to Github, using Git LFS.  
We had to upgrade the Data Plan of the ISSKA organization, because by default, only 1 GB of Git LFS is free on Github.  
We now have 50 GB available.

The mirror is available here: https://github.com/ISSKA/srtm-30  
Github doesn't allow to view it entirely on the web, because there are too many files.

Here are the commands to clone the repository and download the actual binary files:

```bash
apt install git-lfs # install the git lfs extension
git clone https://github.com/ISSKA/srtm-30
cd srtm-30
git lfs pull # ask for the binary files instead of the lfs pointers
```

### Configuration

The path to the SRTM tiles is configured [in Spring](https://github.com/ISSKA/VISKAR/blob/develop/src/server/spring-restAPI/src/main/resources/application.yml) via the key `srtm-tiles-root`.  
The path is set to `/root/srtm-30`.  

In the docker configuration of [dev](https://github.com/ISSKA/VISKAR/blob/develop/src/docker/dev.docker-compose.yaml) and [prod](https://github.com/ISSKA/VISKAR/blob/develop/src/docker/prod.docker-compose.yaml), the `rest-api` container has a volume with a bind mount from `/root/srtm-30` to the physical path `/root/srtm-30`, where the Github Mirror was cloned.  
This way, both dev and prod use the same data on the host machine, and it is easier to manage it.  
Be warned: to update the docker configuration, you need to commit to dev, then manually update the VISKAR repository cloned at `/root/VISKAR` on the server.  
**It is not handled by Jenkins.**

For local development, the Spring config can be changed for any path where the Github Mirror was cloned. That change must however not be committed.

## Maintenance

If we know there is a new version of a certain SRTM tile, it is possible to download it manually, or use the puller script.  
The new data can then be committed to the Gihub Mirror. Finally, the new version has to be pulled on the Viskar Server.  
No restart is required, as the data is read from disk on demand.

If we wish to switch to another SRTM provider, the data has to be completely replaced, and care must be taken to update the SRTM constants [in the backend service](https://github.com/ISSKA/VISKAR/blob/develop/src/server/spring-restAPI/src/main/java/ch/isska/viskar/spring/service/jooq/DemService.java):

```java
// SRTM3 constants
private static final int SRTM3_NB_POINTS_LATITUDE = 1201;
private static final int SRTM3_NB_POINTS_LONGITUDE = 1201;
private static final ByteOrder SRTM3_BYTE_ORDER = ByteOrder.BIG_ENDIAN;
private static final double SRTM3_NO_DATA_VALUE = -32768.0;
private static final double SRTM3_CELL_SIZE = 3.0 / 3600.0;
```

In the old Backend, the entire SRTM downloading logic was abstracted, to allow the integration of multiple SRTM sources, even though only one was ever used.  
During reimplementation, all that complexity was replaced with a single implementation, because it was not worth the time to abstract all the logic again.  
If we wish to support multiple sources in the future, the logic, together with these constants, could be refactored to be abstract.  
The DemService would then call an abstract SrtmProvider, instead of the code being in plain inside the DemService.

The only assumptions in the code is that an SRTM tile's size is 1x1 in WSG84 coordinates, the name of the SRTM file is build with the `stringifySrtm3Tile` method, and by adding `.SRTMGL3.hgt.zip` at the end.  
We also cap the maximum number of tiles processed at once to 4, to avoid overloading our server in case of mistake, since 95% of projects should not require more than 4 tiles.
