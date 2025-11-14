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
    
    
## Enabling TRIM for SSD

TRIM is a command that helps solid-state drives (SSDs) maintain their performance over time. It tells the SSD which data blocks are no longer in use and can be erased, which improves write speeds and extends the lifespan of the drive.

1.  **Enable TRIM:**

    Run the following commands to enable and verify TRIM:

    ```bash
    sudo systemctl enable fstrim.timer
    sudo systemctl start fstrim.timer
    systemctl status fstrim.timer
    ```

    *   `sudo systemctl enable fstrim.timer`: Enables the `fstrim.timer` service to run automatically at boot.
    *   `sudo systemctl start fstrim.timer`: Starts the `fstrim.timer` service immediately.
    *   `systemctl status fstrim.timer`: Checks the status of the `fstrim.timer` service to ensure it is active and running.

## Enabling Bluetooth

These steps enable Bluetooth support.

1.  **Enable Bluetooth:**

    Run the following commands to enable and verify Bluetooth:

    ```bash
    sudo systemctl enable bluetooth.service
    sudo systemctl start bluetooth.service
    systemctl status bluetooth.service
    ```

    *   `sudo systemctl enable bluetooth.service`: Enables the `bluetooth.service` to run automatically at boot.
    *   `sudo systemctl start bluetooth.service`: Starts the `bluetooth.service` immediately.
    *   `systemctl status bluetooth.service`: Checks the status of the `bluetooth.service` to ensure it is active and running.

## Enabling NetworkManager

NetworkManager is a network management tool that simplifies connecting to wired and wireless networks.

1.  **Enable NetworkManager:**

    ```bash
    sudo systemctl enable NetworkManager.service
    sudo systemctl start NetworkManager.service
    systemctl status NetworkManager.service
    ```

    *   `sudo systemctl enable NetworkManager.service`: Enables the `NetworkManager.service` to run automatically at boot.
    *   `sudo systemctl start NetworkManager.service`: Starts the `NetworkManager.service` immediately.
    *   `systemctl status NetworkManager.service`: Checks the status of the `NetworkManager.service` to ensure it is active and running.

## Setting up the Firewall

This section configures the Uncomplicated Firewall (UFW) to protect your system by allowing only necessary network connections.

1.  **Configure UFW:**

    Run the following commands to set up UFW with basic rules:

    ```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow ssh
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw enable
    ```

    *   `sudo ufw default deny incoming`: Denies all incoming connections by default.
    *   `sudo ufw default allow outgoing`: Allows all outgoing connections by default.
    *   `sudo ufw allow ssh`: Allows incoming SSH connections (port 22 by default).
    *   `sudo ufw allow http`: Allows incoming HTTP connections (port 80).
    *   `sudo ufw allow https`: Allows incoming HTTPS connections (port 443).
    *   `sudo ufw enable`: Enables the UFW firewall.

2.  **Optional: Allow specified ports and services for Steam (if used):**

    Run the following commands to allow incoming connections for Steam features (e.g., game streaming, voice chat) through your network:

    ```bash
    sudo ufw allow 53/tcp
    sudo ufw allow 8080/tcp
    sudo ufw allow 32000/tcp
    sudo ufw allow 8081/tcp
    sudo ufw allow 2020/tcp
    sudo ufw allow 1716/tcp
    sudo ufw allow 27060/tcp
    sudo ufw allow 33220/tcp
    sudo ufw allow 44101/tcp
    sudo ufw allow 37653/tcp
    sudo ufw allow 47317/tcp
    sudo ufw allow 5355/tcp
    sudo ufw allow 53/udp
    sudo ufw allow 27036/udp
    sudo ufw allow 50547/udp
    sudo ufw allow 59838/udp
    sudo ufw allow 5353/udp
    sudo ufw allow 5355/udp
    sudo ufw allow 1714:1764/udp
    ```

3.  **Show the status of UFW:**

    Run the following command to confirm the rules have been added:

    ```bash
    sudo ufw status verbose
    ```

    This command will display the status of UFW and the list of allowed and denied connections.

## Implementing Dotfiles

This section explains how to implement your dotfiles.

1.  **Clone the dotfiles repository:**

    Open a terminal and clone the repository directly into your Documents directory as a folder named `Dotfiles`:

    ```bash
    mkdir -p ~/Documents/Dotfiles/
    git clone https://github.com/linuxury/dotfiles.git ~/Documents/Dotfiles
    ```

