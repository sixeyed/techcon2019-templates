# Arm NN - Machine Learning SDK

A machine learning example which runs TensorFlow models in a Docker container on Arm.

This sample shows how to use `buildx` to package a runnable TensorFlow model, and use an existing Docker Hub image for the SDK.

## Pre-requisites

This is Part 2 of the workshop. You should have already completed the following steps from Part 1:

- Logged into Docker Hub with your username `{{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}`
- Created a Docker context to use your remote A1 instance
- Created a `buildx` builder using your remote A1 instance

## Usage instructions

Here are the steps to run the demo. 

Start by opening a terminal in VS Code with `Ctrl-'` (or the menu _Terminal...New Terminal_).

### Switch to the A1 builder

You can use your A1 instance to compile the app. This is a good example where local building and instruction translation is not as good as A1 performance.

Use the A1 builder you created in Part 1:

```
docker buildx use a1
```

### Compile and package the app

Change to the directory with the NN code, and use `buildx` to package a Docker image for 32-bit and 64-bit Arm processors:

```
cd ml-armnn

docker buildx build --platform linux/arm64,linux/arm/v7 --push -t {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel -f Dockerfile .
```

> This will ask for your A1 password - Docker uses SSH for the connection to the A1 instance

The build will take a few minutes. While it runs have a look at the [Dockerfile](./Dockerfile) it's using. It uses the public [armnn/sdk](https://hub.docker.com/r/armnn/sdk/tags) image on Docker Hub to build the model.

### Check the build

Docker `buildx` pushes both variants to Docker Hub. You can see them in your [{{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/c-hello-world](https://cloud.docker.com/repository/docker/{{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel/) repository.

You can also inspect the image using `buildx` to check the supported architectures:

```
docker buildx imagetools inspect {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel
```

### Run the app on A1

You can use your A1 instance to run the ML app in a container on AWS.

Change to use the Docker context for your A1 machine that you created in Part 1:

```
docker context use a1
```

Now run the app in a container:

```
docker container run --rm  {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel
```

> You'll need your A1 password again here

You can pass in parameters to configure how many MNIST digits to run. The default is 10. Powerful Arm CPUs can run more digits. 

Still using the A1 instance, try 100 digits:

```
docker container run --rm  {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel 1 100
```

### Run locally on Docker Desktop

The image is built for Arm, but you can run it on your Intel laptop using Docker Desktop's emulation.

Switch back to your local Docker context and run the same container image:

```
docker context use default

docker container run --platform linux/arm64 --rm  {{range .Services}}{{if eq "docker-hub" .ID}}{{.Parameters.username}}{{end}}{{end}}/armnn-rel
```

> You can experiment with different numbers of digits on A1 and your local laptop to compare performance


## Next step - run on Raspberry Pi

Now you can return to the [main workshop instructions](https://armtechcon2019.s3.amazonaws.com/index.html) to see how to run the ML app on your Raspberry Pi.