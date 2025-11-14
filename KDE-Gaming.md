# Configuring Arch Linux with KDE Plasma: Essential Initial Setup

This guide walks you through the essential initial configuration of your Arch Linux system after installing KDE Plasma using `archinstall`. It focuses on optimizing your package manager, shell, and download sources. This guide is tailored for users with little to no prior Linux experience, so all steps are explained in detail.

> **Note**: These instructions assume you're comfortable using a terminal. If a command fails, the Arch Wiki ([https://wiki.archlinux.org/](https://wiki.archlinux.org/)) is an invaluable resource.

## Optimizing Pacman for Speed and Reliability

Pacman is Arch Linux's powerful package manager, used to install, update, and remove software. Configuring it correctly from the start ensures a smooth and efficient system. We'll adjust settings to enable faster downloads and easier-to-read output.

### Steps

1.  **Edit the `pacman.conf` file:**
    The main configuration file for `pacman` is located at `/etc/pacman.conf`. You need to open it with a text editor as the root user. `nano` is a simple, terminal-based editor that's ideal for this purpose.

    Open the file using `nano`:

    ```bash
    sudo nano /etc/pacman.conf
    ```

2.  **Enable useful options in the `[options]` section:**
    In `nano`, scroll down until you find the `[options]` section. This section controls Pacman's global behavior. Inside this section, uncomment (remove the `#` symbol) and/or add the following lines:

    ```plaintext
    [options]
    Color
    CheckSpace
    VerbosePkgLists
    ParallelDownloads = 15 # or $(nproc) for core count
    ILoveCandy
    ```

    Let's break down what each of these options does:

    *   `Color`: This enables colorized output in your terminal, making it much easier to scan `pacman`'s messages and identify important information.
    *   `CheckSpace`: This tells `pacman` to verify there's enough free disk space before attempting to install any new packages. This helps prevent potential errors if you're running low on space.
    *   `VerbosePkgLists`: This makes `pacman` display the old and new versions of packages during system updates, so you can see exactly what's being changed.
    *   `ParallelDownloads = 15`: This allows `pacman` to download multiple packages simultaneously, which can dramatically speed up the update process. A good starting point is 15, but you can increase or decrease this number depending on the speed of your internet connection. Alternatively, you can match this to the number of cores your CPU has by using the command `$(nproc)`
    *   `ILoveCandy`: This adds a progress bar during package extraction, because who doesn't love a little eye candy?

    After making these changes, save the file by pressing `Ctrl+O`, then press `Enter` to confirm the filename. Exit `nano` by pressing `Ctrl+X`.

## Supercharging Bash with Autocompletion

Bash is the command-line interpreter, or *shell*, that you use to interact with your Arch Linux system. Installing and configuring `bash-completion` will greatly improve your command-line experience by providing automatic suggestions as you type commands.

### Steps

1.  **Install `bash-completion`:**

    Install the `bash-completion` package with pacman:

    ```bash
    sudo pacman -S bash-completion --needed
    ```

2.  **Enable bash completion by modifying `~/.bashrc`:**
    The `~/.bashrc` file contains a set of commands that are executed every time you launch a new Bash terminal session. By adding the appropriate lines to this file, you can enable bash completion.

    Open the `~/.bashrc` file with `nano`:

    ```bash
    nano ~/.bashrc
    ```

    Add the following lines to the file:

    ```bash
    # Enable bash completion
    [ -f /usr/share/bash-completion/bash_completion ] && . /usr/share/bash-completion/bash_completion
    complete -cf sudo
    ```

    Save the changes by pressing `Ctrl+O`, then press `Enter`, and exit `nano` by pressing `Ctrl+X`.

3.  **Refresh your current terminal session:**

    The changes you made to `~/.bashrc` will only take effect in new terminal sessions. To apply the changes to your current session, run the following command:

    ```bash
    source ~/.bashrc
    ```
 

## Installing an AUR Helper: Paru

The Arch User Repository (AUR) is a community-driven repository containing package descriptions (PKGBUILDs) for software not directly available in the official Arch Linux repositories. An AUR helper simplifies the process of downloading, building, and installing these packages. While `yay` is another popular option, this guide will focus on `paru` due to its modern features and active development.

### Steps

1.  **Install the base development tools:**
   Before you can install any AUR helper, you need to install the base development tools that are required for compiling software from source.

   ```bash
   sudo pacman -S git base-devel --needed
   ```

   *   `git`: A version control system used to download the `paru` PKGBUILD.
   *   `base-devel`: A group of essential development tools, including `make`, `gcc`, and other utilities.

2.  **Clone the `paru` repository:**
   The PKGBUILD for `paru` is hosted on the AUR, so you need to clone the repository to your local machine.

   ```bash
   git clone https://aur.archlinux.org/paru.git
   ```

3.  **Build and install `paru`:**
   Navigate into the `paru` directory and use `makepkg` to build and install the package. The `-si` flags tell `makepkg` to automatically resolve dependencies and install the resulting package.

   ```bash
   cd paru
   makepkg -si --needed
   ```

   You will be prompted to confirm the installation.

4.  **Clean up (optional):**
   After the installation is complete, you can remove the `paru` source directory.

   ```bash
   cd ..
   rm -rf paru
   ```

5.  **Verify the installation:**
   To ensure that `paru` has been installed correctly, run the following command:

   ```bash
   paru --version
   ```

   This should display the version number of `paru`.

### Yay vs Paru
Both `yay` and `paru` are popular AUR helpers, but here's a quick comparison:

