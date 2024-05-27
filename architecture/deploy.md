Deployment
==========

The server runs Ubuntu 18.04. On top of it docker containers are run:

- Web CI
- Web Prod

We use docker as it let's us separate all servers and allows us e.g. to update and restart Web CI without affecting the production server.  
The full configuration of the docker images including settings, dependencies, build etc. resides in the [docker](../../src/docker) directory.

The new architecture is based on jenkins and docker instead of nixos see [here](../jenkins/jenkins.md) got more information on the CI aritecture.

**Disclaimer:** The old server config can be found https://github.com/ISSKA/viskar-ops/

Initial Setup
-------------
**Disclaimer:** this is part of the initial setup using nixos which is not used anymore, only the "relevant parts" are included here for the rest you can use git.

1. Login as any non-root user with sudo permissions
1. Login as root
1. Install docker, nginx and git: ``apt-get install docker.io git nginx systemd-docker``
1. Clone the viskar-ops repository: ``git clone git@github.com:ISSKA/viskar-ops.git``
1. Clone the VISKAR repository: ``git clone git@github.com:ISSKA/VISKAR.git``
1. Enable nginx site: ``pushd /etc/nginx/sites-available && ln -s /root/viskar-ops/docker-deploy/nginx_visualkarsys.config && popd``
1. Setup let's encrypt according to https://certbot.eff.org/lets-encrypt/ubuntuxenial-nginx.html for all domains
    - visualkarsys.com
    - dev.visualkarsys.com
    - hydra.visualkarsys.com
    - www.visualkarsys.com
1. Copy the user file:
    - ``mkdir -p /etc/nginx/auth``
    - ``cp /root/viskar-ops/docker-deploy/nginx_hydra_users /etc/nginx/auth/``
1. Restart nginx: ``systemctl restart nginx``
1. Enable ipv4 forwarding in /etc/sysctl.conf: `net.ipv4.ip_forward=1`
1. Prepare the directories to store the persistent docker container data:
    - ``mkdir -p mkdir -p /root/data/web-ci/var/lib``
    - ``mkdir -p mkdir -p /root/data/web-prod/var/lib``

1. Link systemd services:
    - ``pushd /etc/systemd/system``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-hydra.service``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-machine-hail@.service``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-machine-web-ci.service``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-machine-web-prod.service``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-cleanup.service``
    - ``ln -s /root/viskar-ops/docker-deploy/docker-cleanup.timer``
    - ``popd``
1. Enable the systemd services:
    - ``systemctl enable docker-hydra.service``
    - ``systemctl enable docker-machine-hail@web-ci.service``
    - ``systemctl enable docker-machine-hail@web-prod.service``
    - ``systemctl enable docker-machine-web-ci.service``
    - ``systemctl enable docker-machine-web-prod.service``
    - ``systemctl enable docker-cleanup.timer``
1. Open hydra.visualkarsys.com in a web browser, HTTP user viskar, password ************
1. Login as admin, password ************
1. Click create project and enter the following settings:
    - Enabled: Yes
    - Identifier: viskar
    - Display name: viskar
    - Owner: admin
    - Declarative spec file: ci/spec.json
    - Declarative input type: Git checkout - https://541421423d432615c7e86547650c02cbca44ed35@github.com/ISSKA/viskar-ops.git develop
1. Reboot the machine
1. Wait until the web pages are online (this might take a moment)
1. Create an initial admin account, e.g. for web-ci:

- ``docker exec -it web-ci /run/current-system/sw/bin/bash``
- ``viskar-admin create-user --email bla@test.ch --user-name "bla" --password "************"``

Installation of the backup environment
--------------------------------------

Disclaimer: all username / password are on the password Trello card.

