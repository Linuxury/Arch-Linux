# Configuring Arch Linux with KDE Plasma: Essential Initial Setup

This guide walks you through the essential initial configuration of your Arch Linux system after installing KDE Plasma using `archinstall`. It focuses on optimizing your package manager, shell, download sources, and essential tools. This guide is tailored for users with little to no prior Linux experience, so all steps are explained in detail.

> **Note**: These instructions assume you're comfortable using a terminal. If a command fails, the Arch Wiki ([https://wiki.archlinux.org/](https://wiki.archlinux.org/)) is an invaluable resource.

## Optimizing Pacman for Speed and Reliability

Pacman is Arch Linux's powerful package manager, used to install, update, and remove software. Configuring it correctly from the start ensures a smooth and efficient system. We'll adjust settings to enable faster downloads and easier-to-read output.

1.  **Edit the `pacman.conf` file:**
    The main configuration file for `pacman` is located at `/etc/pacman.conf`. You need to open it with a text editor as the root user. `nano` is a simple, terminal-based editor that's ideal for this purpose.

    Open the file using `nano`:

    ```bash
    sudo nano /etc/pacman.conf
    ```

2.  **Enable useful options in the `[options]` section:**
    In `nano`, scroll down until you find the `[options]` section. This section controls Pacman's global behavior. Inside this section, uncomment (remove the `#` symbol) the following options *if they exist*, or add them to the section if they are missing:

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
    *   `ParallelDownloads = 15`: This allows `pacman` to download multiple packages simultaneously, which can dramatically speed up the download process. A good starting point is 15, but you can increase or decrease this number depending on the speed of your internet connection. Alternatively, you can match this to the number of cores your CPU has by using the command `$(nproc)`
    *   `ILoveCandy`: This adds a fun progress bar during package extraction, because who doesn't love a little eye candy?

    After making these changes, save the file by pressing `Ctrl+O`, then press `Enter` to confirm the filename. Exit `nano` by pressing `Ctrl+X`.

## Enhancing Your Command Line with Bash Autocompletion

Bash is the command-line interpreter, or *shell*, that you use to interact with your Arch Linux system. Installing and configuring `bash-completion` will greatly improve your command-line experience by providing automatic suggestions as you type commands.

1.  **Install `bash-completion`:**

    Install the `bash-completion` package with pacman:

    ```bash
    sudo pacman -S bash-completion --needed
    ```

2.  **Enable bash completion by modifying `~/.bashrc`:**
    The `~/.bashrc` file contains a set of commands that are executed every time you launch a new Bash terminal session. By adding the appropriate lines to this file, you can enable bash completion.

    **Method 1: Using `nano` (recommended for beginners)**

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

    **Method 2: Using `echo` (advanced users)**

    Alternatively, you can add the lines directly to the end of the file using `echo`:

    ```bash
    echo "# Enable bash completion" | sudo tee -a ~/.bashrc
    echo "[ -f /usr/share/bash-completion/bash_completion ] && . /usr/share/bash-completion/bash_completion" | sudo tee -a ~/.bashrc
    echo "complete -cf sudo" | sudo tee -a ~/.bashrc
    ```

    > **Warning**: This method appends the lines to the end of the file. It's recommended to use the `nano` to check the end of the file.

3.  **Refresh your current terminal session:**

    The changes you made to `~/.bashrc` will only take effect in new terminal sessions. To apply the changes to your current session, run the following command:

    ```bash
    source ~/.bashrc
    ```

## Installing an AUR Helper: Paru

The Arch User Repository (AUR) is a community-driven repository containing package descriptions (PKGBUILDs) for software not directly available in the official Arch Linux repositories. An AUR helper simplifies the process of downloading, building, and installing these packages. While `yay` is another popular option, this guide will focus on `paru` due to its modern features and active development.

Since building from source can be time-consuming, we'll install the pre-built binary version of `paru` (paru-bin)

1.  **Install paru-bin:**
    Run these commands to install `paru-bin`:

    ```bash
    sudo pacman -S git base-devel --needed
    git clone https://aur.archlinux.org/paru-bin.git
    cd paru-bin
    makepkg -si --needed
    cd ..
    rm -rf paru-bin
    paru --version
    ```

    You will be prompted to confirm the installation.

