# Setting up your XION Developer Environment on Windows (WSL2 + Ubuntu)

This guide walks you through setting up a local XION developer environment on a Windows machine using **WSL2** with **Ubuntu**. This is the recommended setup for Windows users who want to work with `xiond`, build and interact with their CosmWasm smart contracts.



## Prerequisites

1. **Windows 10 or 11 (64-bit)**



## Install WSL2 with Ubuntu

If you don’t have **WSL2** set up, execute the following command in **PowerShell**:

```powershell
wsl --install
```

The `--install` comand performs the following actions:

* Set WSL2 as the default
* Downloads and installs the default Ubuntu Linux distribution. You will need to restart your machine during the installation process.

If you would want to install another Linux distribution other than Ubuntu or another version of Ubuntu you would first use the following command to get the available listing available:

```powershell
wsl --list --online
```

To change the distribution installed, enter:&#x20;

```powershell
wsl --install -d <Distribution Name>
```

Replace `<Distribution Name>` with the name of the distribution you would like to install.

Check to make sure WSL2 is set as default:

```powershell
wsl --status
```

This should display the following "**Default Version: 2**".

> If you're already using WSL1, you can upgrade to WSL2 using the following:

```powershell
wsl --set-version Ubuntu 2
```

You can list your installed Linux distributions and check the version of WSL each is set to by entering the command:

```powershell
wsl -l -v
```



## Set up your Linux username and password <a href="#set-up-your-linux-username-and-password" id="set-up-your-linux-username-and-password"></a>

Click the Windows **start menu** button and in the search field enter "**Ubuntu**".

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-01 145216.png" alt=""><figcaption></figcaption></figure>

Click the **Ubuntu app** to open the distribution and in the terminal that's loaded you will be asked to create a **User Name** and **Password** for the Linux distribution. This **User Name** and **Password** is specific to each separate Linux distribution that you install and has no bearing on your Windows user name.

<figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

## Update and upgrade packages <a href="#update-and-upgrade-packages" id="update-and-upgrade-packages"></a>

In the Ubuntu terminal use the following command to update the Ubuntu distribution to reduce the chance of a package not working:

```bash
sudo apt update && sudo apt upgrade
```

### Install Build Essentials and Development Tools

```bash
sudo apt install -y build-essential pkg-config libssl-dev curl git jq unzip wget
```



## Install Docker (for CosmWasm Optimizer)

Follow [Docker's official instructions for WSL2](https://docs.docker.com/desktop/windows/wsl/) or install Docker Desktop and enable **WSL2 backend**. The general steps include:

1. Download and install the latest version of [Docker Desktop for Windows](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe).
2. Follow the usual installation instructions to install Docker Desktop. Depending on which version of Windows you are using, Docker Desktop may prompt you to turn on WSL 2 during installation. Read the information displayed on the screen and turn on the WSL 2 feature to continue.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-01 155509 (1).png" alt=""><figcaption></figcaption></figure>

3. Start Docker Desktop from the **Windows Start** menu.
4. Navigate to **Settings**.
5.  From the **General** tab, select **Use WSL 2 based engine**..

    If you have installed Docker Desktop on a system that supports WSL 2, this option is turned on by default.
6. Select **Apply & Restart**.

Once installed, make sure docker is running. You can verify that docker is installed and accessible via the **Ubuntu** distribution by executing:

```bash
docker --version
```



## Setting up Visual Studio Code <a href="#use-visual-studio-code" id="use-visual-studio-code"></a>

Follow this step-by-step guide to [Get started using Visual Studio Code with WSL](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode), which includes installing the [Remote Development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack). This extension enables you to run WSL, SSH, or a development container for editing and debugging with the full set of Visual Studio Code features. Quickly swap between different, separate development environments and make updates without worrying about impacting your local machine.

Once VS Code is installed and set up, you can open your WSL project with a VS Code remote server by entering: `code .` .

_Be sure to add the period at the end of the command to open the current directory._

![VS Code with WSL extensions displayed](https://learn.microsoft.com/en-us/windows/wsl/media/vscode-remote-wsl-extensions.png)

## Optimize Contract: Example

In this example, we’ll walk through the steps to compile a Xion smart contract using the setup described above.

### **Clone the Repository** <a href="#clone-the-repository" id="clone-the-repository"></a>

Start the **Ubuntu** app, and in the terminal, use the following commands to clone the repository and navigate into the project directory:

```sh
git clone https://github.com/burnt-labs/cw-counter
cd cw-counter
```

{% hint style="info" %}
Make sure Docker is running.
{% endhint %}

Next, compile and optimize the smart contract using the [CosmWasm Optimizing Compiler](https://github.com/CosmWasm/optimizer). You need to have **Docker** running to execute the command below:

```sh
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/optimizer:0.16.0
```

Once compiled, the optimized contract will be available at:

```sh
./artifacts/cw_counter.wasm
```

### Use Visual Studio Code

To optimize the contract from within **Visual Studio Code**, open your project using the command:

```sh
code .
```

This will launch the project in Visual Studio Code. From there, open a terminal within the IDE and run the Docker command above.



## Install `xiond` Binary

You can now follow this [guide](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment#xiond) to install `xiond` within the Ubuntu environment. It's preferable to use the **Debian based** installer.

