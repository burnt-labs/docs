# Set up your XION Mobile Development Environment

To build a mobile app on **XION** with **React Native** and **Expo**, your development environment must support either **Android** or **iOS**. This guide walks you through the steps to initialize a **React Native** mobile app with **Expo** and configure it to work with XION using the `@burnt-labs/abstraxion-react-native` package and have it running on either **Android** or **iOS**.

The following is also a very useful [guide](https://docs.expo.dev/guides/local-app-development) that will help you set up your local development environment for **Expo**.



## Android Setup

### Install Java Development Kit

The Java Development Kit (JDK) is required for Android development. The following instructions will help you to install JDK on your operating system.

{% tabs %}
{% tab title="macOS" %}
Use Homebrew to install the **Azul Zulu OpenJDK**, which supports both **Intel** and **Apple Silicon** Macs.

In your terminal, run:

```bash
brew install --cask zulu@17
```

Once the installation is complete, set the `JAVA_HOME` environment variable by adding the following to your `~/.bash_profile` (or `~/.zshrc` if you're using Zsh):

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home
```
{% endtab %}

{% tab title="Linux" %}
You can get [OpenJDK](http://openjdk.java.net/) either through your system’s package manager or by downloading it directly from [AdoptOpenJDK](https://adoptopenjdk.net/).
{% endtab %}

{% tab title="Windows" %}
#### Requirements

Before proceeding, ensure you have a package manager like [**Chocolatey**](https://chocolatey.org/install) set up on your system.

#### Install Required Tools

To install the Java SE Development Kit (JDK), run the following command in your terminal:

```bash
choco install -y microsoft-openjdk17
```
{% endtab %}
{% endtabs %}

### **Install Android Studio**

* Download: [Android Studio](https://developer.android.com/studio)
* During setup, install:
  * **Android SDK**
  * **Android SDK Platform-Tools**
  * **Android Virtual Device (AVD) Manager**
* Go to **Tools** > **SDK Manager** menu item and within that window go to **Settings** > **Languages & Frameworks** > **Android SDK**. From the SDK Platforms tab, select the latest Android version (API level).
* Click on the **SDK Tools** tab and make sure you have at least one version of the Android SDK Build-Tools and Android Emulator installed.



### **Set Up Android Emulator**

* Open **Android Studio > Device Manager**
* Create a new virtual device (recommended: Pixel 9 with API 35+)



### **Add Environment Variables (macOS/Linux)**

Add to your shell config (e.g., `~/.bashrc` or `~/.zshrc`):

```bash
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

Then run:

```bash
source ~/.zshrc # or your respective shell file
```



## iOS Setup (macOS only)

### **Install Xcode**

* Download from the [Mac App Store](https://apps.apple.com/us/app/xcode/id497799835)
* Install **Command Line Tools** via your terminal by executing:

```bash
xcode-select --install
```

### Install an iOS Simulator

To install an iOS Simulator, open **Xcode** > **Settings**... > **Components**, and under **Platform Support** > **iOS** ..., click **Get**.



For more detailed guidance on configuring your development environment, refer to the official Expo setup guide: [https://docs.expo.dev/get-started/set-up-your-environment/](https://docs.expo.dev/get-started/set-up-your-environment/).



## Next Steps

Now that your project is set up, the next guide will walk you through **authenticating users with Meta Accounts** using Abstraxion.
