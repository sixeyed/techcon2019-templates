# Simple multi-architecture Docker example C program

A simple C program which prints the host architecture of the running container.

It shows how to use `docker buildx` to create images for multiple architectures and run them.

## Usage instructions

Here are the steps to run the demo. 

Start by opening a terminal in VS Code with `Ctrl-'` (or the menu _Terminal...New Terminal_).

### Login to Docker Hub

Open a terminal in VS Code with `Ctrl-'` (or the menu _Terminal...New Terminal_).

Login with your Docker ID credentials so you can push images to Docker Hub:

```
docker login --username {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}
```

### Build the app

Change to the source code directory:

```
cd c-hello-world
```

[Docker buildx](https://docs.docker.com/buildx/working-with-buildx/) is built into Docker Desktop. It uses "builders" to package applications for different architectures. Create a builder which can cross-compile the app:

```
docker buildx create --use --name local
```

> This creates a builder on Docker Desktop called "local", and sets it to be the current builder

Now build the app and package it to run on three different architectures:

```
docker buildx build --platform linux/arm64,linux/amd64,linux/arm/v7 -t {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world --push .
```

> Docker `buildx` builds all three platforms concurrently, using emulation for the Arm architectures

### Check the build

Docker `buildx` pushes all three variants to Docker Hub. You can see them in your [{{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world](https://cloud.docker.com/repository/docker/{{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world/) repository.

You can also inspect the image using `buildx` to check the supported architectures:

```
docker buildx imagetools inspect {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world
```

### Run the app on your native architecture

Docker will use the right version of the image for the architecture you have, when you run a container from a multi-architecture image. 

This will run the 64-bit Intel version on your laptop:

```
docker container run --rm {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world
```

> You should see the output state you're running an `x86_64` Intel platform

### Run with Arm emulation

You can also run with emulated Arm architecture using Docker Desktop. You need to remove your original image, and then run with a specific platform.

This runs as a 64-bit Arm platform:

```
docker image rm {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world

docker container run --platform linux/arm64 --rm {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world
```

> Now the output shows you're running an `aarch64` Arm platform

And as a 32-bit Arm platform:

```
docker image rm {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world

docker container run --platform linux/arm/v7 --rm {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world
```

> Now the output shows you're running an `armv7l` Arm platform


## Next step - build on AWS A1

Now you've seen Docker `buildx` with emulation, it's time to build using a real Arm processor in the cloud.

> Move on to [building on Amazon Web Service A1](AWS-A1.md)
