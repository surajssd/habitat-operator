[![Build Status](https://circleci.com/gh/habitat-sh/habitat-operator.svg?style=svg)](https://circleci.com/gh/habitat-sh/habitat-operator)
[![Go Report Card](https://goreportcard.com/badge/github.com/habitat-sh/habitat-operator)](https://goreportcard.com/report/github.com/habitat-sh/habitat-operator)

# habitat-operator

This project is currently unstable - breaking changes may still land in the future.

## Overview

The Habitat operator is a Kubernetes controller designed to solve running and auto-managing Habitat Services on Kubernetes. It does this by making use of [`Custom Resource Definition`][crd]s.

To learn more about Habitat, please visit the [Habitat website](https://www.habitat.sh/).

For a more detailed description of the Habitat type have a look [here](https://github.com/habitat-sh/habitat-operator/blob/master/pkg/apis/habitat/v1beta2/types.go).

## Prerequisites

- Habitat `>= 0.52.0`
- Kubernetes cluster with version `1.8.x`, `1.9.x` or `1.10.x`
- Kubectl version `1.9.x` or `1.10.x`

## Installing

    go get -u github.com/habitat-sh/habitat-operator/cmd/habitat-operator

## Building manually from source directory

First clone the operator:

    git clone https://github.com/habitat-sh/habitat-operator.git
    cd habitat-operator

Then build it:

    make build

Note: Make sure the source directory is in your `$GOPATH` before you execute the above command.

## Usage

### Running outside of a Kubernetes cluster

Start the Habitat operator by running:

    habitat-operator --kubeconfig ~/.kube/config

If you built the operator manually, you'll have to specify the path to the binary. So from the root of the source directory, run:

    ./habitat-operator --kubeconfig ~/.kube/config

### Running inside a Kubernetes cluster

#### Building image from source

First build the image:

    make image

This will produce a `habitat/habitat-operator` image, which can then be deployed to your cluster.

The name of the generated docker image can be changed with an `IMAGE` variable, for example `make image IMAGE=mycorp/my-habitat-operator`. If the `habitat-operator` name is fine, then a `REPO` variable can be used like `make image REPO=mycorp` to generate the `mycorp/habitat-operator` image. Use the `TAG` variable to change the tag to something else (the default value is taken from `git describe --tags --always`) and a `HUB` variable to avoid using the default docker hub.

#### Using release image

Habitat operator images are located [here](https://hub.docker.com/r/habitat/habitat-operator/), they are tagged with the release version.

#### Deploying Habitat operator

##### Cluster with RBAC enabled

Make sure to give Habitat operator the correct permissions, so it's able to create and monitor certain resources. To do it, use the manifest files located under the examples directory:

    kubectl create -f examples/rbac

For more information see [the README file in RBAC example](examples/rbac/README.md)

##### Cluster with RBAC disabled

To deploy the operator inside the Kubernetes cluster use the Deployment manifest file located under the examples directory:

    kubectl create -f examples/habitat-operator-deployment.yml

### Deploying an example

To create an example service run:

    kubectl create -f examples/standalone/habitat.yml

This will create a single-pod deployment of an `nginx` Habitat service.
More examples are located in the [example directory](https://github.com/habitat-sh/habitat-operator/tree/master/examples/).

## Contributing

### Dependency management

This project uses [go dep](https://github.com/golang/dep/) `>= v0.4.1` for dependency management.

If you add, remove or change an import, run:

    dep ensure

### Testing

To run unit tests locally, run:

    make test

Clean up after the tests with:

    make clean-test

Our current setup does not allow e2e tests to run locally. It is best run on a [CI setup with Google Cloud](/doc/ci-gcp-setup.md).

### Code generation

If you change one of the types in `pkg/apis/habitat/v1beta2/types.go`, run the code generation script with:

    make codegen

[crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/extend-api-custom-resource-definitions/
