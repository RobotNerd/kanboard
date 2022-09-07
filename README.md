# Kanboard

Build and deploy [kanboard](https://kanboard.org/) to docker
with plugin support.

## Build and deploy

- Plugins
  - Create a `plugins/` subdirectory.
  - Download [kanboard plugins](https://kanboard.org/plugins.html)
    to the `plugins/` directory. These will be copied to the docker
    container when building it.

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

