# Custom PostgreSQL Docker Image

This repository contains the Dockerfile and associated scripts for building a custom PostgreSQL Docker image. The unique feature of this image is a modification to the PostgreSQL source code to support longer identifier lengths. This is achieved by increasing `NAMEDATALEN` from the default of 64 to 151.

The Dockerfile includes procedures for fetching the PostgreSQL source, patching the `pg_config_manual.h` file, compiling the server with the new identifier length, and setting up the container environment.

## Prerequisites

Before building and running this custom PostgreSQL Docker image, make sure you have the following prerequisites installed:
- Docker Engine

## Building the Image

To build the Docker image from this repository, run the following command in the root directory where the Dockerfile is located:

```sh
docker build -t your-username/postgres_custum:latest .
```

Replace `your-username` with your Docker Hub username or any other username you prefer for naming your Docker images.

## Running the Container

To run a container based on the built image, execute the following command:

```sh
docker run -d \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -v custom_pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  your-username/postgres_custum:latest
```

Replace `mysecretpassword` with a secure password of your choice. The `-v` option mounts a volume for PostgreSQL data persistence, and the `-p` option maps the default PostgreSQL port from the container to the host.

## Custom PostgreSQL Configuration

The key feature of this Docker image is an increased maximum identifier length for database objects, such as table names, column names, and others. Using a `sed` command during the build process, the `NAMEDATALEN` parameter in `pg_config_manual.h` is changed:

```sh
RUN sed -i 's/#define NAMEDATALEN 64/#define NAMEDATALEN 151/g' pg_config_manual.h
```

This customization allows identifiers to be up to 151 characters long rather than the default 64 characters.

## Environment Variables

When you start the PostgreSQL container, you can adjust the configuration by passing one or more environment variables to the `docker run` command.

- `POSTGRES_PASSWORD` (required): The password for the PostgreSQL superuser.
- `POSTGRES_USER`: The superuser username (default: `postgres`).
- `POSTGRES_DB`: The name of the default database to create (default: value of `POSTGRES_USER`).
- `POSTGRES_HOST_AUTH_METHOD`: The authentication method for local connections (default: `md5`).

## Initialization Scripts

If you need to execute additional SQL or shell scripts upon container initialization, place them in the `/docker-entrypoint-initdb.d/` directory. This can be achieved by mounting a host directory containing your initialization scripts:

```sh
docker run -d \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -v custom_pgdata:/var/lib/postgresql/data \
  -v /path/to/initdb/scripts:/docker-entrypoint-initdb.d \
  -p 5432:5432 \
  your-username/postgres_custum:latest
```

Scripts in `/docker-entrypoint-initdb.d/` will be executed in alphabetical order. SQL files will be imported by default into the database specified by `POSTGRES_DB`, and shell scripts will be executed as-is.

## License

This repository is available under the terms of the [MIT License](LICENSE).

## Contributing

If you have any improvements or suggestions, please open an issue or pull request on GitHub.

## Acknowledgments

This custom PostgreSQL image builds upon the work of the PostgreSQL community and the robustness of the official PostgreSQL Docker image.


This is from [docker-library/postgres](https://github.com/docker-library/postgres/tree/d416768b1a7f03919b9cf0fef6adc9dcad937888/16/bookworm)