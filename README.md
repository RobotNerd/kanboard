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

- Start the containers.
  ```
  docker-compose -f ./docker-compose-kanboard.yml up
  ```

- Manually create the database (first time only).
  - Exec into the postgres contaienr
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

- Stop the containers.
  ```
  docker-compose -f ./docker-compose-kanboard.yml stop
  ```

- Backup
  ```
  docker-compose -f ./docker-compose-kanboard.yml run --rm db-backup
  ```

  - This creates the file `./backup/db.tar.bz2`

- Restore
  ```
  docker-compose -f ./docker-compose-kanboard.yml run --rm db-restore
  ```

