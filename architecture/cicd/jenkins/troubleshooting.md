# Jenkins Issues Troubleshooting

Your build is failing? There are several problems that can fail your build. Some of the problems we had before are documented here.

## Unclean Workspace

Jenkins does not fully remove the workspace after a build. This can speed up sequential builds with little changes.
However, this can break builds, when certain things change but the workspace has old artefacts still there.

For example when we updated gmlib-0.3.1 to gmlib-0.3.11 the jenkins workflow with the pr was working fine but not the develop pipeline.
Because the pr pipeline workspace was fresh and the develop workspace had old artefacts' init.
Or on one instance, the creation of the FLYWAY-*.jar was moved from /src/logic to /src/test which left the workspace with 2 of these jars, breaking the sbt (scala) build task.
To fix such issues, you can just delete the workspace folder. 

To do this connect to the server via ssh.
Then run this command. Please beware, do not delete the folder while a build is running, wait for it to fail or succeed.
```shell
rm -rf /var/lib/jenkins/workspace/VISKAR_develop
```

## No space left on device

Jenkins works with virtual memory and thus is occupying space on the server's disk. It might be possible that the space available is too small for the Jenkings to process any new job : builds, tasks and even rebooting.
The first thing to do is to check what is taking so much space on the server disk.
With the
```shell
df -h
du -h
```
commands, or with specific docker commands.
```shell
docker system df
```

The first case of the problem was that the docker images were not deleted and thus the server had about 240Go of unusable old images.
To delete them, we used the docker command :
```shell
docker image prune -a --filter 'until=2022-08-01'
```
With this, we deleted all the unused images older than the 1st August 2022. This freed a bit of space but most of the deleted data went into the build cache.
This is why the next command followed :
```shell
docker builder prune
```

This allowed us to clean 150Go of the disk space.
