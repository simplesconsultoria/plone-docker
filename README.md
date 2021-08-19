# Plone (pip based)

[Plone](https://plone.org) is a free and open source content management system built on top of the Zope application server.


## Features

- Images for Plone: 5.2.4, 5.2.5
- Images for ZEO server: 5.2.2
- Installed with pip, not unified installer.


### Supported tags and respective `Dockerfile` links

#### Plone
- [`5.2.5`, `latest` (*5.2.4/Dockerfile*)](https://github.com/simplesconsultoria/docker-plone/blob/main/5.2.4/Dockerfile)

- [`5.2.4`, (*5.2.4/Dockerfile*)](https://github.com/simplesconsultoria/docker-plone/blob/main/5.2.4/Dockerfile)


## Prerequisites

Make sure you have Docker installed and running for your platform. You can download Docker from https://www.docker.com.

## Usage


### Standalone Plone Instance

Plone standalone instances are best suited for testing Plone and development.

Download and start the latest Plone 5 container, based on [Debian](https://www.debian.org/).

```console
docker run -p 8080:8080 simplesconsultoria/plone:latest
```

This image includes `EXPOSE 8080` (the Plone port), so standard container linking will make it automatically available to the linked containers. Now you can add a Plone Site at http://localhost:8080 - default Zope user and password are **`admin/admin`**.

### Extending from this image

In a folder, create a Dockerfile, i.e.:

```
FROM simplesconsultoria/plone:latest

COPY requirements.txt requirements.txt
RUN ./bin/pip install -r requirements.txt

```

Also add a requirements.txt file, with additional packages

```
plone.restapi
plone.tiles>=1.8.3
plone.subrequest>=1.8.1
plone.app.tiles>=3.0.3
plone.app.standardtiles>=2.2.0
plone.app.blocks>=4.1.1
plone.app.drafts>=1.1.2
plone.app.mosaic>=2.0rc8
plone.formwidget.multifile>=2.0
plone.jsonserializer>=0.9.5

```

Build your new image

```console
docker build -t site .
```

And start the container

```console
docker run -d -p 8080:8080 site
```

## License

The project is licensed under the GPLv2.
