# Updating Templates

Application templates are rendered using Docker containers. This repo has the Dockerfiles and content to build the images which render the templates.

> You'll need access to the [techcon2019](https://hub.docker.com/orgs/techcon2019) organization on Docker Hub to push template images with content updates, and to the [sixeyed/techcon2019-templates](https://github.com/sixeyed/techcon2019-templates) repo to push changes to the app template config.

## Structure

The content for the templates is in the `services` directory. There are folders for each demo app.

The folders contain an `assets` directory with all the source code and the README docs. 

The source code gets rendered as-is. The README doc uses variables in Go templating syntax (lots of curly braces) to get the user's A1 IP address and Docker Hub username at runtime, so each user's instructions are tailored to them.

## Updating content

Make any changes under the service's assets folder. Then from the root directory build all the services:

```
docker-compose build
```

And push to Docker Hub:

```
docker-compose push
```

## Updating the template configuration

The app templates are put together from the individual services. This configuration is in [techcon-2019-templates.yaml](techcon-2019-templates.yaml).

You can update that file and push to GitHub. As long as user's reference the library from the GitHub repo (as specified in the [README](./README.md)), they will see the latest changes when you push.
