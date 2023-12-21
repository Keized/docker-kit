# Symfony Docker

A [Docker](https://www.docker.com/)-based installer and runtime for the [Symfony](https://symfony.com) web framework.

![CI](https://github.com/keized/docker-kit/workflows/symfony/badge.svg)

## Getting Started

1. If not already done, [install Docker Compose](https://docs.docker.com/compose/install/) (v2.10+)
2. Run `docker compose build --no-cache` to build fresh images
3. Run `docker compose up --pull always -d --wait` to start the project
5. Run `docker compose down --remove-orphans` to stop the Docker containers.


## Troubleshooting

### Editing Permissions on Linux

If you work on linux and cannot edit some of the project files right after the first installation, you can run `docker compose run --rm php chown -R $(id -u):$(id -g) .` to set yourself as owner of the project files that were created by the docker container.

## Production

### Deploying 
Copy your project on the server using git clone, scp or any other tool that may fit your need.

### Build

```
SERVER_NAME=your-domain-name.example.com \
APP_SECRET=ChangeMe \
docker compose -f compose.yaml -f compose.prod.yaml up -d --wait
```

## Credits

INpired by [Kévin Dunglas](https://dunglas.dev) [Symfony Docker Respository](https://github.com/dunglas/symfony-docker)