2.  **Set Permissions:**

    After cloning, it's crucial to ensure that your user owns the `Dotfiles` directory to prevent permission errors. Run the following command:

    ```bash
    sudo chown -R $USER:$USER ~/Documents/Dotfiles
    ```

3.  **Setting Up Symlinks:**

    After cloning, create symlinks for the dotfiles. Here’s a quick summary of the commands you would need to run:

    ```bash
    # Create symlinks for each application
    ln -s ~/Documents/Dotfiles/dunst ~/.config/dunst
    ln -s ~/Documents/Dotfiles/fastfetch ~/.config/fastfetch
    ln -s ~/Documents/Dotfiles/fish ~/.config/fish
    ln -s ~/Documents/Dotfiles/ghostty ~/.config/ghostty
    ln -s ~/Documents/Dotfiles/helix ~/.config/helix
    ln -s ~/Documents/Dotfiles/Kvantum ~/.config/Kvantum
    ln -s ~/Documents/Dotfiles/MangoHud ~/.config/MangoHud
    ln -s ~/Documents/Dotfiles/starship ~/.config/starship
    ln -s ~/Documents/Dotfiles/topgrade ~/.config/topgrade.d
    ln -s ~/Documents/Dotfiles/zed ~/.config/zed
    ln -s ~/Documents/Dotfiles/nano/.nanorc ~/.nanorc
    sudo ln -s ~/Documents/Dotfiles/nano/.nanorc /root/.nanorc
    rsync -a --ignore-existing ~/Documents/Dotfiles/Pictures ~/
    ```

    **Important Notes:**

    *   Adjust the `ln -s` commands to match the specific files and directories in your dotfiles repository. The example above is a template.
    *   The `rsync` command is used to copy the `Pictures` directory from the dotfiles repository to your home directory, ignoring any existing files. This is specific to the example repository and may not be applicable to all dotfiles repositories.
    *   The `sudo ln -s ~/Documents/Dotfiles/nano/.nanorc /root/.nanorc` command requires root privileges and is used to create a symlink to the `.nanorc` file in the root user's home directory.

4.  **Apply changes:**

    Some changes may require you to restart your terminal or source the relevant configuration file (e.g., `.bashrc`, `.zshrc`).

Now your system should be configured with the dotfiles.

## Configuring SDDM

This section explains how to configure SDDM, the Simple Desktop Display Manager used by KDE Plasma.

1.  **Create the configuration directory:**

    Create the `/etc/sddm.conf.d` directory if it doesn't already exist:

    ```bash
    sudo mkdir -p /etc/sddm.conf.d
    ```

2.  **Create the configuration file and add settings:**

    Use the following commands to create the `kde_settings.conf` file and add the configuration settings:

    ```bash
    echo "[General]" | sudo tee /etc/sddm.conf.d/kde_settings.conf
    echo "Numlock=on" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf
    echo "HaltCommand=/usr/lib/systemd/systemd-shutdown poweroff" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf
    echo "RebootCommand=/usr/lib/systemd/systemd-shutdown reboot" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf
    echo "[Theme]" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf
    echo "Current=breeze" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf
    ```

    *   `echo "[General]" | sudo tee /etc/sddm.conf.d/kde_settings.conf`: Creates the `kde_settings.conf` file and adds the `[General]` section header.
    *   `echo "Numlock=on" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf`: Enables NumLock at startup.
    *   `echo "HaltCommand=/usr/lib/systemd/systemd-shutdown poweroff" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf`: Sets the command to power off the system.
    *   `echo "RebootCommand=/usr/lib/systemd/systemd-shutdown reboot" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf`: Sets the command to reboot the system.
    *   `echo "[Theme]" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf`: Adds the `[Theme]` section header.
    *   `echo "Current=breeze" | sudo tee -a /etc/sddm.conf.d/kde_settings.conf`: Sets the current theme to "breeze".

Now SDDM should be configured with the specified settings.

## Configuring Fish Shell

This section explains how to set Fish as your default shell.

1.  **Configure Fish as the default shell:**

    Run the following commands to verify Fish, add it to the list of valid shells, and set it as your default shell:

    ```bash
    which fish  # Verify Fish
    echo /usr/bin/fish | sudo tee -a /etc/shells
    chsh -s /usr/bin/fish
    ```

    *   `which fish`: Verifies that Fish is installed and shows its path.
    *   `echo /usr/bin/fish | sudo tee -a /etc/shells`: Adds Fish to the list of valid shells in `/etc/shells`. This is required for `chsh` to work correctly.
    *   `chsh -s /usr/bin/fish`: Changes your default shell to Fish. You will be prompted for your password.

