## Boson Project Builders - Making Local

Copy of the Boson Project Buildpacks just to demonstrate how to make a builder in a local environment.

Change the `builder.toml` file to reference the images as tagged in a different container registry, i.e. the

- buildpacks / uri
- stack / run-image
- stack / build-image

then

`make builders`

This will generate `quay.io/swinches/faas-python-builder:v0.9.2` - our own tagged builder image.

Check the metadata is correct....

```
$ podman run -it quay.io/swinches/faas-python-builder:v0.9.2 bash
bash-4.4$ find / -name *.toml
find: '/root': Permission denied
find: '/proc/tty/driver': Permission denied
find: '/lost+found': Permission denied
find: '/var/lib/private': Permission denied
find: '/var/lib/rhsm': Permission denied
find: '/var/lib/portables': Permission denied
find: '/var/cache/ldconfig': Permission denied
/cnb/buildpacks/dev.boson.python/0.0.1/buildpack.toml
/cnb/stack.toml
/cnb/order.toml
bash-4.4$ cat /cnb/stack.toml
[run-image]
  image = "quay.io/swinches/something:python-v0.9.2"
```

Note that the `run-image` now references the one as specifed.




## Boson Project Buildpacks

This repository contains the necessary code to create CNCF buildpacks for functions.

### Usage

Run `make all` to ensure that you have all of the necessary images created
and available in your local Docker daemon.

### Building a Sample Node.js Application

Set your default builder to the one that you just created.

```console
pack set-default-builder quay.io/boson/faas-nodejs-builder
```

Then you can build a Node.js function app.

```console
pack build hello-nodejs -p apps/hello-nodejs
```

Run the app using Docker

```console
docker run --rm -p 8080:8080 hello-nodejs
```

Send the app a Cloud Event

```console
curl -X POST -d '{"hello": "world"}' \
    -H'Content-type: application/json' \
    -H'Ce-id: 1' \
    -H'Ce-source: cloud-event-example' \
    -H'Ce-type: dev.knative.example' \
    -H'Ce-specversion: 1.0' \
    http://localhost:8080
```
