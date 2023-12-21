---
description: How to download pre-built versions of the xiond binary
---

# Download the Xion Daemon

We provide pre-built versions of the `xiond` binary.&#x20;

This section shows how to avail yourself of these versions, should you prefer.

{% hint style="info" %}
:warning: **Development environment**

We use [Docker](https://www.docker.com/) and [Kubernetes](https://kubernetes.io/) internally at Burnt Labs.

This guide assumes that you are familiar with these technologies, and have a pre-existing installation on your workstation.

Adequate installation and configuration of your environment is out of scope. Please refer to the [appropriate](https://docs.docker.com/get-docker/) [documentation](https://kind.sigs.k8s.io/docs/user/quick-start/) for assistance.
{% endhint %}

## Download the image from Docker Hub

* We publish Docker images to Docker Hub on every commit to `main`.
* The image versions are tagged with the short Git SHA.
* We do not use the `latest` tag, to avoid confusion and mistakes.

```
$ docker pull burntnetwork/xion:sha-abcdef9
```

## Obtain a shell in the Docker container

* We can now invoke the `xiond` binary inside the container.

```
$ docker run -ti burntnetwork/xion:sha-abcdef9 /bin/bash
# xiond version
# xiond version --long
# xiond --help
```

