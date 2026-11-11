# dotnet-runtime

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
	oci-archive:dotnet-runtime_8.0_amd64.rock \
	docker-daemon:dotnet-runtime:8.0
```

The image has `/usr/bin/dotnet [--info]` as entrypoint.

```sh
$ docker run --rm dotnet-runtime:8.0
2024-03-14T13:11:06.286Z [pebble] Started daemon.
2024-03-14T13:11:06.301Z [pebble] POST /v1/services 7.557692ms 202
2024-03-14T13:11:06.301Z [pebble] Started default services with change 1.
2024-03-14T13:11:06.308Z [pebble] Service "dotnet-runtime" starting: /usr/bin/dotnet [ --info ]
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] Host:
2024-03-14T13:11:06.628Z [dotnet-runtime]   Version:      8.0.2
2024-03-14T13:11:06.628Z [dotnet-runtime]   Architecture: x64
2024-03-14T13:11:06.628Z [dotnet-runtime]   Commit:       1381d5ebd2
2024-03-14T13:11:06.628Z [dotnet-runtime]   RID:          ubuntu.24.04-x64
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] .NET SDKs installed:
2024-03-14T13:11:06.628Z [dotnet-runtime]   No SDKs were found.
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] .NET runtimes installed:
2024-03-14T13:11:06.628Z [dotnet-runtime]   Microsoft.NETCore.App 8.0.2 [/usr/lib/dotnet/shared/Microsoft.NETCore.App]
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] Other architectures found:
2024-03-14T13:11:06.628Z [dotnet-runtime]   None
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] Environment variables:
2024-03-14T13:11:06.628Z [dotnet-runtime]   DOTNET_ROOT       [/usr/lib/dotnet]
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] global.json file:
2024-03-14T13:11:06.628Z [dotnet-runtime]   Not found
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] Learn more:
2024-03-14T13:11:06.628Z [dotnet-runtime]   https://aka.ms/dotnet/info
2024-03-14T13:11:06.628Z [dotnet-runtime] 
2024-03-14T13:11:06.628Z [dotnet-runtime] Download .NET:
2024-03-14T13:11:06.628Z [dotnet-runtime]   https://aka.ms/dotnet/download
2024-03-14T13:11:06.638Z [pebble] [change 1 "Start service \"dotnet-runtime\"" task] failed: cannot start service: exited quickly with code 0
```

### Building and running a .NET application

Assuming you have your .NET dll named `myapp.dll`, a simple execution using
this rock as base would be by creating this Dockerfile:

```Dockerfile
FROM ubuntu/dotnet-runtime:8.0-24.04_stable

ADD myapp.dll /

ENTRYPOINT ["dotnet", "/myapp.dll"]
```

Build the image:

```sh
$ docker build -t myapp .
```

Run your application container:

```sh
docker run myapp
...
```
