# Building Arm Docker images using AWS A1 instances

Your A1 instance is a VM in the cloud powered by an Arm processor. You can use it to natively build Arm Docker images, using the same Dockerfiles you use locally.

## Setup a connection to the A1 machine

Your A1 server is already running Docker. You can use the `docker` command line on your laptop to work with the remote Docker server by creating a [Docker context](https://docs.docker.com/engine/reference/commandline/context/).

Run this to create a context pointing to your A1 instance, using SSH to connect:

```
docker context create a1 --docker "host=ssh://{{range .Services}}{{if eq "arm-server" .ID}}{{.Parameters.username}}{{end}}{{end}}@{{range .Services}}{{if eq "arm-server" .ID}}{{.Parameters.ipAddress}}{{end}}{{end}}"
```

> You can switch between your local and remote Docker machines using the CLI - you'll do that later in this step


## Create a builder on the A1 instance

You created a local `buildx` builder in the last step, which ran builds on Docker Desktop. `buildx` can also manage remote build servers. You can create a builder which executes builds on the A1 machine.

Run this to create a builder which will use the context of the A1 machine to build images:

```
docker buildx create --use --name a1 --platform linux/arm/v7,linux/arm64 a1
```

> This creates the builder for the A1 machine, and the `--use` flag switches `buildx` to use it as the current builder

## Use the A1 builder to build the same app

You can use the same Dockerfile you've already worked with to build the app on the A1 builder. When you run a build against a remote builder, Docker sends all the build data up to the server. The Dockerfile and all the files it uses - like the source code - get sent to the server, so it can execute the Dockerfile instructions.

Run this to build the app on the A1 builder, targeting 32-bit and 64-bit Arm images:

```
docker buildx build --platform linux/arm64,linux/arm/v7 -t {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world-a1 --push .
```

> This will ask for your A1 password - Docker uses SSH for the connection to the A1 instance

The build is pretty much the same `docker buildx` command, except now it runs on the A1 instance in the cloud.

## Try the app on the A1 machine

The images have been pushed from the A1 machine to Docker Hub. You can switch the Docker context to use that A1 machine, and run the app as a Docker container in the cloud.

Switch the current Docker context to use the A1 instance:

```
docker context use a1
```

Now run the app - the A1 instance will pull the 64-bit Arm image you've just built, run the app locally and send the output back to your laptop:

```
docker container run {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world-a1
```

> You can switch back to use your local Docker Desktop now, with `docker context use default`

## Next step - run on Raspberry Pi

Now you can return to the [main workshop instructions](https://armtechcon2019.s3.amazonaws.com/index.html) to see how to configure your Raspberry Pi and run the app you've built on the A1 instance.