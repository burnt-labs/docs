# Setting up your Local Development Environment for XION

A properly configured local development environment is essential for building and testing smart contracts on XION. This guide covers the necessary tools, dependencies, and configurations needed to get started.



The following tools will be installed:

* [**Rust & Cargo**](installation-prerequisites-setup-local-environment.md#rust) – The programming language and package manager used for writing and compiling smart contracts.
* [**cargo-generate**](installation-prerequisites-setup-local-environment.md#cargo-generate) – A tool for quickly setting up new Rust projects from templates.
* [**Go**](installation-prerequisites-setup-local-environment.md#go) – Required for interacting with blockchain tooling and dependencies.
* [**Docker**](installation-prerequisites-setup-local-environment.md#docker) – Used for optimizing Rust smart contracts via the Rust Optimizer.
* [**xiond**](installation-prerequisites-setup-local-environment.md#xiond) – The XION blockchain daemon for running a local node, deploying smart contracts, and interacting with the blockchain.

Each section provides step-by-step installation instructions for macOS, Debian-based Linux, Red Hat-based Linux and Arch Linux.

Once these dependencies are installed, you will have everything needed to start developing on XION.



## **Prerequisites**

* A Unix-like operating system (Linux, macOS, or Windows Subsystem for Linux). If you're using Windows, refer to this [guide](https://learn.microsoft.com/en-us/windows/wsl/install) for installing Windows Subsystem for Linux (WSL).



## **Rust**

[Rust](https://www.rust-lang.org/) is the programming language for developing smart contracts on XION. Known for its performance, security, and memory safety, Rust is well-suited for blockchain development. To build, test, and deploy smart contracts, developers need a properly configured Rust toolchain.

The recommended way to install Rust is through [**rustup**](https://rustup.rs/), the official Rust toolchain installer.

{% tabs %}
{% tab title="macOS" %}
Install Rust using Homebrew:

`brew install rustup`

`rustup-init`



Follow the on-screen instructions, then restart your terminal or run:

`source $HOME/.cargo/env`
{% endtab %}

{% tab title="Debian-based Linux" %}
Install required dependencies:

`sudo apt update && sudo apt install -y curl build-essential`



Then install Rust using rustup:

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`



Follow the setup instructions and restart your terminal or run:

`source $HOME/.cargo/env`
{% endtab %}

{% tab title="Red Hat-based Linux" %}
Install required dependencies:

`sudo dnf install -y curl gcc-c++ make`



Then install Rust:

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`



Follow the setup instructions and restart your terminal or run:

`source $HOME/.cargo/env`
{% endtab %}

{% tab title="Arch Linux" %}
Install Rust using Pacman:

`sudo pacman -S rustup`



Then initialize Rust:

`rustup default stable`
{% endtab %}
{% endtabs %}

### **Verifying Installation**

After installation, verify **Rust** is correctly installed:

```
rustc --version
```

You should see output similar to the following::

```
rustc 1.82.0-nightly (c1a6199e9 2024-07-24)
```



## Cargo

Cargo is Rust's package manager and build system, essential for compiling and managing dependencies in XION smart contract development. When installing Rust using **rustup** which we did earlier, Cargo is automatically included. However, if Cargo is missing or needs to be installed separately, follow the steps below.

### **Check if Cargo is Installed**

Run the following command to check if Cargo is already installed:

```
cargo --version
```

If Cargo is installed, you should see an output similar to:

```
cargo 1.82.0-nightly (5f6b9a922 2024-07-19)
```

If Cargo is missing, follow the installation steps below.

{% tabs %}
{% tab title="macOS" %}
If Rust is installed via **rustup**, install Cargo with:

`rustup component add cargo`
{% endtab %}

{% tab title="Debian-based Linux" %}
`sudo apt update && sudo apt install -y cargo`
{% endtab %}

{% tab title="Red Hat-based Linux" %}
`sudo dnf install -y cargo`
{% endtab %}

{% tab title="Arch Linux" %}
`sudo pacman -S cargo`
{% endtab %}
{% endtabs %}



## **Cargo Generate**

**cargo-generate** is a Rust tool that simplifies the creation of new Rust projects from pre-existing templates. It is particularly useful for setting up smart contract projects on XION by automating the scaffolding process.

Execute the following command to install **cargo-generate**:

```
cargo install cargo-generate
```

### **Verifying Installation**

After installation, confirm that `cargo-generate` is working correctly:

```
cargo generate --version
```

If cargo-generate is installed, you should see an output similar to:

```
cargo generate-generate 0.17.3
```



## **Go**

**Go** is required for certain blockchain development tasks, including working with XION’s tooling and dependencies for example `xiond`. Follow the installation steps below for your operating system:

{% tabs %}
{% tab title="macOS" %}
`brew install go`
{% endtab %}

{% tab title="Debian-based Linux" %}
`sudo apt update && sudo apt install -y golang`
{% endtab %}

{% tab title="Red Hat-based Linux" %}
`sudo dnf install -y golang`
{% endtab %}

{% tab title="Arch Linux" %}
`sudo pacman -S go`
{% endtab %}
{% endtabs %}

Alternatively, download and install Go manually from the [official website](https://go.dev/doc/install).

### **Verifying Installation**

After installation, verify that Go is correctly installed:

```
go version
```

If **go** is installed, you should see an output similar to:

```
go version go1.22.3 darwin/arm64
```

To ensure **Go** modules work correctly, set up the Go environment:

```
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc
```

If you are using a different shell, replace `~/.bashrc` with the corresponding configuration file for your shell. For example, if you're using **Zsh**, replace `~/.bashrc` with `~/.zshrc`.

After setting up your environment, restart your terminal to apply the changes.



## Docker

Docker is required for optimizing Rust smart contracts using the [**Rust Optimizer**](https://github.com/CosmWasm/optimizer), which ensures that contracts are compiled efficiently for deployment on **XION**. It can also be used to run `xiond`. You can install Docker for your operating system [here](https://docs.docker.com/get-docker/).

{% hint style="warning" %}
For Linux users, it's recommended to run the Docker daemon in [**Rootless Mode**](https://docs.docker.com/engine/security/rootless/)**.**
{% endhint %}



## xiond

`xiond` is the core command-line tool for running and interacting with the **XION blockchain**. It serves as the blockchain daemon, enabling developers and node operators to manage network operations, deploy smart contracts, and interact with the XION ecosystem.

To install `xiond`, you can either download a [**pre-built binary**](installation-prerequisites-setup-local-environment.md#use-pre-built-binary)**,** [**build it from source**](installation-prerequisites-setup-local-environment.md#build-from-source) or utilize a **docker** build. Ensure that you use the release corresponding to the version of `xiond` being used on the network where you will be interacting with.

### **Use Pre-built Binary**

You will need to download the appropriate binary for your system architecture [https://github.com/burnt-labs/xion/releases/tag/v17.0.0](https://github.com/burnt-labs/xion/releases/tag/v17.0.0):

{% tabs %}
{% tab title="Linux (amd64)" %}
**Download the binary using**

```
wget https://github.com/burnt-labs/xion/releases/download/v17.0.0/xiond-17.0.0.tar.gz
```



**Verify the Integrity of the Binary**

Generate the SHA256 hash of the downloaded file:

```
sha256sum xiond_linux_amd64
```
{% endtab %}

{% tab title="macOS (arm64)" %}
Download the binary using:

```
wget https://github.com/burnt-labs/xion/releases/download/v17.0.0/xiond_17.0.0_darwin_arm64.zip
```



**Verify the Integrity of the Binary**

Generate the SHA256 hash of the downloaded file:

```
sha256sum xiond_linux_arm64
```
{% endtab %}
{% endtabs %}

The `sha256sum` command should generate a hash string (e.g., `d41d8cd98f00b204e9800998ecf8427e`).

Download the official checksum file:

```sh
wget https://github.com/burnt-labs/xion/releases/download/v17.0.0/xiond-17.0.0-checksums.txt
```

Compare the two hash strings to ensure they match. This confirms that the downloaded file is authentic and unaltered.

#### **Add Executable Permissions**

Make the binary executable:

```sh
chmod +x xiond_linux_amd64
```

#### **Move the Binary to a System Directory**

Move the binary to a directory in your `PATH` (e.g., `/usr/local/bin`) and rename it to `xiond`:

```sh
sudo mv xiond_linux_amd64 /usr/local/bin/xiond
```

After this, `xiond` should be available for use.

***

### Run xiond with Docker

Instead of installing `xiond` manually, you can run it using Docker.

#### **Download the Image from Docker Hub**

We publish Docker images to Docker Hub on every commit to the `main` branch. The images are tagged with their respective Git SHA.

```
docker pull burntnetwork/xion:latest
```

For the latest available tags, check out the [Docker Hub](https://hub.docker.com/r/burntnetwork/xion) page for this image.

#### **Obtain a Shell in the Docker Container**

Once the image is pulled, you can invoke the `xiond` binary inside a Docker container:

```
docker run -ti burntnetwork/xion:latest /bin/bash
```

From within the container, you can interact with `xiond`:

```
xiond version
xiond version --long
xiond --help
```

Using Docker ensures a clean and isolated environment for running `xiond`, eliminating the need for manual installation and dependency management.

***

### **Build from Source**

If downloading the pre-built binary is not an option, you can install `xiond` from source. Ensure that **Go**, **Git**, and **Make** are installed.

#### **Clone the Repository and Build `xiond`**

```sh
git clone https://github.com/burnt-labs/xion.git
cd xion
git checkout main
make install
```

This will install the `xiond` binary to your `GOPATH`.

#### **Verify Installation**

Check that `xiond` is properly installed:

```sh
xiond version
```

If successful, `xiond` is now ready for use.