2.  **Verify the change:**

    Log out and log back in, or start a new terminal session. Your new terminal should now be using Fish. You can verify this by running:

    ```bash
    echo $SHELL
    ```

    The output should be `/usr/bin/fish`.

## Enabling Global Menu (Optional)

The global menu places the application menu (File, Edit, View, etc.) at the top of the screen, rather than within the application window itself. Some users prefer this style of interface.

1.  **Configure Global Menu:**

    Run the following commands to install the necessary packages and restart the Plasma shell:

    ```bash
    sudo pacman -S appmenu-gtk-module libdbusmenu-glib --needed
    kquitapp5 plasmashell && kstart5 plasmashell
    ```

    *   `sudo pacman -S appmenu-gtk-module libdbusmenu-glib --needed`: Installs the required packages for the global menu functionality.
    *   `kquitapp5 plasmashell && kstart5 plasmashell`: Quits and then restarts the Plasma shell to apply the changes without needing a full logout/login.

After these steps, the global menu should be enabled. If you don't like it, you can uninstall the packages to revert to the default behavior.

## Configuring the Bootloader for a Fast and Silent Boot

`systemd-boot` is a simple UEFI boot manager. This section guides you through optimizing your `systemd-boot` configuration to achieve a faster and quieter boot experience, displaying messages only if errors or issues occur.

1.  **Identify your root filesystem's PARTUUID:**

    To correctly configure your boot entry, you need the `PARTUUID` of your root filesystem. You can find this using the `blkid` command. Replace `/dev/sdXn` with your actual root partition (e.g., `/dev/sda2`, `/dev/nvme0n1p2`).

    ```bash
    blkid -s PARTUUID -o value /dev/sdXn
    ```

    Alternatively, you can list all block devices and their PARTUUIDs:

    ```bash
    ls -l /dev/disk/by-partuuid/
    ```

    **Make a note of the `PARTUUID` for your root partition**, as you will need it in the next step.

2.  **Configure `loader.conf` for silent boot:**

    This file controls the global behavior of `systemd-boot`, including the timeout for the boot menu. We will set the timeout to `0` for an immediate boot.

    Open the `loader.conf` file using `nano`:

    ```bash
    sudo nano /boot/loader/loader.conf
    ```

    Ensure the file contains the following lines. **Specifically, change the `timeout` value to `0` and add `console-mode max` and `editor no` if they are not present.** Adjust `default` to match your preferred boot entry filename (e.g., `arch-zen.conf` for the `linux-zen` kernel).

    ```plaintext
    default  arch-zen.conf
    timeout  0
    console-mode max
    editor   no
    ```

    *   `default arch-zen.conf`: Sets the default boot entry.
    *   `timeout 0`: Sets the boot menu timeout to 0 seconds, meaning it will boot directly into the default entry without waiting for user input.
    *   `console-mode max`: Uses the highest available console resolution.
    *   `editor no`: Disables the boot entry editor in the boot menu.

    Save the changes by pressing `Ctrl+O`, then `Enter`, and exit `nano` by pressing `Ctrl+X`.

3.  **Create or edit your boot entry file for silent kernel output:**

    Boot entries for `systemd-boot` are typically located in `/boot/loader/entries/`. The exact filename depends on your kernel, for example, `arch.conf` for the standard kernel or `arch-zen.conf` for the `linux-zen` kernel.

    If you don't have a boot entry yet, you can create one. If one exists, open it. Assuming you are using the `linux-zen` kernel:

    ```bash
    sudo nano /boot/loader/entries/arch-zen.conf
    ```

    Ensure your boot entry file looks similar to the example below. **The key change is to add the `quiet splash loglevel=3 rd.systemd.show_status=false rd.udev.log_priority=3` parameters to the `options` line.**

    **Remember to replace `YOUR_ROOT_PARTUUID` with the actual `PARTUUID` you noted in step 1.**

    ```plaintext
    title   Arch Linux (linux-zen)
    linux   /vmlinuz-linux-zen
    initrd  /initramfs-linux-zen.img
    options root=PARTUUID=YOUR_ROOT_PARTUUID zswap.enabled=0 rootflags=subvol=@ rw rootfstype=btrfs quiet splash loglevel=3 rd.systemd.show_status=false rd.udev.log_priority=3
    ```

    *   `title Arch Linux (linux-zen)`: The name displayed in the `systemd-boot` menu.
    *   `linux /vmlinuz-linux-zen`: Path to the Linux kernel image.
    *   `initrd /initramfs-linux-zen.img`: Path to the initial ramdisk image.
    *   `options ...`: Kernel parameters.
        *   `root=PARTUUID=YOUR_ROOT_PARTUUID`: Tells the kernel which partition is the root filesystem.
        *   `zswap.enabled=0`: Disables zswap.
        *   `rootflags=subvol=@`: Specifies the Btrfs subvolume.
        *   `rw`: Mounts the root filesystem as read-write.
        *   `rootfstype=btrfs`: Specifies the filesystem type.
        *   `quiet`: Suppresses most kernel messages during boot.
        *   `splash`: (Optional) Can be used with a graphical boot splash.
        *   `loglevel=3`: Sets the kernel log level to show only error messages.
        *   `rd.systemd.show_status=false`: Disables showing status messages from `systemd` during early boot.
        *   `rd.udev.log_priority=3`: Sets `udev` log priority to only show errors.

    Save the changes by pressing `Ctrl+O`, then `Enter`, and exit `nano` by pressing `Ctrl+X`.

