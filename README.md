# Stakater App Agility Platform (SAAP) Documentation

SAAP docs are built using [MkDocs](https://github.com/mkdocs/mkdocs) which is based on Python.

## GitHub Actions

This repository has Github action workflow which checks the quality of the documentation and builds the Dockerfile image on Pull Requests. On a push to the main branch, it will create a GitHub release and push the built Dockerfile image to an image repository.

## Build Dockerfile image and run container

Build Dockerfile image:

```shell
$ docker build . -t test
```

Run container:

```shell
$ docker run -p 8080:8080 test
```

Then access the docs on [`localhost:8080`](localhost:8080).

## Build locally

It is preferred to build and run the docs using the Dockerfile image, however an alternative is to run the commands locally.

Use [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/install.html) to set up Python virtual environments.

Install [Python 3](https://www.python.org/downloads/).

Install mkdocs-material and mermaid plugin:

```sh
$ pip3 install mkdocs-material mkdocs-mermaid2-plugin
```

Finally serve the docs using the built-in web server which is based on Python http server - note that the production build will use Nginx instead:

```
$ mkdocs serve
```

or 

```
$ python3 -m mkdocs serve 
```