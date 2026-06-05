---
description: How to build the xiond binary
---

# Build the Xion Daemon

If you want to build the `xiond` binary yourself, this section will show you how.

{% hint style="info" %}
:warning: **Development environment**

The Xion Daemon is written using [the Go programming language](https://go.dev/).

This guide assumes you have a pre-existing installation on your workstation.

Adequate installation and configuration of your environment is out of scope. Please refer to [the appropriate documentation](https://go.dev/doc/install) for assistance.
{% endhint %}

{% hint style="info" %}
:warning: **Go Versions**

We use Go version 1.22 internally at Burnt.

If you are building the binary yourself, please make sure you are using Go 1.22, as we have observed consensus-breaking behavior on nodes that are not running the same version as the majority of the network.\
A specific symptom of this would be an `AppHash` mismatch.
{% endhint %}

## Fetch the Source Code

* Use `git` to retrieve the [Xion](https://github.com/burnt-labs/xion) repository, whose default branch contains the latest and greatest code.

```
$ git clone https://github.com/burnt-labs/xion.git
```

* If you want a specific stable version, refer to [the Releases page](https://github.com/burnt-labs/burnt/releases).

```
$ git clone https://github.com/burnt-labs/xion.git -b vX.Y.Z
```

## Build the Binary

{% hint style="info" %}
If using Windows the following must be run using WSL (Windows Subsystem for Linux)
{% endhint %}

```bash
# If on windows run the following
sudo apt update && sudo apt install coreutils

# check if make is already installed
make --version

# if it's not installed run
sudo apt install make
```





* We provide a `Makefile` with several targets common to projects in the Cosmos ecosystem.

```
$ cd xion
$ make install
```

## Verify that the Binary was installed

* Assuming everything went well, the binary is now available in your `$PATH`

```
$ xiond version
$ xiond version --long
$ xiond --help
```
