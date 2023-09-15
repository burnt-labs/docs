# Installation

## **Getting Started**

To get started developing on XION, you will need to have the following prerequisites in mind and ready to use.

* [Golang](https://go.dev/doc/install)
* [Rust](https://www.rust-lang.org/)
* [Cargo](https://doc.rust-lang.org/cargo/commands/cargo-install.html)
* [Docker](https://docs.docker.com/get-docker/)
* [WASM](https://webassembly.org/)

## Golang

Golang was designed to address some of the challenges faced by developers in building scalable, concurrent, and efficient software. It’s a simple, open-source language and has gained popularity in the coding world due to its simplicity, performance, and strong support for concurrency.

## Rust

Rust is a programming language that has gained significant attention and popularity, particularly for systems programming and low-level development. Due to Rust’s memory safety guarantees and performance advantages, it is increasingly being adopted for projects where both safety and efficiency are critical, such as operating systems, game engines, web browsers, and networking software. Its modern design and innovative features make it a powerful tool for a variety of programming tasks.

### Installing Rust

Provided is a step-by-step instructions for installing Rust on both macOS and Windows systems:

#### _Installing Rust on macOS:_

1. Open a terminal window. You can find the Terminal application in the "Utilities" folder within the "Applications" directory.
2. Copy and paste the following command into the terminal and press Enter to initiate the installation of rustup (the Rust toolchain installer):

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

3. Follow the on-screen prompts. You'll be asked to review the installation options and confirm the installation by pressing Enter.
4. After the installation is complete, the terminal will display a message indicating that Rust has been installed. The rustup tool has also added the Rust binaries to your system's PATH.
5. Close and reopen the terminal to start using Rust. You can verify the installation by typing the following command:

`rustc --version`

#### _Installing Rust on Windows:_

1. Open a web browser and visit the following URL to download the rustup-init.exe installer:

[`https://rustup.rs/`](https://rustup.rs/)

2. Run the downloaded ‘r`ustup-init.exe`’ file. This will open a Command Prompt-like interface.
3. Follow the on-screen instructions:

Press **Enter** to start the installation.

Choose an installation type by pressing the corresponding key (e.g., 1 for the default installation).

The installer will download and install the necessary components. You might be asked to accept license terms during the installation.

4. Once the installation is complete, the installer will display a message indicating that Rust has been installed.
5. Close and reopen any Command Prompt or PowerShell windows to ensure the changes take effect.
6. To verify the installation, open a Command Prompt or PowerShell window and type:

`rustc --version`

With the instructions provided, a user should guide themselves through the process of installing Rust on both macOS and Windows systems. If the user encounters any issues, you can refer to the official Rust documentation for troubleshooting and more information: https://www.rust-lang.org/tools/install

## Cargo

Cargo is an essential tool for managing Rust projects, dependencies, and building ones applications. Cargo is the package manager for Rust, and is automatically installed alongside Rust when you use the rustup installation method. Therefore, if the user has followed the instructions provided to install Rust using rustup on macOS or Windows, the user will already have Cargo installed.

_Here's a quick recap of the steps:_

**macOS (Assuming you've installed Rust using rustup):**

Open a terminal window.

To verify that Cargo is installed, you can type the following command:

`cargo --version`

This should display the version of Cargo that is installed.



**Windows (Assuming you've installed Rust using rustup):**

Open a Command Prompt or PowerShell window.

To verify that Cargo is installed, you can type the following command:

`cargo --version`

This should display the version of Cargo that is installed.



## Docker

Docker is an open-source platform that automates the deployment, scaling, and management of applications inside lightweight, portable containers. Many use docker to package applications along with their dependencies and run them consistently across different environments, such as development, testing, and production.

### Installing Docker

To install Docker, a user can follow these general steps.&#x20;

**Open a terminal and update your package database:**

`sudo apt update`

**Install required packages to enable Docker repository usage:**

`sudo apt install apt-transport-https ca-certificates curl software-properties-c`ommon

**Add the Docker repository and key:**

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`

`echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list`

**Update the package database again and install Docker:**

`sudo apt update`

`sudo apt install docker-ce docker-ce-cli containerd.io`

**Start and enable Docker:**

`sudo systemctl start docker`

`sudo systemctl enable docker`

**Open a terminal and install required packages:**

`sudo yum install -y yum-utils device-mapper-persistent-data lvm2`

**Add the Docker repository:**

`sudo yum-config-manager --add-repo` [`https://download.docker.com/linux/centos/docker-ce.repo`](https://download.docker.com/linux/centos/docker-ce.repo)

**Install Docker:**

`sudo yum install docker-ce docker-ce-cli containerd.io`

**Start and enable Docker:**

`sudo systemctl start docker`

`sudo systemctl enable docker`

**For macOS:**

Download and install Docker Desktop for macOS from the official Docker website: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

**For Windows:**

Download and install Docker Desktop for Windows from the official Docker website: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

After installation, the user should be able to run the docker command in their terminal to manage Docker containers and images.



## WASM

WebAssembly, commonly known as the abbreviation as WASM, is a binary instruction format designed as a portable compilation target for high-performance web applications. It allows code to be executed efficiently and safely in web browsers, enabling developers to write web applications with near-native performance.



### Local Installations

_You need root access to run docker_. Make sure to follow all the steps with root access to prevent conflicts and confusions on the deployment process.

### Attention

For Linux users, it's recommended to run the Docker daemon in Rootless Mode

If you prefer, you can use Docker Engine instead of Docker Desktop.



## Start a local testnet

1. Run `docker compose up testnet`
2. On first run, it can take several minutes to build the images locally and start the testnet.
3. Install `xiond` cli locally following [these directions](xion-daemon.md)

Run the following to confirm the testnet is running:

```
$ xiond query bank total
```

Which will respond with the something resembling the following when the chain is operational

```
pagination:
  next_key: null
  total: "0"
supply:
- amount: "110215739277465"
  denom: uxion
```
