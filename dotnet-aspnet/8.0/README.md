# dotnet-aspnet

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
	oci-archive:dotnet-aspnet_8.0_amd64.rock \
	docker-daemon:dotnet-aspnet:8.0
```

The image has `/usr/bin/dotnet [--info]` as entrypoint.

```sh
$ docker run --rm dotnet-aspnet:8.0
2024-03-14T13:11:06.286Z [pebble] Started daemon.
2024-03-14T13:11:06.301Z [pebble] POST /v1/services 7.557692ms 202
2024-03-14T13:11:06.301Z [pebble] Started default services with change 1.
2024-03-14T13:11:06.308Z [pebble] Service "dotnet-aspnet" starting: /usr/bin/dotnet [ --info ]
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] Host:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Version:      8.0.2
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Architecture: x64
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Commit:       1381d5ebd2
2024-03-14T13:11:06.628Z [dotnet-aspnet]   RID:          ubuntu.24.04-x64
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] .NET SDKs installed:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   No SDKs were found.
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] .NET runtimes installed:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Microsoft.AspNetCore.App 8.0.2 [/usr/lib/dotnet/shared/Microsoft.AspNetCore.App]
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Microsoft.NETCore.App 8.0.2 [/usr/lib/dotnet/shared/Microsoft.NETCore.App]
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] Other architectures found:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   None
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] Environment variables:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   DOTNET_ROOT       [/usr/lib/dotnet]
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] global.json file:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   Not found
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] Learn more:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   https://aka.ms/dotnet/info
2024-03-14T13:11:06.628Z [dotnet-aspnet] 
2024-03-14T13:11:06.628Z [dotnet-aspnet] Download .NET:
2024-03-14T13:11:06.628Z [dotnet-aspnet]   https://aka.ms/dotnet/download
2024-03-14T13:11:06.638Z [pebble] [change 1 "Start service \"dotnet-aspnet\"" task] failed: cannot start service: exited quickly with code 0
```

### Building and running a ASP.NET application

Let's use the following HelloWorld web application as an example:

```sh
$ git clone https://github.com/Azure-Samples/dotnetcore-docs-hello-world
$ cd dotnetcore-docs-hello-world
$ dotnet publish -c Release -o /app --self-contained false
```

You will obtain an ASP.NET dll named `/app/HelloWorld.dll`. The Dockerfile will
look like:

```Dockerfile
FROM ubuntu/dotnet-aspnet:8.0-24.04_stable

RUN mkdir /app
ADD /app /app

EXPOSE 8080

ENTRYPOINT ["dotnet", "/app/HelloWorld.dll"]
```

Build the image:

```sh
$ docker build -t HelloWorld .
```

Run your application container:

```sh
docker run -p 8080:8080 HelloWorld
...
```

Access your web app at `http://localhost:8080`.
