# Setting Up Jenkins in Kubernetes without Privileges

This repo has been set up to hold and maintain resources related to setting up an instance of Jenkins inside of a Kubernetes-environment where the user does not have any privileges or access to the host-docker.

It includes a version of [Jenkins](https://jenkins.io/) (2.226), the [Blue Ocean Plugin](https://jenkins.io/projects/blueocean/) (for visualizing parallel pipeline stages), and [GenuineTools' IMG](https://github.com/genuinetools/img) (for the creation of Docker images).

The [Dockerfile](Dockerfile-Jenkins) has been included to show how the image has been set up. The image itself can be found [here](https://hub.docker.com/repository/docker/frankvhoof/customjenkins).

**NOTE: the Kubectl-binary in the image has NOT been set up with a config. Configuration for the Kubernetes-Environment of choice can be done with the post-install instructions**




## Instructions

[Installation Instructions](docs/Setup-Instructions.md)

[Post-Install Instructions](docs/Configuration-Instructions.md)

[Setting up a Build-Slave](docs/Build-Slave-Setup.md)