After completing these steps, your `systemd-boot` should be configured for a fast and silent boot, showing output only in case of errors. You will need to **reboot your system** for these changes to take effect.

## Arch Update Counter Configuration

This section guides you through configuring the Arch Update Notifier applet for Plasma, allowing you to customize its behavior, update commands, and visual appearance.

1.  **General Settings:**
    *   **Change the option for update to**: `5 minute(s)`
    *   **Select the option for**: `Retry if error`

2.  **Search & Count Settings:**
    *   **Count ARCH command**: `checkupdates | wc -l`
    *   **Count AUR command**: `paru -Qua | wc -l`
    *   **List ARCH command**: `checkupdates`
    *   **List AUR command**: `paru -Qua`

3.  **Update Package Settings:**
    *   **Update command**: `topgrade`
    *   **Update one command**: `paru -Sy`
    *   **Terminal cmd for update action**: `ghostty -e`
    *   **Terminal cmd for update with do not close**: `ghostty --noclose -e`

4.  **Popup Color Settings:**
    *   **Name**: White `#D8DEE9`
    *   **Source**: Teal `#88C0D0`
    *   **From Version**: Light Gray `#E5E9F0`
    *   **Separator**: Green `#A3BE8C`
    *   **To Version**: Bright white `#ECEFF4`

## Dunst Notification Configuration

This section explains how to configure Dunst, a lightweight and highly customizable notification daemon, to work effectively with KDE Plasma.

1.  **Disable Plasma's Notification Service:**

    To ensure Dunst handles notifications instead of Plasma's default service, disable Plasma's notification service.

    *   Open `System Settings > Startup and Shutdown > Background Services` (or search for "Notifications" in the settings).
    *   Under System Services, find "Notifications" and set it to `Disabled`. This prevents Plasma from claiming the D-Bus notification service.
    *   Alternatively, for a more permanent (but hackier) approach, rename Plasma's D-Bus service file to prevent it from loading:
        ```bash
        sudo mv /usr/share/dbus-1/services/org.kde.plasma.Notifications.service /usr/share/dbus-1/services/org.kde.plasma.Notifications.service.disabled
        ```
        > To revert, rename it back. Path may vary slightly by distro; check `/usr/share/dbus-1/services/`

2.  **Autostart Dunst:**

    Ensure Dunst starts automatically when you log in.

    *   In `System Settings > Startup and Shutdown > Autostart`, click `Add > Add Application` and select `dunst` (just type `dunst`, it will work).
    *   Alternatively, add it to your `~/.xinitrc` or `~/.xprofile` if you use a custom session, or use a systemd user service for better reliability:
        ```bash
        mkdir -p ~/.config/systemd/user
        echo '[Unit]
        Description=Dunst
        [Service]
        ExecStart=/usr/bin/dunst
        Restart=always
        [Install]
        WantedBy=default.target' > ~/.config/systemd/user/dunst.service
        systemctl --user enable --now dunst.service
        ```

3.  **Restart Plasma:**

    Log out and back in, or run the following command in a terminal to reload the shell without logging out. This ensures the D-Bus service switches to Dunst.

    ```bash
    kquitapp5 plasmashell && kstart5 plasmashell
    ```

    > You could also reload Dunst specifically with:
    ```bash
    killall dunst && dunst &
    ```

