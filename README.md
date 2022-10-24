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

To backup, run this command to create a tar file of the db directory.
Replace DATE with the current date/time.

```
tar -cvjSf backup-DATE.tar.bz2 db
```

To restore from a fresh deployment, delete the `db` subdirectory and
run this command:

```
tar -xvjf backup-DATE.tar.bz2
```