We make backups to a storage box on hetzner. First, activate SSH support for the storage box in the [hetzner storage box parameters](https://robot.your-server.de/storage). As we have both storage box
and server commands, we precise in which environment the command should be executed using: `root@server:/#` or `sftp>`.

1. Connect to the server with ssh, install borgbackup:
    ```console 
    root@server:/# apt install borgbackup
    ```

2. Create the storage box authorization keys:
    ```console 
    root@server:/# cat .ssh/id_rsa.pub >> storagebox_authorized_keys
    ```
3. Upload the `storagebox_authorized_keys` to the sftp:
    ```console 
    root@server:/# echo -e "mkdir .ssh \n chmod 700 .ssh \n put storagebox_authorized_keys .ssh/authorized_keys \n chmod 600 .ssh/authorized_keys" | sftp <box-username>@<box-username>.your-backup.de
    ```

4. You should now be able to connect to the sftp without password using port 23:
    ```console 
    root@server:/# sftp -P 23 <box-username>@<box-username>.your-backup.de
    ```

Keep in mind that you have to be connected to the server in ssh first as the storage box is only accessible from within the hetzner network.

5. Create the backup folder:
    ```console 
    sftp> mkdir backups
    sftp> cd backups
    sftp> mkdir VK
    ```

6. Initialize borg backup:

    ```console 
    root@server:/# borg init --encryption=repokey ssh://<box-username>@<box-username>.your-backup.de:23/./backups/VK
    ```

You will be asked to provide a passphrase, don't forget to put it on Trello password's card.

7. Create the first backup using:

    ```console 
    root@server:/# borg create ssh://<box-username>@<box-username>.your-backup.de:23/./backups/VK::YYYY_MM_DD_initial /root/data/web-ci /root/data/web-prod /root/hydra_working_docker_image.tar /root/data/hydra/var/lib/postgresql/10.0/ # Important folder to backup may change overtime, you can add/remove folder here
    ```

8. You can create a script file to do backup more easily:

    ```console 
    root@server:/# mkdir -p /var/log/borg
    root@server:/# nano /usr/local/bin/backup.sh
    ```

9. Add the following content to the script:

```shell
#!/usr/bin/env bash

##
## Set environment variables
##

## if you don't use the standard SSH key,
## you have to specify the path to the key like this
# export BORG_RSH="ssh -i /home/userXY/.ssh/id_ed25519"

## You can save your borg passphrase in an environment
## variable, so you don't need to type it in when using borg
# export BORG_PASSPHRASE="top_secret_passphrase"

##
## Set some variables
##

LOG="/var/log/borg/backup.log"
BACKUP_USER="<box-username>"
REPOSITORY_DIR="VK"

## Tip: If using with a Backup Space you have to use
## 'your-storagebox.de' instead of 'your-backup.de'

REPOSITORY="ssh://${BACKUP_USER}@${BACKUP_USER}.your-backup.de:23/./backups/${REPOSITORY_DIR}"

##
## Output to a logfile
##

exec > >(tee -i ${LOG})
exec 2>&1

echo "###### Backup started: $(date) ######"

## Uncomment to run cleanup
## echo "Cleanup previous backups"
## ## Keep all backups for a month and keep one backup for 
## ## each of the 12 previous months
## borg prune -v --list --keep-daily=31 --keep-monthly=12 $REPOSITORY
## echo "## Cleanup ended: $(date) ##"

echo "Transfer files ..."
borg create -v --stats                   		    \
    $REPOSITORY::'{now:%Y-%m-%d_%H:%M}'  		    \
    /root/data/web-ci                           \
    /root/data/web-prod                         \
    /root/hydra_working_docker_image.tar        \
    /root/data/hydra/var/lib/postgresql/10.0/   \
    --exclude /dev                       		    \
    --exclude /proc                      		    \
    --exclude /sys                       		    \
    --exclude /var/run                   		    \
    --exclude /run                       		    \
    --exclude /lost+found                		    \
    --exclude /mnt                       		    \
    --exclude /var/lib/lxcfs

echo "###### Backup ended: $(date) ######"
```

10. Set the permission to execute the script:

    ```console 
    root@server:/# chmod +x /usr/local/bin/backup.sh
    ```

11. Execute the script using the command under _Making a server backup_.

12. If you added a script you can now create an automatic job as well, doing so we recommend uncommenting this line in the script above:

  ```shell
  ## Uncomment to run cleanup
  echo "Cleanup previous backups"
  ## Keep all backups for a month and keep one backup for 
  ## each of the 12 previous months
  borg prune -v --list --keep-daily=31 --keep-monthly=12 $REPOSITORY
  echo "## Cleanup ended: $(date) ##"
  ```

13. Open crontab as root:

  ```console
  root@server:/# crontab -e
  ```

14. Add the following line to a run a daily backup at 00:00:

  ```console
    0 0 * * * /usr/local/bin/backup.sh > /dev/null 2>&1
  ```

15. To check that backups are well done, you can get the list of backups using:

  ```console
    root@server:/# borg list ssh://<box-username>@<box-username>.your-backup.de:23/./backups/VK
  ```

Complementary information:

- https://docs.hetzner.com/robot/storage-box/backup-space-ssh-keys/
- https://community.hetzner.com/tutorials/install-and-configure-borgbackup

Create new release
------------------

To create a new release, simply merge the develop branch into master and push the result to github:

    git checkout master
    git merge --no-ff develop
    git push

The Prod deployment will be updated after master has been successfully built and all tests have run through. This will take around ~10-20 minutes.



Making a manual server backup
-----------------------------

Server backups are incremental based on the installation (see _Installation of the backup environment_), you can just run the command from the server:

```console
borg create --stats ssh://<box-username>@<box-username>.your-backup.de:23/./backups/VK::YYYY_MM_DD /root/data/web-ci /root/data/web-prod /root/hydra_working_docker_image.tar /root/data/hydra/var/lib/postgresql/10.0/ # Important folder to backup may change overtime, you can add/remove folder here
```

You can also use the script for easier backup if it was created in the installation process:

```console
/usr/local/bin/backup.sh
```

Backup / Restore database
-------------------------

**Disclaimer:** database backup should be done using server backup directly, these commands are not used anymore.

To backup the database, use the following command:

    pg_dump viskar | gzip > viskar_prod_YYYY_MM_DD.sql.gz

To restore the datbase, use the following command:

    createdb -O viskar-api viskar
    gunzip -c viskar_prod_YYYY_MM_DD.sql.gz | psql viskar
    echo "ALTER DATABASE viskar SET postgis.gdal_enabled_drivers TO 'ENABLE_ALL';" | psql viskar

Credentials
-----------

### Github CI account

User: isska-bot  
Password: ************
