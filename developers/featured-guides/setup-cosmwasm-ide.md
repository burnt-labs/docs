---
description: Integrated Development Environment Setup
---

# Setup CosmWasm IDE

This tutorial will guide you through the process of setting up an Integrated Development Environment (IDE) for developing CosmWasm smart contracts. Since CosmWasm contracts are currently built using Rust, this tutorial will focus on setting up your IDE for Rust development.

## Prerequisites

Prior to continuing, refer back to the [Installation](installation-prerequisites-setup-local-environment.md) guide to install the Rust compiler (rustc) before proceeding.

## Install Visual Studio Code

Visual Studio Code, commonly referred to as VS Code, stands as a widely embraced, cost-free, and open-source integrated development environment (IDE). It boasts a robust source code editor, seamless Git integration, and versatile debugging capabilities, greatly amplified by an extensive library of extensions that augment its functionality across an array of programming languages, such as Rust, Go, C++, Java, Python, and PHP. This versatile IDE finds its home on Windows, macOS, and Linux operating systems, catering to a diverse developer audience.

## Please refer to the steps below for your respective operating system:

### _For macOS:_

_For additional setup information for macOs, see_ [_Visual Studio Code on macOS_](https://code.visualstudio.com/docs/setup/mac)_._

1. Download Visual Studio Code for macOS.
2. Open the browser's download list and locate the downloaded app or archive.
3. If archive, extract the archive contents. Use double-click for some browsers or select the 'magnifying glass' icon with Safari.
4. Drag Visual Studio Code.app to the Applications folder, making it available in the macOS Launchpad.
5. Open VS Code from the Applications folder, by double-clicking the icon.
6. Add VS Code to your Dock by right-clicking on the icon, located in the Dock, to bring up the context menu and choosing Options, Keep in Dock.

### _For Windows:_

_For additional setup information for Windows, see_ [_Visual Studio Code on Windows_](https://code.visualstudio.com/docs/setup/windows)_._

1. Download the Visual Studio Code installer for Windows.
2. Once it is downloaded, run the installer (VSCodeUserSetup-{version}.exe).

## Configure v. Code

To activate Rust support within VS Code, it is essential to install a couple of extensions, specifically rust-analyzer and CodeLLDB. These extensions furnish crucial features such as syntax highlighting, code completion, debugging tools, and other indispensable functionalities.

### Rust-Analyzer

The rust-analyzer extension serves as a language server tailored for the Rust programming language. Its primary role is to deliver vital features, including code completion, error validation, and documentation assistance when working with Rust code in the VS Code environment.

_**The following is listed to help guide the installation of the extension**_

1. Go to the Extension panel
2. In the Search field enter rust-analyzer
3. Click the install button to the bottom right of rust-analyzer

_For additional information as to the benefits and features of rust-analyzer, see the rust-analyzer_ [_User Manual_](https://rust-analyzer.github.io/manual.html)_._

### Code LLBD

The CodeLLDB extension serves as a native debugger designed for the purpose of debugging Rust and other compiled programming languages.

_**The following is listed to help guide the**_ _**installation of the extension**_

1. Go to the Extension panel
2. In the Search field enter CodeLLDB
3. Click the install button to the bottom right of CodeLLDB

_For full details on how to use the CodeLLDB extension, please see the_ [_Manual_](https://github.com/vadimcn/codelldb/blob/v1.9.0/MANUAL.md)_._

## The Basic Rust Program

Let's check your ability to write and run Rust programs in VS Code. Begin by creating a new file named "main.rs" and then insert the following code into the file:

```
fn main() {
    println!("Hello, XION!");
}
```

Launch a terminal within VS Code by going to **Terminal -> New Terminal** and execute the following command to compile the program:

```
rustc main.rs
```

To execute the program, use the following command:

```
./main
```

{% hint style="info" %}
on Windows, this command may be **.**`/main.exe.`
{% endhint %}

The output of the program should be: `Hello, XION!`

_Syntax highlighting should be in effect alongside code completion, error checking, among other features_

## Cosmy Wasmy

[Cosmy Wasmy](https://marketplace.visualstudio.com/items?itemName=spoorthi.cosmy-wasmy) is a VS Code plugin designed to streamline the development and interaction with CosmWasm smart contracts. It comes pre-configured with the most widely used testnets for CosmWasm chains, ensuring seamless chain interactions during testing. With Cosmy Wasmy, you can perform all these interactions directly within VS Code, eliminating the need to utilize the command-line interface (CLI).

_For more information about installation, configuration, and the commands and features available, check out_ [_Cosmy Wasmy_](https://marketplace.visualstudio.com/items?itemName=spoorthi.cosmy-wasmy)_._

## CosmWasm IDE

The [CosmWasm IDE](https://github.com/oraichain/cw-ide-vscode) is a tool that simplifies the development and deployment workflows for CosmWasm smart contracts. It seamlessly integrates with popular platforms like VSCode, Gitpod, and Keplr to provide a user-friendly yet robust environment for building, deploying, and engaging with CosmWasm smart contracts. Through Gitpod, CosmWasm developers can even create smart contracts directly within their web browsers. The ongoing maintenance of this tool is a collaborative effort led by Oraichain and the CosmWasm community.

_**The following three sub-repositories make CosmWasm IDE:**_

* [**CosmWasm Gitpod** ](https://github.com/oraichain/cw-ide-webview)acts as a Gitpod builder, automating the setup of a comprehensive development environment. This environment includes the installation of Rust, a web-based version of VS Code, essential VS Code extensions, and seamless integration with the Keplr wallet. By utilizing this repository, CosmWasm developers can bypass the time-consuming process of manually setting up tools and libraries, ensuring a convenient and secure experience when deploying contracts through Keplr.
* The [**CosmWasm IDE extension**](https://github.com/oraichain/cw-ide-vscode) for VS Code streamlines the process of creating and deploying CosmWasm smart contracts. It provides a set of essential functionalities that can be easily accessed through a few simple button clicks directly within VS Code.
* The [**webview of the CosmWasm IDE extension**](https://github.com/oraichain/cw-ide-webview) is a React application that acts as an overlay for the CosmWasm IDE Extension. It manages interactions with the Keplr wallet and presents user-friendly input interfaces for deploying and interacting with smart contracts. Furthermore, it provides the capability to incorporate custom networks as needed.

_You can find additional CosmWasm IDE documentation on the official_ [_Oraichain documentation_](https://docs.orai.io/developers/cosmwasm-ide/tutorial-01) _site._