### Yay vs Paru
Both `yay` and `paru` are popular AUR helpers, but here's a quick comparison:

*   `paru` is written in Rust, offering potential performance and security benefits.
*   `paru` is actively maintained and incorporates modern features.
*   `yay` is also a solid choice and is widely used, but development is less active.

For most users, `paru` is an excellent choice due to its speed, features, and active development.

## Setting up Chaotic-AUR

Chaotic-AUR is a repository of pre-built packages from the Arch User Repository (AUR). Using Chaotic-AUR can save you significant time and resources, as you won't need to compile these packages yourself.

1.  **Add the Chaotic-AUR key and repository:**
    Run the following commands to automatically add the key and repository:

    ```bash
    sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
    sudo pacman-key --lsign-key 3056513887B78AEB
    sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst'
    sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
    echo "[chaotic-aur]" | sudo tee -a /etc/pacman.conf
    echo "Include = /etc/pacman.d/chaotic-mirrorlist" | sudo tee -a /etc/pacman.conf
    sudo pacman -Syyu
    ```

    > **Warning**: Chaotic-AUR is a third-party repository. Exercise caution when installing packages from it, and review the PKGBUILDs whenever possible to mitigate security risks.

## Setting up GPU Drivers

Properly installed graphics drivers are essential for utilizing your GPU for gaming and other graphically intensive tasks. Without drivers, your system will rely on the CPU for graphics processing, resulting in poor performance. The steps to install the correct drivers depend on your GPU vendor (NVIDIA, AMD, or Intel).

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

## Package Installation

This section provides a convenient command to install all the recommended packages for a fully functional and optimized KDE Plasma experience on Arch Linux. This includes essential system utilities, desktop environment enhancements, multimedia codecs and applications, productivity tools, development tools, gaming-related software, and more. Simply copy and paste the following command into your terminal and press Enter to begin the installation process:

```bash
paru -S bluez bluez-utils networkmanager rsync reflector timeshift topgrade pamac pacman-contrib xfsprogs kwalletmanager kdeconnect kdotool plasma6-themes-layan-git tela-icon-theme breezex-cursor-theme plasma6-applets-arch-update-notifier kvantum fish ghostty legacy-launcher faugus-launcher starship loupe dunst alsa-plugins gst-libav gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly giflib glfw gst-plugins-base-libs libjpeg-turbo libva libxslt mpg123 openal opencl-icd-loader phonon-qt6-gstreamer-git amberol showtime onlyoffice-bin zed thunderbird thunderbird-ublock-origin firefox firefox-ublock-origin fluent-reader-bin discord kdepim-addons merkuro helix jre21-openjdk jre22-openjdk jre23-openjdk papers gamemode gamescope mangohud openrgb power-profiles-daemon proton-ge-custom protontricks wine winetricks steam heroic-games-launcher-bin sgdboop-bin lsfg-vk-git vulkan-tools lib32-libva lib32-vulkan-intel lib32-vulkan-radeon mcpelauncher-ui-git mcpelauncher-linux-git nerd-fonts ttf-liberation lib32-alsa-plugins lib32-giflib lib32-gst-plugins-base-libs lib32-gtk3 lib32-libjpeg-turbo lib32-mpg123 lib32-ocl-icd lib32-opencl-icd-loader lib32-openal gnome-disk-utility ufw flatpak mission-center obs-studio --needed
```

## Package Categories and Descriptions

### Core System & Utilities

```bash
paru -S bluez bluez-utils networkmanager rsync reflector timeshift topgrade pamac pacman-contrib xfsprogs --needed
```

