# Kanboard

Build and deploy [kanboard](https://kanboard.org/) to docker
with plugin support.

## Build and deploy

- Plugins
  - Create a `plugins/` subdirectory.
  - Download [kanboard plugins](https://kanboard.org/plugins.html)
    to the `plugins/` directory. These will be copied to the docker
    container when building it.

- Database
  - Create a `db/` subdirectory.
  - This will be mounted on the container to provide persistent
    storage for the postgres database container.

- Build the custom kanboard image that includes plugins.
  ```
  docker build -t kanboard:mib .
  ```

- Start the containers in the background.
  ```
  docker-compose -f ./docker-compose-kanboard.yml up -d
  ```

- Manually create the database (first time only).
  - NOTE: If restore from backup, skip these steps and follow the
    [Restore](#restore) instructions below.
  - Exec into the postgres container.
    ```
    docker exec -it <ID> /bin/ash
    ```
  - Start psql.
    ```
    psql -U postgres
    ```
  - Create the database.
    ```
    CREATE DATABASE kanboard;
    ```

- Login to kanboard at http://localhost:8088

## Backup and restore

> Steps taken from here https://stackoverflow.com/a/63435830/241025.

- Run all commands on the host.
- Replace `CONTAINER_ID` with the id of the postgres container.
- To backup:
```shell
docker exec -t CONTAINER_ID pg_dumpall -c -U postgres | gzip > ./kanboard_dump_$(date +"%Y-%m-%d_%H_%M_%S").sql.gz
```

- To restore (replace `BACKUP` with the name of the backup file).
```shell
gunzip < BACKUP | docker exec -i CONTAINER_ID psql -U postgres -d kanboard
```

### Automate backups

Create a cron job to automatically run the backups. The steps below provide an example implementation.
- Create a directory to store the backups.
  ```
  mkdir $HOME/dev/kanboard/backup
  ```
- Run `crontab -e`.
- Add a line to run the backup command.
  - This example runs the backup every Saturday at 0100.
  - This command gets the container id assuming that there's only one postgres container running on the system.
  ```shell
  0 1 * * 6 docker exec -t $(docker ps | grep postgres | awk '{ print $1 }') pg_dumpall -c -U postgres | gzip > $HOME/dev/kanboard/backup/kanboard_dump_$(date +"%Y-%m-%d_%H_%M_%S").sql.gz
  ```
