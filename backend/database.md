# Postgres/Postgis Update
For The duration of the update it is a good idea to temporarily shutdown the server/nginx so no data is lost in between. 
This can be achieved by using the "/bin/true" command in the <dev|prod>.docker-compose.yaml and restarting the service `systemctl restart <dev|prod>.docker-compose`.
## Major Manual

**NOTE 11.01.2021:** As we had previously used nixos we had to upgrade manually.  
based on https://www.postgresql.org/docs/current/upgrading.html#UPGRADING-VIA-PGDUMPALL
First we had to dump the database and reload it. The update from start to finish takes about 1 hour.
* dev-server
```console
root@Ubuntu-1804-bionic-64-minimal ~# docker exec -it web-prod /run/current-system/sw/bin/bash
root@web-ci:/$ su postgres
postgres@web-ci::/$ cd 
postgres@web-ci::/$ pg_dumpall > outputfile
root@Ubuntu-1804-bionic-64-minimal ~# mv /root/data/web-prod/var/lib/postgresql-10.0/outputfile  /root/data/web-ci/var/lib/postgresql-13-3.1/outputfile
root@Ubuntu-1804-bionic-64-minimal ~# docker exec -it docker_postgis-dev_1 bash
root@docker_postgis-dev_1:~$ cd /var/lib/postgresql/data
root@docker_nginx-dev_1:/var/lib/postgresql/data#$ psql -d postgres -U viskar -f outputfile
```
* prod-server
```console
root@Ubuntu-1804-bionic-64-minimal ~# docker exec -it web-prod /run/current-system/sw/bin/bash
root@web-ci:/$ su postgres
postgres@web-ci::/$ cd 
postgres@web-ci::/$ pg_dumpall > outputfile
root@Ubuntu-1804-bionic-64-minimal ~# mv /root/data/web-prod/var/lib/postgresql-10.0/outputfile  /root/data/web-prod/var/lib/postgresql-13-3.1/outputfile
root@Ubuntu-1804-bionic-64-minimal ~# docker exec -it docker_postgis-prod_1 bash
root@docker_postgis-prod_1:~$ cd /var/lib/postgresql/data
root@docker_nginx-prod_1:/var/lib/postgresql/data#$ psql -d postgres -U viskar -f outputfile
```
**NOTE 11.01.2021:** For some reason one of the triggers `crop_dem_to_project_trigger` was not correctly re-imported which caused the SRTM re-fetching to produce incorrect data.  
In case of Error Using `pg_dump -U viskar --schema-only viskar >schema-only` one can compare the schema before and after the migration.  
After this step.

## Major Automatic
It may be possible to upgrade using `pg_upgrade` https://www.postgresql.org/docs/current/upgrading.html#UPGRADING-VIA-PG-UPGRADE

## Minor Updates & Adding Extensions
To add minor extensions or make minor updates to them one can just:
1. add them to the [postgresql.Dockerfile](../../src/docker/postgresql.Dockerfile) 
2. re-build `docker-compose -f src/docker/ci-it.docker-compose.yaml build` (might also be triggered automatically.)
3. connect to the db `docker exec -it docker_postgis-local_1 bash` and `psql -U viskar`
3. create or update the extension`<create|update> extension <name>` (or follow the docu of the extension)
  1. add the extension to [setupPostGIS](../../src/server/logic/src/main/scala/ch/isska/viskar/persistence/package.scala)