4.  **Test Dunst Notifications:**

    Send some test notifications to confirm Dunst is working correctly:

    ```bash
    notify-send --urgency=low --icon=firefox "Firefox Test" "Testing Firefox icon"
    notify-send --urgency=normal --icon=dialog-information "Info Test" "Testing dialog-information icon"
    notify-send --urgency=normal --icon=update "Update Test" "Testing update icon"
    notify-send --urgency=normal --icon=mail "Mail Test" "Testing mail icon"
    notify-send --urgency=critical --icon=dialog-error "Error Test" "Testing dialog-error icon"
    notify-send --urgency=normal --icon=terminal "Terminal Test" "Testing terminal icon"
    notify-send --urgency=normal --icon=document "Document Test" "Testing document icon"
    ```

## uBlock Origin Extension

This section provides custom filters for uBlock Origin, specifically designed to hide YouTube Shorts.

1.  **Add Custom Filters in Firefox:**

    *   Go into your `Firefox` browser and navigate to the `uBlock Origin` extension settings.
    *   Enter into `Settings`, then select the `My filters` tab.
    *   Copy the code below and hit `Apply Changes` to apply this filter. This will remove all `Shorts` from `Youtube`.

    ```plaintext
    ! Title: Hide YouTube Shorts
    ! Description: Hide all traces of YouTube shorts videos on YouTube
    ! Version: 1.8.0
    ! Last modified: 2023-01-08 20:02
    ! Expires: 2 weeks (update frequency)
    ! Homepage: https://github.com/gijsdev/ublock-hide-yt-shorts
    ! License: https://github.com/gijsdev/ublock-hide-yt-shorts/blob/master/LICENSE.md

    ! Hide all videos containing the phrase "#shorts"
    youtube.com##ytd-grid-video-renderer:has(#video-title:has-text(#shorts))
    youtube.com##ytd-grid-video-renderer:has(#video-title:has-text(#Shorts))
    youtube.com##ytd-grid-video-renderer:has(#video-title:has-text(#short))
    youtube.com##ytd-grid-video-renderer:has(#video-title:has-text(#Short))

    ! Hide all videos with the shorts indicator on the thumbnail
    youtube.com##ytd-grid-video-renderer:has([overlay-style="SHORTS"])
    youtube.com##ytd-rich-item-renderer:has([overlay-style="SHORTS"])
    youtube.com##ytd-video-renderer:has([overlay-style="SHORTS"])
    youtube.com##ytd-item-section-renderer.ytd-section-list-renderer[page-subtype="subscriptions"]:has(ytd-video-renderer:has([overlay-style="SHORTS"]))

    ! Hide shorts button in sidebar
    youtube.com##ytd-guide-entry-renderer:has-text(Shorts)
    youtube.com##ytd-mini-guide-entry-renderer:has-text(Shorts)

    ! Hide shorts section on homepage
    youtube.com##ytd-rich-section-renderer:has(#rich-shelf-header:has-text(Shorts))
    youtube.com##ytd-reel-shelf-renderer:has(.ytd-reel-shelf-renderer:has-text(Shorts))

    ! Hide shorts tab on channel pages
    ! Old style
    youtube.com##tp-yt-paper-tab:has(.tp-yt-paper-tab:has-text(Shorts))
    ! New style (2023-10)
    youtube.com##yt-tab-shape:has-text(/^Shorts$/)

    ! Hide shorts in video descriptions
    youtube.com##ytd-reel-shelf-renderer.ytd-structured-description-content-renderer:has-text("Shorts remixing this video")

    ! Remove empty spaces in grid
    youtube.com##ytd-rich-grid-row,#contents.ytd-rich-grid-row:style(display: contents !important)


    !!! MOBILE !!!

    ! Hide all videos in home feed containing the phrase "#shorts"
    m.youtube.com##ytm-rich-item-renderer:has(#video-title:has-text(#shorts))
    m.youtube.com##ytm-rich-item-renderer:has(#video-title:has-text(#Shorts))
    m.youtube.com##ytm-rich-item-renderer:has(#video-title:has-text(#short))
    m.youtube.com##ytm-rich-item-renderer:has(#video-title:has-text(#Short))

    ! Hide all videos in subscription feed containing the phrase "#shorts"
    m.youtube.com##ytm-item-section-renderer:has(#video-title:has-text(#shorts))
    m.youtube.com##ytm-item-section-renderer:has(#video-title:has-text(#Shorts))
    m.youtube.com##ytm-item-section-renderer:has(#video-title:has-text(#short))
    m.youtube.com##ytm-item-section-renderer:has(#video-title:has-text(#Short))

    ! Hide shorts button in the bottom navigation bar
    m.youtube.com##ytm-pivot-bar-item-renderer:has(.pivot-shorts)

    ! Hide all videos with the shorts indicator on the thumbnail
    m.youtube.com##ytm-video-with-context-renderer:has([data-style="SHORTS"])

    ! Hide shorts sections
    m.youtube.com##ytm-rich-section-renderer:has(ytm-reel-shelf-renderer:has(.reel-shelf-title-wrapper:has-text(Shorts)))
    m.youtube.com##ytm-reel-shelf-renderer.item:has(.reel-shelf-title-wrapper:has-text(Shorts))

    ! Hide shorts tab on channel pages
    m.youtube.com##.single-column-browse-results-tabs>a:has-text(Shorts)
    ```