*   `paru` is written in Rust, offering potential performance and security benefits.
*   `paru` is actively maintained and incorporates modern features.
*   `yay` is also a solid choice and is widely used, but development is less active.

For most users, `paru` is an excellent choice due to its speed, features, and active development.


## Setting up GPU Drivers

Properly installed graphics drivers are essential for utilizing your GPU for gaming and other graphically intensive tasks. Without drivers, your system will rely on the CPU for graphics processing, resulting in poor performance. The steps to install the correct drivers depend on your GPU vendor (NVIDIA, AMD, or Intel).

### Steps

1.  **Identify your GPU:**
    Use the following command to identify your graphics card vendor:

    ```bash
    lspci | grep -i vga
    ```

    This command will output information about your graphics card, including the vendor (NVIDIA, AMD, or Intel). Make a note of the vendor, as you'll need it for the next step.

2.  **Install the appropriate drivers:**
    Install the drivers for your specific GPU vendor. It's also important to install the 32-bit versions of the drivers (if available) to ensure compatibility with older games and applications.

    *   **NVIDIA:**

        ```bash
        sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils --needed
        ```

    *   **AMD:**

        ```bash
        sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon --needed
        ```

    *   **Intel:**

        ```bash
        sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel --needed
        ```

3.  **Verify the installation:**
    Verify that your GPU drivers are loaded correctly by running:

    ```bash
    glxinfo | grep "OpenGL renderer"
    ```

    This command should output information about your OpenGL renderer. Look for the name of your graphics card vendor in the output, which confirms that the drivers are active.

> **Note**: Sometimes, installing GPU drivers can lead to issues such as a black screen or no signal after rebooting. If this happens, you may need to consult the Arch Wiki or other resources for troubleshooting steps specific to your hardware. Common solutions involve using a different kernel, modifying boot parameters, or using a different display manager.


## Installing Essential Tools

These are tools that are often used in this guide and are generally useful for managing your system. It is highly recommended to install them now.

### Steps

1.  **Install the packages:**
    Use the following command to install the essential packages:

    ```bash
    sudo pacman -S curl wget zip unzip nano net-tools dnsutils vulkan-tools firefox firefox-ublock-origin --needed
    ```

Here's a description of the use for each tool:

*   `curl` and `wget`: Command-line tools for downloading files from the internet.
*   `zip` and `unzip`: Tools for creating, extracting, and managing compressed files.
*   `nano`: A simple and easy-to-use terminal-based text editor.
*   `net-tools`: Provides essential networking tools like `ifconfig` and `route` (though `iproute2` is generally preferred these days).
*   `dnsutils`: A collection of utilities for querying DNS name servers (e.g., `nslookup`, `dig`).
*   `vulkan-tools`: Useful utilities for developing and debugging Vulkan graphics applications.
*   `firefox` and `firefox-ublock-origin`: A web browser and the uBlock Origin extension for ad-blocking and privacy.

## Finding the Fastest Download Servers: Optimizing Mirrors

Mirrors are servers that host Arch Linux packages. Using mirrors that are geographically close to you and have good bandwidth ensures quicker and more reliable downloads. To help you find the best mirrors, we'll use `reflector`.

### Steps

1.  **Install `reflector`:**

    Install the `reflector` package with pacman:

    ```bash
    sudo pacman -S reflector --needed
    ```

2.  **Find and save the fastest mirrors using `reflector`:**

    `reflector` automates the process of testing the download speed of various mirrors and generating a ranked list of the fastest ones. Use the following command, replacing `US` with your country code (e.g., `DE` for Germany, `FR` for France), or a comma-separated list of countries:

    ```bash
    sudo reflector --country US --age 12 --latest 20 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
    ```

    This command tells `reflector` to:

    *   Find mirrors located in the specified `--country`.
    *   Only consider mirrors that have been updated within the last `--age 12` hours.
    *   Select the `--latest 20` mirrors.
    *   `--sort rate` the mirrors based on their download rate (speed).
    *   Only use mirrors that support `--protocol https` (secure connections).
    *   `--save /etc/pacman.d/mirrorlist` the resulting mirror list to the specified file, which `pacman` uses.

    > **Tip**: A list of valid country codes can be found at [https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). For a broader selection of potentially fast mirrors globally, you can remove the `--country` option entirely, but this is not usually necessary

3.  **Update package databases:**

    After generating the new mirror list, you need to tell `pacman` to update its package databases so that it uses the new mirror list. This is done with the following command:

    ```bash
    sudo pacman -Syy
    ```

## Setting up Chaotic-AUR

Chaotic-AUR is a repository of pre-built packages from the Arch User Repository (AUR). Using Chaotic-AUR can save you significant time and resources, as you won't need to compile these packages yourself.

### Steps

1.  **Add the Chaotic-AUR key:**
    First, you need to add the key used to sign packages in the Chaotic-AUR repository:

    ```bash
    sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
    sudo pacman-key --lsign-key 3056513887B78AEB
    ```

2.  **Add the Chaotic-AUR repository to your `pacman.conf`:**
    Open the `/etc/pacman.conf` file with nano:

    ```bash
    sudo nano /etc/pacman.conf
    ```

    Add the following lines at the end of the file:

    ```plaintext
    [chaotic-aur]
    Include = /etc/pacman.d/chaotic-mirrorlist
    ```

3.  **Update your system:**
    Finally, update your system to recognize the new packages available from Chaotic-AUR:

    ```bash
    sudo pacman -Syyu
    ```

    > **Warning**: Chaotic-AUR is a third-party repository. Exercise caution when installing packages from it, and review the PKGBUILDs whenever possible to mitigate security risks.
