# Archinstall: A Beginner-Friendly Installation Guide for Arch Linux

This guide will walk you through the entire process of installing Arch Linux using the `archinstall` script. It's designed for users who have little to no prior knowledge of Linux, ensuring a smooth and understandable installation experience.

`archinstall` is a helper library designed to automate the installation of Arch Linux. It comes bundled with various pre-configured installers, most notably a "guided" installer that simplifies the installation process.

## Important Considerations Before You Begin

*   **Internet Connection:** Ensure you have a stable internet connection (wired is recommended for simplicity during installation).
*   **Live Medium:** You will need a bootable Arch Linux live medium (USB drive or DVD). Instructions on how to create one are outside the scope of this document, but you can find comprehensive information on the [ArchWiki Installation Guide](https://wiki.archlinux.org/title/Installation_guide#Pre-installation).
*   **Backup Data:** Always back up any important data on the drive where you plan to install Arch Linux. The installation process involves partitioning and formatting, which will erase existing data.
*   **UEFI vs. BIOS:** Most modern systems use UEFI. `archinstall` handles both, but it's good to be aware of your system's boot mode as it affects bootloader installation. You can usually check this in your system's BIOS/UEFI settings.
*   **Disk Selection:** Be extremely careful when selecting your disk for installation. Choosing the wrong disk can lead to data loss.

## Step 1: Boot the Arch Linux Live Environment
1.  Insert your Arch Linux live medium into your computer.
2.  Boot your computer from the live medium. This usually involves pressing a specific key during startup (e.g., `F2`, `F10`, `F12`, `Del`) to enter the boot menu or BIOS/UEFI settings.
3.  Once booted, you will be presented with an Arch Linux command-line environment. You should see a prompt like `#`.

## Step 2: Identify Your Drives (Crucial Pre-installation Check)
Before running `archinstall`, it is **highly recommended** to identify your storage drives to avoid accidentally installing Arch Linux on the wrong disk and losing valuable data.

1.  Use the `lsblk` command to list all block devices (storage drives and their partitions):
    ```/dev/null/lsblk_command.sh#L1-1
    lsblk
    ```
    This command will show you a tree-like structure of your disks (e.g., `/dev/sda`, `/dev/nvme0n1`) and their sizes. Pay close attention to the `NAME` and `SIZE` columns to identify the target drive for your Arch Linux installation.

    For example, you might see something like:
    ```/dev/null/lsblk_output.sh#L1-6
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    sda           8:0    0 931.5G  0 disk
    ├─sda1        8:1    0   512M  0 part /boot/efi
    ├─sda2        8:2    0 232.4G  0 part /
    └─sda3        8:3    0 698.6G  0 part /home
    nvme0n1     259:0    0   1.8T  0 disk
    ```
    In this example, `sda` is a 931.5GB drive, and `nvme0n1` is a 1.8TB drive.

2.  Make a note of the exact drive identifier (e.g., `/dev/sda` or `/dev/nvme0n1`) that you intend to use for installation.

## Step 3: Verify Internet Connection (Optional but Recommended)
While `archinstall` can often configure networking, it's a good idea to ensure you have an active internet connection before starting, especially if you plan to install additional packages.

*   **Wired Connection:** If you're using a wired connection, it should typically work out of the box. You can test it by pinging a website:
    ```/dev/null/ping_test.sh#L1-1
    ping -c 3 google.com
    ```
    If you see responses, your connection is working. Press `Ctrl+C` to stop the ping.
*   **Wireless Connection:** For wireless connections, you might need to use `iwctl`. A simplified approach is:
    ```/dev/null/iwctl_commands.sh#L1-6
    iwctl
    device list # Lists available wireless devices. Find your device name, e.g., wlan0.
    station <device> scan # Replace <device> with your wireless device name, e.g., `station wlan0 scan`.
    station <device> get-networks # Replace <device> with your wireless device name, e.g., `station wlan0 get-networks`. This lists available Wi-Fi networks.
    station <device> connect <SSID> # Replace <device> with your wireless device name and <SSID> with your Wi-Fi network's name, e.g., `station wlan0 connect MyWiFiNetwork`. You will be prompted for the password.
    exit # To exit iwctl
    ```
    Then, test with `ping -c 3 google.com`.

## Step 4: Update `archinstall` (Recommended)
It's always a good idea to ensure you're using the latest version of `archinstall`, as the live ISO might have an older version.

```/dev/null/update_archinstall.sh#L1-1
# pacman -Sy archinstall
```
A partial upgrade on the live ISO is usually fine, but a complete system update is preferable if memory allows.

## Step 5: Run `archinstall`
Now, you're ready to start the guided installer.

```/dev/null/run_archinstall.sh#L1-1
# archinstall
```

The `archinstall` script will launch and guide you through a series of questions. Here's a breakdown of the typical steps and what to do, in the order you will encounter them:

### 5.1. Language and Keyboard Layout
*   **Language:** Choose your preferred language for the installation process. Examples: `English`, `Español`, `Français`.
*   **Keyboard Layout:** Select your keyboard layout. This is important for correct input during installation. Examples: `us`, `de`, `fr`.

### 5.2. Select Mirrors
*   **Purpose:** Choose geographical regions for package download servers. The installer will present a list of regions.
*   **Action:** Select your region (e.g., `United States`) or `Global`. You can select multiple regions to ensure a good fallback.
*   **Tip:** `archinstall` will often try to pre-select mirrors based on your location. You can review and adjust these.

### 5.3. Additional Repositories (multilib)
*   **Purpose:** Enables extra package repositories.
*   **Action:** You will be prompted to enable additional repositories. Toggle `multilib` to enable it.
*   **Why for Gaming?**: The `multilib` repository is essential for installing 32-bit libraries required by Steam and many Windows games running through compatibility layers like Proton.
*   **Tip:** Ensure this option is checked!

### 5.4. Disk Configuration
This is arguably the most critical step in the installation process, as it dictates how your Arch Linux system will be organized and stored on your drive. **Proceed with extreme caution, as incorrect choices can lead to irreversible data loss.**

*   **Select a Drive:** When `archinstall` prompts you to select a drive, it will list available storage devices (e.g., `/dev/sda`, `/dev/nvme0n1`).
    *   **Action:** **Carefully select the drive where you want to install Arch Linux.** Refer back to **Step 2: Identify Your Drives** if you are unsure which drive to pick. Choosing the wrong drive *will* erase data on that drive. Double-check your selection!

*   **Disk Layout Options:** After selecting your target drive, `archinstall` will present you with options for how to manage the partitions on that drive.

    *   **`Guided` (Highly Recommended for Beginners):**
        *   **Description:** This is the easiest and safest option if you are performing a clean installation and want `archinstall` to handle the partitioning for you. It will automatically set up a standard partitioning scheme on the selected drive.
            *   **Typical Layout:**
                *   **EFI System Partition (ESP):** Around 512MB, formatted as FAT32, mounted to `/boot/efi`. Essential for UEFI bootloaders.
                *   **Root Partition (`/`):** The primary partition where your operating system files, applications, and system data reside. This will take up the majority of your disk space.
                *   **Swap Partition:** Used as virtual memory when your RAM is full, or for hibernation. Size varies but commonly 2GB-8GB, or roughly equal to your RAM.
        *   **Action:** Select `Guided` and follow the prompts. `archinstall` will then ask you to choose a filesystem for your root partition (e.g., `ext4`, `btrfs`).
        *   **Recommendation:** Use this if you are performing a clean install and are comfortable with `archinstall` managing the entire disk, or if you are new to partitioning. It's designed for simplicity and reliability.

    *   **`Manual` (For Experienced Users / Custom Layouts):**
        *   **Description:** This option grants you full control over partitioning. You will need to manually create, modify, and assign partitions and filesystems. This is more flexible but also more complex and significantly increases the chance of error if you are unfamiliar with Linux partitioning.
        *   **Pre-`archinstall` Preparation (Using `cfdisk`):**
            If you choose `Manual`, it's often best to create your partitions *before* running `archinstall`. You can do this using a command-line partitioning tool like `cfdisk`.
            1.  **Launch `cfdisk`:**
                ```/dev/null/cfdisk_command.sh#L1-1
                cfdisk /dev/sdX # Replace /dev/sdX with your target drive, e.g., /dev/sda or /dev/nvme0n1
                ```
            2.  **Choose Partition Table Type:**
                *   `archinstall` will usually detect your boot mode (UEFI or BIOS).
                *   For **UEFI systems (most modern computers)**, select `gpt` (GUID Partition Table).
                *   For **older BIOS/Legacy systems**, select `dos` (MBR partition table).
                *   **Tip:** If unsure, `gpt` is generally preferred for new installations.
            3.  **Clear Existing Partitions (Optional but Recommended for Clean Install):**
                *   If the disk has existing partitions, use the `Delete` option to remove them until the entire drive shows as `Free space`.
            4.  **Create Partitions:**
                *   **EFI System Partition (for UEFI systems ONLY):**
                    *   Select `Free space` and choose `New`.
                    *   Enter Size: `512M` (or 1024M if you plan to install multiple kernels).
                    *   Select `Type` and choose `EFI System`.
                *   **Swap Partition:**
                    *   Select remaining `Free space` and choose `New`.
                    *   Enter Size: Recommended to be `2G` to `8G` (or roughly equal to your RAM, up to 1.5x RAM if you need hibernation).
                    *   Select `Type` and choose `Linux swap`.
                *   **Root Partition (`/`):**
                    *   Select the remaining `Free space` and choose `New`.
                    *   Accept the default (maximum) size.
                    *   Select `Type` and choose `Linux filesystem`.
                *   **(Optional) Home Partition (`/home`):**
                    *   If you want a separate `/home` partition (recommended for easier reinstallations without affecting personal data), calculate the desired size for `/` and `/home`.
                    *   Create the `/` partition first, leaving enough free space for `/home`.
                    *   Then, select the remaining `Free space` and choose `New` for `/home`, setting its type to `Linux filesystem`.
            5.  **Write Changes:**
                *   Select `Write`. Type `yes` to confirm. **This step is irreversible!**
            6.  **Quit `cfdisk`:**
                *   Select `Quit`.
            After exiting `cfdisk`, you can then run `archinstall`. When `archinstall` asks for disk configuration, select `Manual`, and you will then be able to assign your pre-created partitions to mount points (e.g., `/`, `/boot/efi`, `[SWAP]`, `/home`).

        *   **Recommendation:** **Only choose this if you are experienced with Linux partitioning, mount points, and filesystem types.** For beginners, the `Guided` layout is highly recommended.

    *   **`Block device` (For Specialized, Single-Partition Setups):**
        *   **Description:** This option allows you to select an entire block device (e.g., `/dev/sda`) or a pre-existing partition (e.g., `/dev/sda1`) to be used as the root filesystem directly, without `archinstall` creating additional partitions. This is typically for very specific, minimalist, or custom setups where you manage partitioning outside `archinstall` and just want to format a single target.
        *   **Recommendation:** Not for beginners. Requires a clear understanding of what you are doing and why you are skipping a standard partitioning scheme.

*   **Filesystem Selection (Post-Layout Choice):** If you choose `Guided` or have opted for `Manual` partitioning within `archinstall`, you will then select a filesystem for your partitions (at least for the root partition).

    Here's a breakdown of common filesystems and their suitability:

    *   **ext4 (Fourth Extended Filesystem)**
        *   **Pros:** Highly stable, universally supported by Linux distributions, offers good performance for general desktop use, robust against data corruption, and has mature tooling. It's the long-standing default for a reason.
        *   **Cons:** Lacks advanced features found in newer filesystems like transparent compression, built-in snapshotting, or sophisticated volume management.
        *   **Recommendation:** **Excellent choice for beginners and the vast majority of general-purpose desktop or server installations.** Prioritizes simplicity, reliability, and ease of use. If you don't have specific advanced needs, `ext4` is your safest bet.

    *   **btrfs (B-tree Filesystem)**
        *   **Pros:** A modern copy-on-write (CoW) filesystem with advanced features. Key advantages include:
            *   **Snapshots:** Allows you to take read-only copies of your filesystem state at any point, enabling easy system rollbacks in case of bad updates or configuration changes.
            *   **Transparent Compression:** Can save disk space and improve read performance for compressible data.
            *   **Data Integrity:** Uses checksums for data and metadata, protecting against silent data corruption.
            *   **Subvolumes:** Flexible partitioning within a single filesystem.
            *   **RAID Support:** Built-in volume management for RAID configurations.
        *   **Cons:** More complex to manage and understand than `ext4`. Historically had some stability concerns (though greatly improved over time), and certain workloads might experience performance variations without proper tuning. Requires a bit more active maintenance (e.g., scrubbing, balancing) to ensure optimal health.
        *   **Recommendation:** Good for users who specifically desire advanced features like snapshots (e.g., for easy system recovery or experimentation), or those with specific data integrity and flexible storage management needs. It comes with a steeper learning curve than `ext4`.

    *   **XFS (Extended Filesystem)**
        *   **Pros:** Designed for high performance on very large filesystems (terabytes and petabytes) and parallel I/O operations. Excellent for handling large files and data-intensive applications like media servers, databases, or scientific computing.
        *   **Cons:** Less efficient with a very large number of small files. While it has journaling for recovery, it can be less robust than `ext4` in the event of sudden power loss in some scenarios. Snapshots are not a native feature of the filesystem itself, requiring LVM for similar functionality.
        *   **Recommendation:** Primarily used in server environments or for specific workloads involving extremely large files. Generally **not recommended for general desktop use by beginners**.

    *   **F2FS (Flash-Friendly Filesystem)**
        *   **Pros:** Specifically optimized for NAND flash-based storage devices (SSDs, eMMC, USB drives). Aims to extend the lifespan of SSDs and improve performance by minimizing write amplification and managing data efficiently on flash memory.
        *   **Cons:** Less mature and not as widely adopted as `ext4` or `XFS`. Performance can vary depending on the workload and specific flash hardware.
        *   **Recommendation:** A niche choice for those specifically optimizing for flash storage performance and longevity. Generally **not recommended for beginners as a primary root filesystem** due to its specific optimizations and less common usage.

### 5.5. Bootloader
*   `archinstall` will usually detect and configure the bootloader automatically. The common options you'll encounter are `GRUB`, `systemd-boot`, `rEFInd`, and `Limine`. Confirm the suggested option, or choose based on your preference:

    *   **GRUB (Grand Unified Bootloader)**
        *   **Pros:** The most widely used and feature-rich bootloader for Linux. Supports both UEFI and BIOS systems, can boot multiple operating systems (dual-booting), highly configurable, and has extensive documentation.
        *   **Cons:** Can be perceived as more complex to configure manually than `systemd-boot` due to its vast features.
        *   **Recommendation:** The default and recommended choice for most users, especially if you plan to dual-boot with Windows or other Linux distributions, or if you prefer a well-established and powerful bootloader.

    *   **systemd-boot (formerly Gummiboot)**
        *   **Pros:** Simple, fast, and minimalistic UEFI boot manager. Integrates well with `systemd`, easy to configure with plain text files, loads the kernel directly.
        *   **Cons:** Only works on UEFI systems (no BIOS support). Less feature-rich than GRUB (e.g., no themes, limited dual-booting capabilities for non-UEFI OSes).
        *   **Recommendation:** An excellent choice for UEFI-only systems, particularly if Arch Linux is your only operating system or if you value simplicity and speed.

    *   **rEFInd**
        *   **Pros:** A clean, graphical boot manager for UEFI systems. Automatically detects bootable EFI applications (including Linux kernels, Windows, macOS), making it easy to boot various OSes without manual configuration. Highly customizable with themes.
        *   **Cons:** Only works on UEFI systems (no BIOS support). Not a true bootloader but a boot manager that hands off to actual bootloaders or directly to kernels. Requires manual configuration for complex scenarios (e.g., specific kernel parameters).
        *   **Recommendation:** A good choice for UEFI users who appreciate a visually appealing and automatically detecting boot manager, especially if dual-booting multiple UEFI operating systems. It offers a more user-friendly selection interface than GRUB or systemd-boot for multiple bootable options.

    *   **Limine**
        *   **Pros:** A modern, lightweight, and fast bootloader for UEFI and BIOS systems. Designed for simplicity and security, it supports various boot protocols and offers a clean, minimal interface. It's often chosen for its robust design and active development.
        *   **Cons:** Newer than GRUB, so its community support might not be as extensive, and complex dual-boot scenarios might require more manual effort compared to GRUB's broad auto-detection.
        *   **Recommendation:** An excellent choice for users looking for a modern, fast, and secure bootloader for both UEFI and BIOS systems, especially if they prefer a minimalist approach or want to experiment with newer boot technologies. It's a solid alternative if GRUB feels too heavy or systemd-boot is too limited for their setup.

### 5.6. Hostname
*   Enter a hostname for your new system (e.g., `myarchpc`, `archdesktop`). This is the name your computer will have on the network.

### 5.7. Root Password
*   You will be prompted to set a password for the `root` user. The `root` user has administrative privileges.
    *   **Warning:** Leaving the root password blank disables the root account, relying on `sudo` for privilege elevation. This may cause you to lock yourself out of your system if `sudo` is not configured correctly for your regular user. If you choose to leave the root password blank, **you must ensure that your regular user is added to the `sudo` group in the next step** to be able to perform administrative tasks. Setting a strong root password is generally recommended for beginners.

### 5.8. Create a User Account (Recommended)
*   You'll be asked if you want to create a regular user account. **It is highly recommended to create a regular user account for daily use.** Running as `root` for everyday tasks is a security risk.
*   Enter a username (e.g., `linuxury`, `user`).
*   Set a strong password for your new user.
*   `archinstall` will ask if you want to add this user to the `sudo` group. **Say `yes`** to this, as it will allow your regular user to perform administrative tasks using `sudo`.

### 5.9. Profile Selection (Desktop Environment / Server)
This is where you select the general software stack for your new Arch Linux system. `archinstall` offers various "profiles"—collections of packages and configurations tailored for common use cases. **It's important to note that `archinstall` is actively developed, and the exact names and availability of profiles can change with updates.** Always read the prompts carefully.

*   **Standard Profiles (Visible by default):** These are the most common and user-friendly options.
    *   **`desktop`**:
        *   **Description:** This profile sets up a full graphical desktop environment (GUI). After selecting `desktop`, `archinstall` will present you with a list of available Desktop Environments (DEs) to choose from, commonly including `KDE Plasma`, `GNOME`, `Xfce`, `Cinnamon`, `MATE`, `Budgie`, `Deepin`, `Enlightenment (E17)`, `LXQt`, `LXDE`.
        *   **Pros:** Provides a complete and familiar computing experience right after installation. Includes essential graphical utilities and applications.
        *   **Cons:** Installs a larger number of packages, requiring more disk space and potentially longer installation times compared to minimal setups.
        *   **Recommendation:** **Highly recommended for most beginners and general users** who want a graphical interface for daily computing. For a modern, feature-rich, and well-supported experience, `KDE Plasma` or `GNOME` are popular choices. `Xfce` and `Cinnamon` are generally lighter and good for older hardware or those preferring a more traditional interface.
    *   **`server`**:
        *   **Description:** Installs a base system optimized for server roles, typically without a graphical interface. It includes server-specific tools and services for managing network, storage, and various server applications.
        *   **Pros:** Minimal footprint for server tasks, better resource utilization, and improved security due to fewer installed components.
        *   **Cons:** No GUI by default, requiring familiarity with the command line for all operations.
        *   **Recommendation:** For users setting up a headless server (without a monitor or graphical interface) or experienced users who prefer to build their graphical environment (e.g., a tiling window manager) from a server base.
    *   **`minimal`**:
        *   **Description:** Installs only the absolute essential packages required for a bootable Arch Linux system. This is the most barebones installation.
        *   **Pros:** Extremely small disk space footprint, maximum control over every installed package, and fastest installation time.
        *   **Cons:** No GUI, very basic command-line environment. Requires extensive manual configuration to become a usable desktop or server, including setting up networking, users, and any desired software.
        *   **Recommendation:** For advanced users who want full control over every aspect of their system, or for building highly specialized, lightweight systems. **Definitely not for beginners.**

*   **Advanced Profiles (Accessed with `--advanced` flag):**
    When running `archinstall`, you can pass the `--advanced` flag (e.g., `archinstall --advanced`) to reveal additional, more specialized profiles. These often cater to specific niche use cases, alternative configurations, or specific software stacks beyond the common options.
    *   **Examples of Advanced Profiles (exact names and availability vary greatly with `archinstall` updates):**
        *   **Specific Desktop Environments (Direct Install):** `gnome`, `kde`, `xfce`, `cinnamon`, `mate`, `budgie`, `deepin`, `e17` (Enlightenment), `lxqt`, `lxde` (these often install directly without a sub-menu).
        *   **Window Managers:** `i3`, `sway`, `bspwm`, `qtile` (tiling window managers).
        *   **Minimal Graphical Base:** `xorg` (installs just the Xorg server), `wayland` (installs a basic Wayland compositor setup).
        *   **Server Variants:** `server_headless` (a server setup without a graphical environment).
        *   **Specialized System Setups:** `developer` (includes common development tools), `hardened` (focuses on security features), `zen` (system with the `linux-zen` kernel).
        *   **System Variants:** `lts-desktop` (a desktop environment based on the `linux-lts` kernel).
        *   And other specific configurations that may appear.
    *   **Pros:** Offers fine-grained control and pre-configured setups for specific environments or needs, saving manual setup time for those specific use cases.
    *   **Cons:** May require a better understanding of the underlying components and their dependencies. Less general-purpose and may not be as well-tested as the broader `desktop` profile.
    *   **Recommendation:** Explore these only if you have a specific advanced setup in mind and understand the implications of each profile. For beginners, sticking to the standard `desktop` profile (and then choosing your preferred DE) is the most straightforward and supported path.

*   **Custom Package Selection (For Highly Advanced Users):**
    The `archinstall` script also provides an option to skip profiles entirely. This allows you to manually select individual packages to be installed, giving you ultimate control over your system's software.
    *   **Recommendation:** This approach is for highly advanced users who know exactly what packages they need and how to configure them post-installation. It requires significant knowledge of Arch Linux's package ecosystem and system configuration.
    
**Important Note:** The profiles shipped with `archinstall` are specific to the script and are *not* officially supported by Arch Linux package maintainers. They represent convenient starter setups. Always review the details of each profile (if possible, within the script itself) before making a selection, and be aware that specific profile names and their contents can evolve with `archinstall` updates.

### 5.10. Audio Server (If Desktop Profile Selected)
*   If you selected a desktop profile, you might be asked to choose an audio server. The main options are `PipeWire` and `PulseAudio`.

    *   **PipeWire**
        *   **Pros:** Modern, low-latency multimedia server designed to handle audio, video, and hardware inputs. Aims to be a universal solution, replacing both PulseAudio (for pro-audio and general audio) and JACK (for pro-audio), while also managing Wayland screen sharing. Excellent compatibility, especially for Flatpak applications, and is generally considered the future of Linux audio.
        *   **Cons:** Being newer, some very specific niche setups or older applications might occasionally require more configuration (though this is increasingly rare).
        *   **Recommendation:** **Highly recommended for all users, especially beginners.** It offers the best overall experience, modern features, and is becoming the default for most major distributions.

    *   **PulseAudio**
        *   **Pros:** A long-standing, robust sound server that handles audio routing, mixing, and volume control. Widely supported by applications and hardware.
        *   **Cons:** Can sometimes introduce latency for professional audio applications. It's an older design and lacks some of PipeWire's advanced capabilities (e.g., video streaming, better Wayland integration).
        *   **Recommendation:** Still a viable option, but `PipeWire` is generally preferred now for its broader capabilities and future-proofing. If you have specific legacy hardware or software known to only work well with PulseAudio, it might be an option.

    `ALSA` (Advanced Linux Sound Architecture) is the kernel-level sound system that both PipeWire and PulseAudio build upon. You will always have ALSA, but an audio server like PipeWire or PulseAudio sits on top to provide more advanced features and easier management for user applications.

### 5.11. Kernel
*   You'll be prompted to choose a kernel. The common options include `linux`, `linux-lts`, and `linux-zen`.

    *   **`linux-zen` (Optimized for Desktop & Gaming)**
        *   **Pros:** This kernel is specifically optimized for desktop responsiveness, low-latency, and gaming performance. It includes various tweaks and patches not found in the mainline kernel, making it an excellent choice for modern and recent hardware where a smooth, interactive experience is desired. Often results in better frame pacing and reduced input lag.
        *   **Cons:** May be slightly less rigorously tested than the mainline kernel due to its more aggressive optimization. While generally stable, specific hardware configurations might occasionally exhibit quirks.
        *   **Recommendation:** **Highly recommended for users with modern and recent equipment and those who prioritize gaming, multimedia, or a highly responsive desktop experience.** If performance and low latency are key for you, `linux-zen` is an excellent choice.

    *   **`linux` (Mainline Kernel)**
        *   **Pros:** This is the latest stable kernel from the upstream Linux project, offering the newest features, broadest hardware support, and general performance improvements. It represents the bleeding edge of kernel development.
        *   **Cons:** Being the newest, it might occasionally introduce regressions or require more frequent updates, potentially leading to less perceived stability compared to an LTS or Zen kernel for some specific use cases.
        *   **Recommendation:** A solid choice for general desktop use where you want the latest hardware support and features, and for users who prefer to stick close to the upstream kernel development.

    *   **`linux-lts` (Long-Term Support Kernel)**
        *   **Pros:** This kernel receives bug fixes and security updates for a longer period (typically several years), but does not introduce major new features. It's known for its exceptional stability and reliability.
        *   **Cons:** May not have the very latest hardware support or performance optimizations found in the mainline or Zen kernels.
        *   **Recommendation:** Ideal for users who prioritize stability over bleeding-edge features, such as those running servers, production machines, or systems where maximum uptime and minimal changes are desired. Also a good fallback if you experience issues with other kernels.

    *   **Other Kernels (Advanced Users):**
        *   `linux-hardened`: Focuses on security enhancements and hardening measures.
        These are generally for more experienced users with very specific needs.

### 5.12. Network Configuration
*   `archinstall` can configure wired interfaces using `systemd-networkd` or copy the configuration used on the ISO. That means, if you configured a wireless interface using `iwctl` on the live environment, its configuration (including the network password) will be copied to the installed system. It also copies the configuration for wired interfaces that is present on the ISO.

### 5.13. Time Zone
*   Select your geographical region and city to set the correct time zone. Examples: `America/New_York`, `Europe/London`, `Asia/Tokyo`.

### 5.14. Automatic Time Sync (NTP)
*   Enable `NTP` (Network Time Protocol) for automatic time synchronization. This is highly recommended.

### 5.15. Additional Packages
*   This prompt allows you to add any extra packages you'd like to install *immediately* after the base system and selected profile are set up. While you can always install more packages later with `sudo pacman -S <package_name>`, adding critical ones here can streamline your initial setup.

    **Interactive Search (Important!):**
    When prompted for additional packages, you can type `/` (forward slash) to activate an interactive search function. As you type a package name, `archinstall` will filter the available packages, making it easier to find what you need. Press `Tab` to select or unselect multiple packages, and then press `Enter` to confirm your selection(s) and continue typing to search for others, or press `Enter` on an empty line to finish.

    **Bare Minimum Recommendations (if not included by your profile):**
    If you've chosen a comprehensive desktop profile like KDE Plasma, many common utilities will already be included. However, it's a good practice to ensure these critical packages are present:

    *   **Web Browser:**
        *   `firefox`: Essential for browsing the internet and accessing documentation immediately after installation.
    *   **CPU Microcode:** Crucial for system stability and security, addressing potential processor bugs. **Choose only one based on your CPU manufacturer:**
        *   `amd-ucode`: For AMD processors.
        *   `intel-ucode`: For Intel processors.

    **Example (for AMD CPU, with KDE profile):**
    ```/dev/null/example_packages.sh#L1-1
    firefox amd-ucode
    ```
    (Remember to choose either `amd-ucode` or `intel-ucode`, not both, and only add packages not already covered by your chosen profile.)

### 5.16. Save and Install
*   `archinstall` will present you with a summary of all your choices. **Review this carefully!**
*   If everything looks correct, confirm that you want to proceed with the installation.
*   The installation process will begin, downloading and installing packages. This may take some time depending on your internet speed and the profile you chose.

## Step 6: Post-Installation and Reboot
1.  Once the installation is complete, you should see green text indicating that it is safe to reboot.
2.  Type `reboot` and press Enter:
    ```/dev/null/reboot.sh#L1-1
    reboot
    ```
3.  Remove the Arch Linux live medium when prompted or during the reboot process.
4.  Your system should now boot into your newly installed Arch Linux. If you installed a desktop environment, you should be greeted by a login manager (e.g., SDDM for KDE, GDM for GNOME).
5.  Log in with the regular user account you created.

## Congratulations!
You have successfully installed Arch Linux using `archinstall`! From here, you can start customizing your system, installing your favorite applications, and exploring the power and flexibility of Arch Linux.

### Further Steps (Optional)
*   **Update Your System:** Always keep your system updated:
    ```/dev/null/update_system.sh#L1-1
    sudo pacman -Syu
    ```
*   **Install a Text Editor:** If you didn't install one, `nano` is a simple command-line editor: `sudo pacman -S nano`.
*   **Explore the ArchWiki:** The [ArchWiki](https://wiki.archlinux.org/) is your best friend for any further configuration, troubleshooting, or learning about Arch Linux.
