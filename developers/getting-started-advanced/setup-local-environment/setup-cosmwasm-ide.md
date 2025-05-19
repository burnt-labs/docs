---
description: Integrated Development Environment Setup for XION Smart Contract Development
---

# Set Up an Integrated Development Environment (IDE)

A well configured Integrated Development Environment (IDE) can significantly streamline your smart contract development. While there are many options available for Rust development, the following IDEs stand out as top choices:

* [**Visual Studio Code (VS Code)**](https://code.visualstudio.com/docs/languages/rust) – Provides the best overall experience with strong Rust support (Recommended).
* [**RustRover**](https://jetbrains.com/rust/) – A JetBrains IDE tailored specifically for Rust development, offering deep integration with Cargo, advanced debugging, and robust code navigation.
* [**Eclipse**](https://projects.eclipse.org/projects/tools.corrosion) – A versatile IDE with Rust support through plugins like RustDT, suitable for developers who prefer an extensible and customizable environment.

Among these, we recommend **VS Code** for its excellent Rust support, ease of configuration, and built-in frontend development capabilities, eliminating the need for multiple tools.



## Prerequisites

* Before proceeding, ensure that the **Rust compiler (rustc)** is installed. You can refer to the [Installation Guide](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment#rust) for step-by-step instructions on setting up Rust before continuing.



## Install Visual Studio Code (**VS Code)**

**VS Code** is a widely used, free, and open-source IDE known for its powerful features, including a rich code editor, built-in Git integration, and robust debugging tools. With an extensive marketplace of extensions, VS Code provides excellent support for various programming languages, including **Rust, Go, C++, Java, Python, and more**. It is available on **Windows, macOS,** and **Linux**.

Follow the steps below to install VS Code on your operating system:

{% tabs %}
{% tab title="Linux" %}
See the [Download Visual Studio Code](https://code.visualstudio.com/download) page for a complete list of available installation options.

To install via the terminal use one of the following methods based on your operating system:

*   **Debian/Ubuntu** (via `.deb` package):

    ```sh
    sudo apt install ./<file>.deb  # Replace <file> with the downloaded .deb file
    ```
*   **Fedora/RHEL** (via `.rpm` package):

    ```sh
    sudo rpm -i <file>.rpm  # Replace <file> with the downloaded .rpm file
    ```
*   **Snap (Universal Linux Install)**:

    ```sh
    sudo snap install --classic code
    ```
{% endtab %}

{% tab title="macOS" %}
1. Download the macOS version from the [VS Code website](https://code.visualstudio.com/).
2. Open the downloaded `.zip` file and move the **Visual Studio Code** app to the Applications folder.
3.  (Optional) Install the **Command Line Tools** by running:

    ```sh
    sudo ln -s /Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code /usr/local/bin/code
    ```

    This allows you to open VS Code from the terminal using the `code` command.
{% endtab %}
{% endtabs %}



## Configuring VS Code for Rust Development

To enable Rust support in VS Code you need to install the following extensions:

* **rust-analyzer**: Provides syntax highlighting, code completion, error checking, and inline documentation.
* **CodeLLDB**: Enables debugging capabilities for Rust and other compiled languages.

### **Installing rust-analyzer**

Follow these steps to install the extension:

1. Go to the **Extension** panel
2. In the **Search** field enter **rust-analyzer**
3. Click the **install** button to the bottom right of **rust-analyzer**

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

### **Installing CodeLLDB**

The **CodeLLDB** extension is a native debugger essential for debugging Rust applications.

Follow these installation steps:

1. Open VS Code and navigate to the **Extensions** panel.
2. Search for **CodeLLDB**.
3. Click the **Install** button next to **CodeLLDB**.

For comprehensive usage details, refer to the [CodeLLDB Manual](https://github.com/vadimcn/vscode-lldb).

<figure><img src="../../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

### Cosmy wasmy <a href="#cosmy-wasmy" id="cosmy-wasmy"></a>

[Cosmy Wasmy](https://marketplace.visualstudio.com/items?itemName=spoorthi.cosmy-wasmy) is a VS Code plugin designed to streamline the development and interaction with CosmWasm smart contracts. It comes pre-configured with popular CosmWasm testnets, making chain interactions seamless during testing. With Cosmy Wasmy, you can manage all interactions directly within VS Code, eliminating the need for the CLI.

For details on installation, configuration, and available commands and features, explore [Cosmy Wasmy](https://marketplace.visualstudio.com/items?itemName=spoorthi.cosmy-wasmy).
