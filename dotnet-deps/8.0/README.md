# dotnet-deps

This directory contains the image recipe of .NET 8 runtime. This image is
smaller in size, hence less prone to vulnerabilities. Know more about chisel
[here](https://github.com/canonical/chisel).

### Building the image(s)

Build the rock in the usual way:

```sh
$ rockcraft pack
```

### Running the image(s)

Import the recently created rock into Docker using
[skopeo](https://github.com/containers/skopeo).

```sh
$ skopeo --insecure-policy copy \
	oci-archive:dotnet-deps_8.0_amd64.rock \
	docker-daemon:dotnet-deps:8.0
```

The image has `pebble enter` as entrypoint. Learn more about [pebble](https://github.com/canonical/pebble).


### Usage

Launch this image locally:

```sh
$ docker run -d --name dotnet-deps-container -e TZ=UTC ubuntu/dotnet-deps:8.0-24.04_stable
```

You will see the following output:

```sh
docker: Error response from daemon: No command specified.
See 'docker run --help'.
```

And that is normal. That's because this image doesn't have an entrypoint. Its
main purpose is to provide all the basic dependencies for you to be able to
layer a self-contained .NET or ASP.NET application on top of it.

See this example, with a multi-stage Dockerfile building an ASP.NET app on
Ubuntu 22.04 and packaging it on top of ubuntu/dotnet-deps:8.0-24.04_stable:

```Dockerfile
FROM ubuntu:22.04 AS builder

# install the .NET 8 SDK from the Ubuntu archive
# (no need to clean the apt cache as this is an unpublished stage)
RUN apt-get update && apt-get install -y dotnet8 ca-certificates

# add your application code
WORKDIR /source
# using https://github.com/Azure-Samples/dotnetcore-docs-hello-world
COPY . .

# export your .NET app as a self-contained artefact
RUN dotnet publish -c Release -r ubuntu.22.04-x64 --self-contained true -o /app

FROM ubuntu/dotnet-deps:8.0-24.04_stable

WORKDIR /app
COPY --from=builder /app ./

ENTRYPOINT ["/app/dotnetcoresample"]
```

Run the following commands with the Dockerfile example from above:

```sh
$ git clone https://github.com/Azure-Samples/dotnetcore-docs-hello-world.git
$ cd dotnetcore-docs-hello-world
# copy the content from the example above into ./Dockerfile
$ docker build . -t my-chiseled-aspnet-app:latest
$ docker run -p 8080:8080 my-chiseled-aspnet-app:latest
# access http://localhost:8080/
```