*   `bluez`: Bluetooth stack for Linux.
*   `bluez-utils`: Utilities for managing Bluetooth devices.
*   `networkmanager`: Network management daemon.
*   `rsync`: Fast, versatile file copying tool.
*   `reflector`:  Tool to find the fastest Arch Linux mirrors.
*   `timeshift`: System restore utility.
*   `topgrade`: Upgrade your system using many package managers.
*   `pamac`: GUI package manager for Pamac (if you're using it).
*   `pacman-contrib`: Additional tools and scripts for Pacman.
*   `xfsprogs`: Utilities for managing XFS filesystems.

### Desktop Environment & Appearance

```bash
paru -S kwalletmanager kdeconnect kdotool plasma6-themes-layan-git tela-icon-theme breezex-cursor-theme plasma6-applets-arch-update-notifier kvantum fish ghostty legacy-launcher faugus-launcher starship loupe dunst --needed
```

*   `kwalletmanager`: KDE Wallet Manager for storing passwords.
*   `kdeconnect`: Connect your phone to your KDE desktop.
*   `kdotool`:  Keyboard/mouse automation tool.
*   `plasma6-themes-layan-git`: A Plasma 6 theme.
*   `tela-icon-theme`: An icon theme.
*   `breezex-cursor-theme`: A cursor theme.
*   `plasma6-applets-arch-update-notifier`: Applet to check for updates.
*   `kvantum`: SVG-based theme engine for Qt.
*   `fish`: A smart and user-friendly command line shell.
*   `ghostty`: A terminal emulator.
*   `legacy-launcher`: A launcher.
*   `faugus-launcher`: Yet Another AUR Helper.
*   `starship`: A minimal, blazing-fast, and infinitely customizable prompt for any shell.
*   `loupe`: Image viewer.
*   `dunst`: A lightweight notification daemon.

### Multimedia

```bash
paru -S alsa-plugins gst-libav gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly giflib glfw gst-plugins-base-libs libjpeg-turbo libva libxslt mpg123 openal opencl-icd-loader phonon-qt6-gstreamer-git amberol showtime --needed
```

*   `alsa-plugins`: ALSA (Advanced Linux Sound Architecture) plugins.
*   `gst-libav`: GStreamer plugin for libavcodec (ffmpeg).
*   `gst-plugins-base`: GStreamer base plugins.
*   `gst-plugins-good`: GStreamer good quality plugins.
*   `gst-plugins-bad`: GStreamer less good quality plugins.
*   `gst-plugins-ugly`: GStreamer ugly quality plugins.
*   `giflib`: Library for working with GIF images.
*   `glfw`: Library for OpenGL, Vulkan and windowing.
*   `gst-plugins-base-libs`: GStreamer base plugins libraries.
*   `libjpeg-turbo`: JPEG image codec that utilizes SIMD instructions.
*   `libva`: Video Acceleration (VA) API.
*   `libxslt`: Library for transforming XML documents.
*   `mpg123`: Real-time MPEG audio player.
*   `openal`: Open Audio Library (cross-platform 3D audio API).
*   `opencl-icd-loader`: OpenCL Installable Client Driver Loader.
*   `phonon-qt6-gstreamer-git`: Phonon Qt6 GStreamer backend.
*   `amberol`: A simple music player.
*   `showtime`: A simple video player.

### Productivity & Communication

```bash
paru -S onlyoffice-bin zed thunderbird thunderbird-ublock-origin firefox firefox-ublock-origin fluent-reader-bin discord kdepim-addons merkuro --needed
```

*   `onlyoffice-bin`: Office suite.
*   `zed`: A code editor.
*   `thunderbird`: Email client.
*   `thunderbird-ublock-origin`: uBlock Origin extension for Thunderbird.
*   `firefox`: Web browser.
*   `firefox-ublock-origin`: uBlock Origin extension for Firefox.
*   `fluent-reader-bin`: Modern desktop RSS reader.
*   `discord`: Chat and voice communication platform.
*   `kdepim-addons`: Addons for KDE PIM (Personal Information Management) suite.
*   `merkuro`: A simple feed reader.

### Development

```bash
paru -S helix jre21-openjdk jre22-openjdk jre23-openjdk papers --needed
```

*   `helix`: A post-modern modal text editor.
*   `jre21-openjdk`: Java Runtime Environment (OpenJDK 21).
*   `jre22-openjdk`: Java Runtime Environment (OpenJDK 22).
*   `jre23-openjdk`: Java Runtime Environment (OpenJDK 23).
*   `papers`: Read, organize, and annotate academic papers.

### Gaming

```bash
paru -S gamemode gamescope mangohud openrgb power-profiles-daemon proton-ge-custom protontricks wine winetricks steam heroic-games-launcher-bin sgdboop-bin lsfg-vk-git --needed
```

*   `gamemode`: Optimizes system performance for gaming.
*   `gamescope`: A micro-compositor for games.
*   `mangohud`:  Overlay for monitoring system performance in games.
*   `openrgb`: Software to control RGB lighting on computer components.
*   `power-profiles-daemon`: System service for managing power profiles.
*   `proton-ge-custom`: Custom build of Proton for running Windows games.
*   `protontricks`: A tool to run wine tricks for Proton enabled games.
*   `wine`: Compatibility layer for running Windows applications.
*   `winetricks`: Script to install various components needed for Wine.
*   `steam`: Digital game distribution platform.
*   `heroic-games-launcher-bin`: A launcher for Epic Games, GOG and Amazon.
*   `sgdboop-bin`: SteamGridDB boop for changing Steam cover arts.
*   `lsfg-vk-git`: Linux Super Fast Game - Vulkan.

### Low-Level Graphics

```bash
paru -S vulkan-tools lib32-libva lib32-vulkan-intel lib32-vulkan-radeon --needed
```

*   `vulkan-tools`: Vulkan development and debugging tools.
*   `lib32-libva`: 32-bit Video Acceleration (VA) API.
*   `lib32-vulkan-intel`: 32-bit Vulkan driver for Intel GPUs.
*   `lib32-vulkan-radeon`: 32-bit Vulkan driver for AMD GPUs.

### Minecraft

```bash
paru -S mcpelauncher-ui-git mcpelauncher-linux-git --needed
```

*   `mcpelauncher-ui-git`: UI for Minecraft: Bedrock Edition launcher.
*   `mcpelauncher-linux-git`: Minecraft: Bedrock Edition launcher.

### Fonts

```bash
paru -S nerd-fonts ttf-liberation --needed
```

*   `nerd-fonts`: Collection of fonts with extra glyphs (icons).
*   `ttf-liberation`: Set of TrueType fonts.

### 32-bit Libraries

```bash
paru -S lib32-alsa-plugins lib32-giflib lib32-gst-plugins-base-libs lib32-gtk3 lib32-libjpeg-turbo lib32-mpg123 lib32-ocl-icd lib32-opencl-icd-loader lib32-openal --needed
```

*   `lib32-alsa-plugins`: 32-bit ALSA plugins.
*   `lib32-giflib`: 32-bit GIF library.
*   `lib32-gst-plugins-base-libs`: 32-bit GStreamer base plugins libraries.
*   `lib32-gtk3`: 32-bit GTK+ 3 toolkit.
*   `lib32-libjpeg-turbo`: 32-bit JPEG image codec.
*   `lib32-mpg123`: 32-bit MPEG audio player.
*   `lib32-ocl-icd`: 32-bit OpenCL ICD.
*   `lib32-opencl-icd-loader`: 32-bit OpenCL ICD Loader.
*   `lib32-openal`: 32-bit OpenAL.

### System Administration

```bash
paru -S gnome-disk-utility ufw flatpak mission-center power-profiles-daemon obs-studio --needed
```

*   `gnome-disk-utility`: Utility for managing disks and partitions.
*   `ufw`: Uncomplicated Firewall.
*   `flatpak`: Universal application packaging and distribution framework.
*   `mission-center`: A system monitoring app.
*   `power-profiles-daemon`: System service for managing power profiles.
*   `obs-studio`: Software for video recording and live streaming.

## Installing and Testing Flatpak

Flatpak is a universal application packaging and distribution framework. It allows you to install applications that are not available in the Arch Linux repositories or the AUR.

1.  **Install and Configure Flatpak:**

    Run the following commands to install Flatpak and add the Flathub repository:

    ```bash
    sudo pacman -S flatpak --needed
    flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
    flatpak remote-list
    ```

    *   `sudo pacman -S flatpak --needed`: Installs the Flatpak package manager.
    *   `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`: Adds the Flathub repository, a popular source for Flatpak applications. The `--if-not-exists` flag prevents adding the repository multiple times.
    *   `flatpak remote-list`: Verifies that Flatpak is properly installed and configured by listing the configured remotes, including "flathub".