## Optimize Steam for Gaming

This section guides you through configuring Steam to achieve optimal gaming performance, leveraging `proton-ge-custom` and custom launch options. `proton-ge-custom` (which was installed in the [Gaming sub-section](#gaming)) is crucial for enabling Windows games on Steam and Lutris. Custom launch options further allow you to maximize performance and enable advanced features.

#### Setting `proton-ge-custom` as the default compatibility tool

*   Open Steam.

`proton-ge-custom` (which was installed in the [Gaming sub-section](#gaming)) is crucial for enabling Windows games on Steam and Lutris. Custom launch options further allow you to maximize performance and enable advanced features.

1.  **Set `proton-ge-custom` as the default compatibility tool:**
    *   Open Steam.
    *   Navigate to `Settings > Compatibility (Steam Play)`.
    *   Enable "Steam Play for all other titles" and select your installed `Proton-GE` version from the dropdown menu.
    *   If you are also using Lutris, open Lutris and set `proton-ge-custom` as the runner for your games within their individual game settings.

2.  **Configure Steam launch options for individual games:**
    For each game you want to optimize:
    *   Right-click the game in your Steam library.
    *   Select `Properties`.
    *   In the `General` tab, locate the `Launch Options` field.
    *   Add the following recommended parameters:

    ```plaintext
    PROTON_ENABLE_WAYLAND=1 PROTON_ENABLE_HDR=1 PROTON_FSR4_UPGRADE=1 gamemoderun %command%
    ```

    *   **Explanation of parameters**:
        *   `PROTON_ENABLE_WAYLAND=1`: Enables Wayland integration, which can improve scaling and High Dynamic Range (HDR) support.
        *   `PROTON_ENABLE_HDR=1`: Activates HDR output. Your monitor must support HDR for this to have an effect.
        *   `SteamDeck=1`: Applies optimizations intended for Steam Deck, which can benefit desktop Linux users.
        *   `PROTON_FSR4_UPGRADE=1`: Enables experimental FidelityFX Super Resolution 4.0 upscaling.
        *   `PROTON_DLSS_UPGRADE=1`: Enables experimental Deep Learning Super Sampling (DLSS) for NVIDIA GPUs.
        *   `gamemoderun`: Utilizes `gamemode` to dynamically optimize CPU and GPU performance when the game is running.
        *   `mangohud`: Launches MangoHud, an in-game overlay to monitor FPS, GPU usage, temperatures, and more.

    > **Note**: For more detailed information on Wayland-specific flags and advanced options, please refer to: [Etaash-mathamsetty guide](https://github.com/Etaash-mathamsetty/Proton/blob/em-10/docs/EM-ADDITIONS.md)

3.  **Alternative launch options for beginners:**
    If you encounter instability or issues with the experimental flags, you can use a simpler set of launch options:

    ```plaintext
    PROTON_ENABLE_WAYLAND=1 gamemoderun mangohud %command%
    ```

    > **Warning**: Experimental flags (like `PROTON_FSR4_UPGRADE` and `PROTON_DLSS_UPGRADE`) may cause instability or unexpected behavior in some games. If you experience problems, try running the game without these flags.

4.  **Verify Wayland session (optional):**

To confirm your current session is running Wayland, open a terminal and execute:

    ```bash
    echo $XDG_SESSION_TYPE  # This command should output "wayland"
    ```

5.  **Verify `proton-ge-custom` installation:**
    
    To confirm `proton-ge-custom` is properly recognized by Steam, you can check its installation directory:

    ```bash
    ls ~/.local/share/Steam/compatibilitytools.d  # This should list your Proton-GE-Custom version
    ```

After these configurations, your Steam games should be optimized for a better gaming experience.

