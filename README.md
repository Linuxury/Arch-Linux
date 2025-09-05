# Setting up Arch Linux with KDE (For Gaming).

### A. What to do before running the Arch-install script.

> Let's assume you have downloaded and loaded it into your PC/Laptop, ready for installation.

Let's start by making some changes to `pacman.conf`. You will need to use a text editor; you could use `nano` or `vim`. For this guide, we will use `nano`. Add the following command to start:

```javascript
sudo nano /etc/pacman.conf
```

> We are only looking for a few changes in this file. Find **# Misc options** in that area, we are `uncommenting` `Color` and `VerbosePkgLists` by removing `#`, and look a few lines below for `ParallelDownloads` and change the number 5 to how many parallel downloads you are enabling. It's recommended to have a 1-to-1 ratio of your CPU cores for best performance. Also, add to the last line of that group `ILoveCandy`.

```javascript
# Misc options
#UseSyslog
Color
#NoProgressBar
CheckSpace
VerbosePkgLists
ParallelDownloads = 24
DownloadUser = alpm
#DisableSandbox
ILoveCandy
```

> By performing these changes, you will enable terminal bash commands to have color on them and have a faster download because the parallel downloads are increased.

Now, let's run a benchmark and select the fastest mirrors for pacman to use. [optional]

> Paste the following command into the terminal:

```javascript
sudo reflector --country 'United States' --latest 20 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```
### B. Arch-install

You can just run the following command to start the script.

```javascript
archinstall
```

During this process, you will select your Mirror location, repositories, hostname, root password, add user and password, audio settings, network settings, additional packages, timezone, and install once everything is selected. (Go through all options so you don't miss anything and familiarize yourself with the process.)

> After selecting your local `Mirror`, select `Multilib` from repositories for 32-bit libraries.

> For Additional Packages you could search by typing " **/&#32;**". `/wget`    `/firefox`

### C. At first, boot into your desktop environment.

Now that you have completed the initial installation and are on your desktop environment of choice, there are a few things to attend to before you call it a day.



> Install bash-completion for searches with "TAB"

```javascript
sudo pacman -S bash-completion
```

Once installed run

```javascript
nano ~/.bashrc
```

Add to the last line so that all sudo users can use it by default

```javascript
# Bash-Completion
#Complete -cf sudo
complete -cf sudo

    if [ -f /usr/share/bash-completion/bash_completion ]; then
        . /usr/share/bash-completion/bash_completion
    fi
```

Now refresh by running

```javascript
source ~/.bashrc
```

> Let's start with AUR Helper, either paru or yay

First, update your system by using:

`sudo pacman -Syu`

Then install the required packages:

`sudo pacman -S git base-devel --needed`

Now you will copy the paru repository:

`git clone https://aur.archlinux.org/paru.git`

Navigate to the paru directory:

`cd paru`

Once in the paru folder, just build and install paru by using the command:

`makepkg -si`

Once completed, just remove the folder by using the following command:

`cd ..
rm -rf paru`

It should be something like this

```javascript
sudo pacman -S git base-devel --needed
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
cd ..
rm -rf paru
```

For yay, it's almost the same. Use the following commands:

```javascript
sudo pacman -S git base-devel --needed
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd ..
rm -rf yay
```

> Now let's take care of Micro Code

First, run this in the  terminal 

```javascript
lscpu | grep "Vendor ID"
```

This will confirm what type of CPU you currently have in your machine (sanity check if not sure).

For AMD use

```javascript
sudo pacman -S amd-ucode
```

For Intel use

```javascript
sudo pacman -S intel-ucode
```

Once that its completed, you need to update your Grub so it's recognized and included on boot.

```javascript
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo cat /boot/grub/grub.cfg | grep ucode
```

> Perform a Reboot and run in a terminal for Essential tools

```javascript
sudo pacman -S curl wget zip unzip nano net-tools dnsutils firefox --needed
```

> Next, it's optional but highly recommended

Chaotic AUR

Include the following command in the terminal

```javascript
sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
sudo pacman-key --lsign-key 3056513887B78AEB

sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst'
sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
```
Then, we append (adding at the end) the following to /etc/pacman.conf:
```javascript
sudo nano /etc/pacman.conf
```
```javascript
[chaotic-aur]
Include = /etc/pacman.d/chaotic-mirrorlist
```
Recommend running a full system update, along with syncing our mirrorlist.
```javascript
sudo pacman -Syu
```
### D. Essential Applications

Now that the back-end is taken care of, we can concentrate on installing a few other applications that I use on all my installations and have become my default. Bluez and Bluez-utils for my Bluetooth, Fastfetch, Firefox, Fish, Ghostty, Gamemode, Kvantum, NetworkManager for my network, Mangohud, OpenRGB, Onlyoffice-bin, Pamac-AUR, Pacman-contrib, Starship, Thunderbird, Steam, and UFW.

> I have them in one command, but you get the idea and could just run them individually or not at all, your choice.

```javascript
paru -Syyu bluez bluez-utils fastfetch fish ghostty gnome-disk-utility kvantum kwalletmanager kdeconnect legacy-launcher networkmanager nerd-fonts mangohud openrgb onlyoffice-bin pamac-aur pacman-contrib power-profiles-daemon proton-ge-custom protonplus protontricks reflector rsync starship thunderbird timeshift ufw kdotool loupe topgrade paru plasma6-themes-layan-git tela-icon-theme-git breezex-cursor-theme plasma6-wallpapers-wallpaper-engine-git plasma6-applets-arch-update-notifier heroic-games-launcher-bin firefox jre21-openjdk jre22-openjdk jre23-openjdk flatpak sgdboop-bin lsfg-vk-git vvave okular showtime fluent-reader-bin alsa-plugins giflib glfw gst-plugins-base-libs libjpeg-turbo libva libxslt mpg123 openal opencl-icd-loader ttf-liberation vulkan-tools winetricks wine gamemode gamescope lutris steam lib32-alsa-plugins lib32-giflib lib32-gst-plugins-base-libs lib32-gtk3 lib32-libjpeg-turbo lib32-libva lib32-mpg123 lib32-ocl-icd lib32-opencl-icd-loader lib32-openal --needed

```
Enable Flatpak
```javascript
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```
### E. Enable Trim for your SSD

On your terminal, run the following command

```javascript
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
```

### F. Enable Bluetooth

On your terminal, run the following command

```javascript
sudo systemctl start bluetooth.service
sudo systemctl enable bluetooth.service	
```

To confirm it's working, use the following command

```javascript
systemctl status bluetooth.service
```

### G. Enable Network Management

On your terminal, run the following command

```javascript
sudo systemctl start NetworkManager.service
sudo systemctl enable NetworkManager.service
```

To confirm it's working, use the following command

```javascript
systemctl status NetworkManager.service
```

### H. Enable your Firewall (UFW)

On your terminal, run the following command

```javascript
# Enable UFW
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

Then run the following command [optional]
>If you use `Steam`, add the following to enable features from `SteamOS` through your network.
```javascript
# Allow specified ports and services
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

Finally, to confirm they have all been added (they are mostly to allow Steam services)

```javascript
# Show the status of UFW
sudo ufw status verbose
```

### I. GitHub Dot-Files

Open a Terminal

Navigate to the desired directory where they want to store the dotfiles, e.g., `~/Documents/GitRepos`

`mkdir -p ~/Documents/GitRepos` <br/>
`cd ~/Documents/GitRepos`

Clone the repository using the provided URL:

`git clone https://github.com/linuxury/dotfiles.git`


**Setting Up Symlinks**:

- After cloning, they should navigate to the cloned `dotfiles` directory:

cd dotfiles

- They can then create symlinks for the dotfiles as described earlier. Here’s a quick summary of the commands they would need to run:

- Create symlinks for each application <br />
ln -s ~/Documents/GitRepos/dotfiles/MangoHud ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/Kvantum ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/fish ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/fastfetch ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/ghostty ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/bazaar-config ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/helix ~/.config <br />
ln -s ~/Documents/GitRepos/dotfiles/starship ~/.config <br />
    
```javascript
mkdir -p ~/Documents/GitRepos
cd ~/Documents/GitRepos
git clone https://github.com/linuxury/dotfiles.git
cd dotfiles
ln -s ~/Documents/GitRepos/dotfiles/MangoHud ~/.config
ln -s ~/Documents/GitRepos/dotfiles/Kvantum ~/.config
ln -s ~/Documents/GitRepos/dotfiles/fish ~/.config
ln -s ~/Documents/GitRepos/dotfiles/fastfetch ~/.config
ln -s ~/Documents/GitRepos/dotfiles/ghostty ~/.config
ln -s ~/Documents/GitRepos/dotfiles/bazaar-config ~/.config
ln -s ~/Documents/GitRepos/dotfiles/helix ~/.config
ln -s ~/Documents/GitRepos/dotfiles/starship ~/.config
```    
<img width="1118" height="670" alt="image" src="https://github.com/user-attachments/assets/d5646ff0-a03e-48ce-8193-8f25294f2209" />

### J. Steam Launch Options

```javascript
PROTON_ENABLE_WAYLAND=1 PROTON_ENABLE_HDR=1 PROTON_DLSS_INDICATOR=1 PROTON_FSR4_UPGRADE=1 PROTON_DLSS_UPGRADE=1 gamemoderun %command%
```
List of commands:</br>
`PROTON_ENABLE_WAYLAND=1` = Will enable Wayland driver support </br>
`PROTON_ENABLE_HDR=1` = Will enable HDR </br>
`PROTON_DLSS_INDICATOR=1` `PROTON_DLSS_UPGRADE=1` = Will enable DLSS/FSR conversion in games. </br>
`PROTON_FSR4_UPGRADE=1` = Will upgrade FSR 3.1 to FSR 4</br>
`SteamDeck=1` = Will enable Steam Deck Mode (some games only work with this flag)</br>
`gamemoderun` = Will enable a mode that increases the priority of the game.</br>
`mangohud` = Will enable Game Statistics in-game (if the Steam overlay doesn't work)</br>
`%command%` = Always add this to any command for it to work


### Additional Steps

- **Proton Experimental**: Go inside `Steam` and look for it on installed applications, then go to options and select the beta option, and select `bleeding-edge` 
- **ProtonPlus**: After running at least once `Steam` and `Lutris`, you will be able to download proton files for them. For `Steam`, you need `Proton-CachyOS`, and for `Lutris`, you need `Wine-Staging-Tkg` (at least until `Lutris` gets updated and the current error gets fixed).

### K. GRUB [optional]

By default, `GRUB` displays a couple of useful options with a timer added to them, but I prefer not to use that. I prefer a faster boot with minimal information shown on the screen. Let's open the `GRUB` config files located in `/etc/default/grub` with a text editor.

```javascript
sudo nano /etc/default/grub
```
Look for the following: 
- `GRUB_TIMEOUT=5` and change it to `0`
- `GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"` and change it to `"loglevel=0 quiet splash"`
- `GRUB_TIMEOUT_STYLE=menu` and change it to `hidden`

Add this below the `GRUB_TIMEOUT_STYLE` line
```javascript
GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
  ```

After all changes, it should look something similar to this:
<img width="1116" height="674" alt="image" src="https://github.com/user-attachments/assets/bf3ecc61-4578-4576-8bfc-9e5702b3114d" />

Run this to generate a new `GRUB`
```javascript
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### L. SDDM Numlock enabled by default [optional]

By default, `SDDM` will have this feature off, so let's change it

```javascript
sudo mkdir -p /etc/sddm.conf.d
sudo nano /etc/sddm.conf.d/kde_settings.conf
```

Now, all you need to do is look for `[General]` and add a new line to that group with

`Numlock=on`
```javascript
[Autologin]
Relogin=false
Session=
User=

[General]
HaltCommand=/usr/bin/systemctl poweroff
RebootCommand=/usr/bin/systemctl reboot
Numlock=on

[Theme]
Current=breeze

[Users]
MaximumUid=60513
MinimumUid=1000
```

### M. Add Fish to /etc/shells [optional]
Ensure the Fish shell's path is listed in /etc/shells. If it's not present, add it by running: 
```javascript
echo /usr/bin/fish | sudo tee -a /etc/shells
```
Change default shell with chsh: Use the chsh command to change your default shell to Fish:
```javascript
chsh -s /usr/bin/fish
```

You will be prompted to enter your password.

Log out and log back in: For the change to take effect, log out of your current session and then log back in. Your terminal should now open with Fish as the default shell.

### N. Enabling Global Menu [optional]

For some apps, such as Visual Studio Code, the global menu may not function properly or may be attached to the parent app instead of the panel.
Terminal window

To enable global menu support, run the command and restart the app.
```javascript
sudo pacman -S appmenu-gtk-module libdbusmenu-glib
```
### O. Arch Update Counter Popup Color Layan [optional]

Name: White (#D8DEE9) <br/>
Source: Teal (#88C0D0) <br/>
From Version: Gray (#4C566A) <br/>
Separator: Green (#A3BE8C) <br/>
To Version: Bright white (#ECEFF4) <br/> <br/>
<img width="510" height="398" alt="image" src="https://github.com/user-attachments/assets/acb87e0c-409c-4aca-aeee-9d4e545f4dc5" />

### P. Bazaar (FLATPAK Store) [optional]

Run this in Terminal for installation
```javascript
flatpak install flathub io.github.kolunmi.Bazaar
```

Run This for custom config
```javascript
flatpak run io.github.kolunmi.Bazaar --extra-content-config=~/.config/bazaar-config/config.yaml
```
--TEST-- <br />
# Setting up Arch Linux with KDE for Gaming

This guide configures a fresh Arch Linux installation with KDE Plasma, optimized for gaming. It covers pre-installation setup, the `archinstall` script, post-installation configuration, gaming tools, and customizations. Commands are grouped for easy copying, with detailed explanations for beginners. Run `sudo pacman -Syu` before starting, and consider testing in a virtual machine (e.g., VirtualBox or QEMU).

> **Note**: Commands assume a terminal. If a command fails, consult the [Arch Wiki](https://wiki.archlinux.org/) or Arch Linux forums.

## A. Pre-Installation Configuration

Configure the package manager (`pacman`) for performance and usability.

### Why?
- Colors and verbose lists improve readability.
- Parallel downloads speed up installation, matching CPU core count.
- Fast mirrors ensure quick downloads.

### Steps
1. **Edit `pacman.conf`**:
   - Enable `Color`, `VerbosePkgLists`, and `ILoveCandy`. Set `ParallelDownloads` to your CPU cores.
   ```bash
   sudo nano /etc/pacman.conf
   ```
   Modify under `# Misc options`:
   ```plaintext
   # Misc options
   Color
   CheckSpace
   VerbosePkgLists
   ParallelDownloads = 8  # Replace with `nproc` output
   ILoveCandy
   ```
   Save (`Ctrl+O`, `Enter`, `Ctrl+X`).

2. **Optimize mirrors**:
   - Use `reflector` to select fast mirrors for your region.
   ```bash
   sudo pacman -S reflector
   sudo reflector --country 'United States' --latest 20 --sort rate --save /etc/pacman.d/mirrorlist
   ```
   > **Note**: Replace `--country 'United States'` with your country (e.g., `--country 'Germany,France'`) or omit for all mirrors.

3. **Verify**:
   ```bash
   cat /etc/pacman.d/mirrorlist
   sudo pacman -Syy
   ```

## B. Run the Archinstall Script

The `archinstall` script simplifies installation, setting up KDE Plasma for gaming.

### Why?
- Automates installation for beginners.
- KDE Plasma is lightweight and gaming-friendly.
- `multilib` enables 32-bit libraries for Steam.

### Detailed Walkthrough
Run:
```bash
archinstall
```
Navigate with arrow keys, `Enter` to select, `Space` to toggle.

#### 1. Select Mirrors
- **Purpose**: Choose servers for fast package downloads.
- **Action**: Select your region (e.g., `United States`) or `Global`.
- **Why for Gaming?**: Speeds up installation.
- **Tip**: Keep default if `reflector` was used.

#### 2. Disk Configuration
- **Purpose**: Partitions the drive.
- **Action**: Choose `Auto-partition` (EFI, root, 4–8 GB swap, `ext4` filesystem).
- **Why for Gaming?**: Swap handles memory-heavy games; `ext4` is stable.
- **Tip**: Back up data; check SSD (`lsblk -d -o NAME,ROTA`, ROTA=0).

#### 3. Profile
- **Purpose**: Selects desktop environment.
- **Action**: Choose `desktop` > `plasma`.
- **Why for Gaming?**: KDE Plasma supports Wayland, HDR.
- **Tip**: Avoid minimal profiles unless advanced.

#### 4. Audio
- **Purpose**: Selects audio server.
- **Action**: Choose `pipewire`.
- **Why for Gaming?**: Low-latency audio for games.
- **Tip**: Preferred over `pulseaudio`.

#### 5. Kernel
- **Purpose**: Selects kernel.
- **Action**: Choose `linux-zen`.
- **Why for Gaming?**: Reduces latency.
- **Tip**: Use `linux` if hardware issues arise.

#### 6. Network Configuration
- **Purpose**: Sets up internet.
- **Action**: Select `NetworkManager`.
- **Why for Gaming?**: Stable online gaming.
- **Tip**: Verify post-install (`ping archlinux.org`).

#### 7. Timezone
- **Purpose**: Sets system clock.
- **Action**: Choose region/city (e.g., `America/New_York`).
- **Why for Gaming?**: Syncs with game servers.
- **Tip**: Use `Tab` to search.

#### 8. Hostname
- **Purpose**: Names computer.
- **Action**: Enter name (e.g., `gaming-pc`).
- **Why for Gaming?**: Identifies system in multiplayer.
- **Tip**: Use lowercase, no special characters.

#### 9. Root Password
- **Purpose**: Sets admin password.
- **Action**: Enter secure password.
- **Why for Gaming?**: Secures SSH/remote access.
- **Tip**: Store securely.

#### 10. User Account
- **Purpose**: Creates daily-use account.
- **Action**: Add user (e.g., `gamer`), set password, enable `sudo`.
- **Why for Gaming?**: Improves security.
- **Tip**: Choose simple username.

#### 11. Additional Packages
- **Purpose**: Installs extra packages.
- **Action**: Enter `wget firefox vulkan-tools steam`.
- **Why for Gaming?**: Sets up web access, graphics, gaming.
- **Tip**: Use `/` to search.

#### 12. Additional Repositories
- **Purpose**: Enables `multilib` for 32-bit libraries.
- **Action**: Toggle `multilib`.
- **Why for Gaming?**: Required for Steam.
- **Tip**: Ensure checked.

#### 13. Bootloader
- **Purpose**: Installs bootloader.
- **Action**: Choose `grub`.
- **Why for Gaming?**: Supports kernel/microcode.
- **Tip**: Verify UEFI (`ls /sys/firmware/efi`).

#### 14. Install
- **Purpose**: Starts installation.
- **Action**: Review choices, select `Install`.
- **Why for Gaming?**: Sets up KDE system.
- **Tip**: Note errors, check logs (`journalctl -b`).

#### 15. Post-Installation
- **Purpose**: Completes setup.
- **Action**: Select `Reboot`.
- **Why for Gaming?**: Boots into KDE.
- **Tip**: Access GRUB (`Shift` or `Esc`) if boot fails.

### Verify
```bash
echo $XDG_CURRENT_DESKTOP  # Should output "KDE"
grep -A1 "\[multilib\]" /etc/pacman.conf
```

## C. Post-Installation Setup

Configure tools and services in KDE Plasma.

### Why?
- Bash completion improves usability.
- AUR helper simplifies package installation.
- Microcode enhances CPU stability.
- GPU drivers optimize gaming.
- Tools prepare the system.

### Steps
1. **Install bash completion**:
   ```bash
   sudo pacman -S bash-completion
   nano ~/.bashrc
   ```
   Add:
   ```bash
   # Enable bash completion
   [ -f /usr/share/bash-completion/bash_completion ] && . /usr/share/bash-completion/bash_completion
   complete -cf sudo
   ```
   Save (`Ctrl+O`, `Enter`, `Ctrl+X`), refresh:
   ```bash
   source ~/.bashrc
   ```

2. **Install AUR helper (Paru or Yay)**:
   ```bash
   sudo pacman -S git base-devel --needed
   git clone https://aur.archlinux.org/paru.git
   cd paru
   makepkg -si --needed
   cd ..
   pwd  # Verify directory
   rm -rf paru
   ```
   For `yay`:
   ```bash
   git clone https://aur.archlinux.org/yay.git
   cd yay
   makepkg -si --needed
   cd ..
   pwd
   rm -rf yay
   ```
   Verify:
   ```bash
   paru --version  # or yay --version
   ```
   > **Note**: If `makepkg` fails, check dependencies (`sudo pacman -S <package>`) or PKGBUILD (`less PKGBUILD`).

3. **Install CPU microcode**:
   ```bash
   lscpu | grep "Vendor ID"  # Look for "GenuineIntel" (Intel) or "AuthenticAMD" (AMD)
   ```
   For AMD:
   ```bash
   sudo pacman -S amd-ucode
   ```
   For Intel:
   ```bash
   sudo pacman -S intel-ucode
   ```
   Update GRUB:
   ```bash
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   sudo cat /boot/grub/grub.cfg | grep ucode
   ```
   > **Note**: For non-GRUB bootloaders, consult Arch Wiki.

4. **Install GPU drivers**:
   ```bash
   lspci | grep -i vga  # Check for NVIDIA, AMD, Intel
   ```
   For NVIDIA:
   ```bash
   sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils
   ```
   For AMD:
   ```bash
   sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon
   ```
   For Intel:
   ```bash
   sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel
   ```
   Verify:
   ```bash
   glxinfo | grep "OpenGL renderer"
   ```

5. **Install essential tools**:
   ```bash
   sudo pacman -S curl wget zip unzip nano net-tools dnsutils vulkan-tools --needed
   ```

6. **Reboot**:
   ```bash
   reboot
   ```
   Verify microcode:
   ```bash
   dmesg | grep microcode
   ```

## D. Gaming and Desktop Applications

Install gaming, productivity, and KDE customization packages, including Chaotic-AUR.

### Why?
- Gaming tools enable Windows games.
- Desktop apps provide a complete experience.
- Chaotic-AUR simplifies AUR package access.
- AUR packages add custom tools/themes.

### Steps
1. **Install Chaotic-AUR**:
   ```bash
   sudo pacman-key --recv-key 3056513887B78AEB --keyserver hkp://pool.sks-keyservers.net
   sudo pacman-key --lsign-key 3056513887B78AEB
   sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst'
   sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
   sudo nano /etc/pacman.conf
   ```
   Add:
   ```plaintext
   [chaotic-aur]
   Include = /etc/pacman.d/chaotic-mirrorlist
   ```
   Save, update:
   ```bash
   sudo pacman -Syyu
   ```
   > **Warning**: Chaotic-AUR is third-party. Review PKGBUILDs (`paru -Si <package>`) for safety.

2. **Install core packages**:
   - Use `\` for line continuation to improve readability.
   ```bash
   paru -S bluez bluez-utils fastfetch fish ghostty gnome-disk-utility kvantum kwalletmanager \
           kdeconnect legacy-launcher networkmanager nerd-fonts mangohud openrgb onlyoffice-bin \
           pamac-aur pacman-contrib power-profiles-daemon proton-ge-custom protonplus protontricks \
           reflector rsync starship thunderbird timeshift ufw kdotool loupe topgrade \
           plasma6-themes-layan-git tela-icon-theme-git breezex-cursor-theme \
           plasma6-wallpapers-wallpaper-engine-git plasma6-applets-arch-update-notifier \
           heroic-games-launcher-bin firefox jre21-openjdk jre22-openjdk jre23-openjdk flatpak \
           sgdboop-bin lsfg-vk-git vvave okular showtime fluent-reader-bin alsa-plugins giflib \
           glfw gst-plugins-base-libs libjpeg-turbo libva libxslt mpg123 openal opencl-icd-loader \
           ttf-liberation vulkan-tools winetricks wine gamemode gamescope lutris steam \
           lib32-alsa-plugins lib32-giflib lib32-gst-plugins-base-libs lib32-gtk3 \
           lib32-libjpeg-turbo lib32-libva lib32-mpg123 lib32-ocl-icd lib32-opencl-icd-loader \
           lib32-openal --needed
   ```
   > **Note**: The backslash (`\`) splits long commands for readability without affecting execution. Multiple Java versions may cause conflicts; consider only `jre-openjdk`. Review AUR PKGBUILDs.

3. **Enable Flatpak**:
   ```bash
   sudo pacman -S flatpak
   flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
   ```
   Verify:
   ```bash
   flatpak remote-list  # Should show flathub
   ```

4. **Install Bazaar**:
   ```bash
   flatpak install flathub io.github.kolunmi.Bazaar
   ```
   > **Note**: Bazaar configuration is handled by dotfiles in Section F.

## E. Enable System Services

Enable services for SSD maintenance, Bluetooth, networking, and firewall.

### Why?
- `fstrim` optimizes SSDs.
- Bluetooth and NetworkManager ensure connectivity.
- UFW secures the system with Steam compatibility.

### Steps
1. **Enable SSD trim (if SSD)**:
   ```bash
   lsblk -d -o NAME,ROTA  # ROTA=0 for SSD
   sudo systemctl enable fstrim.timer
   sudo systemctl start fstrim.timer
   ```

2. **Enable Bluetooth**:
   ```bash
   sudo systemctl start bluetooth.service
   sudo systemctl enable bluetooth.service
   ```
   Verify:
   ```bash
   systemctl status bluetooth.service
   ```
   > **Tip**: Use `bluez-tools` (`sudo pacman -S bluez-tools`) for pairing.

3. **Enable NetworkManager**:
   ```bash
   systemctl list-unit-files | grep network  # Check conflicts
   sudo systemctl disable netctl  # Disable if present
   sudo systemctl start NetworkManager.service
   sudo systemctl enable NetworkManager.service
   ```
   Verify:
   ```bash
   systemctl status NetworkManager.service
   ```

4. **Enable UFW**:
   ```bash
   # Enable UFW
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   sudo ufw allow ssh
   sudo ufw allow http
   sudo ufw allow https
   sudo ufw enable
   ```
   Then run the following command [optional]
   If you use Steam, add the following to enable features from SteamOS through your network.
   ```bash
   # Allow specified ports and services
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
   Finally, to confirm they have all been added (they are mostly to allow Steam services)
   ```bash
   # Show the status of UFW
   sudo ufw status verbose
   ```

## F. Configure Dotfiles

Customize tools and shells with dotfiles.

### Why?
- Dotfiles streamline gaming tools (e.g., MangoHud) and shells (e.g., Fish).
- Symlinking keeps configurations synced.

### Steps
1. **Clone dotfiles**:
   ```bash
   mkdir -p ~/Documents/GitRepos
   cd ~/Documents/GitRepos
   git clone https://github.com/linuxury/dotfiles.git
   cd dotfiles
   ```
   > **Warning**: Verify repository (`curl -I https://github.com/linuxury/dotfiles`).

2. **Create symlinks**:
   - Back up existing configurations:
   ```bash
   mv ~/.config/MangoHud ~/.config/MangoHud.bak 2>/dev/null
   mv ~/.config/Kvantum ~/.config/Kvantum.bak 2>/dev/null
   mv ~/.config/fish ~/.config/fish.bak 2>/dev/null
   mv ~/.config/fastfetch ~/.config/fastfetch.bak 2>/dev/null
   mv ~/.config/ghostty ~/.config/ghostty.bak 2>/dev/null
   mv ~/.config/bazaar-config ~/.config/bazaar-config.bak 2>/dev/null
   mv ~/.config/helix ~/.config/helix.bak 2>/dev/null
   mv ~/.config/starship ~/.config/starship.bak 2>/dev/null
   ```
   - Create symlinks:
   ```bash
   ln -s ~/Documents/GitRepos/dotfiles/MangoHud ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/Kvantum ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/fish ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/fastfetch ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/ghostty ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/bazaar-config ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/helix ~/.config
   ln -s ~/Documents/GitRepos/dotfiles/starship ~/.config
   ```
   Verify:
   ```bash
   ls -l ~/.config | grep dotfiles
   ```

## G. Optimize Steam for Gaming

Configure Steam with `proton-ge-custom` for optimal performance.

### Why?
- `proton-ge-custom` (installed in Section D) enables Windows games for Steam and Lutris.
- Custom launch options maximize performance and features.

### Steps
1. **Set `proton-ge-custom`**:
   - Open Steam, go to `Settings > Steam Play`, enable Steam Play for all titles, select `Proton-GE-Custom` (installed via `protonplus` in Section D).
   - For Lutris, set `proton-ge-custom` as the runner for games (configured in Lutris GUI).

2. **Set Steam launch options**:
   - Right-click a game in Steam, select `Properties`, add to `Launch Options`:
   ```plaintext
   PROTON_ENABLE_WAYLAND=1 PROTON_ENABLE_HDR=1 SteamDeck=1 PROTON_FSR4_UPGRADE=1 PROTON_DLSS_UPGRADE=1 gamemoderun mangohud %command%
   ```
   - **Explanation**:
     - `PROTON_ENABLE_WAYLAND=1`: Enables Wayland for scaling/HDR.
     - `PROTON_ENABLE_HDR=1`: Enables HDR (monitor must support it).
     - `SteamDeck=1`: Optimizes for Steam Deck-like behavior.
     - `PROTON_FSR4_UPGRADE=1`: Experimental FSR 4.0 upscaling.
     - `PROTON_DLSS_UPGRADE=1`: Experimental DLSS (NVIDIA only).
     - `gamemoderun`: Optimizes CPU/GPU.
     - `mangohud`: Shows FPS/GPU usage.
    
   - **Alternative for beginners**: Use simpler options to avoid issues with experimental flags:
   ```plaintext
   PROTON_ENABLE_WAYLAND=1 gamemoderun mangohud %command%
   ```
   Verify Wayland:
   ```bash
   echo $XDG_SESSION_TYPE  # Should output "wayland"
   ```
   > **Warning**: Experimental flags (`PROTON_FSR4_UPGRADE`, `PROTON_DLSS_UPGRADE`) may cause instability. Test games without them if issues arise.

3. **Verify ProtonPlus**:
   - Ensure `proton-ge-custom` is installed:
   ```bash
   ls ~/.local/share/Steam/compatibilitytools.d  # Should list Proton-GE-Custom
   ```

4. **Configure Lutris**:
   - Open Lutris, set `proton-ge-custom` as the runner for games in the game settings.

## H. Optional Customizations

Enhance KDE Plasma with tweaks.

### Why?
- Improves usability (e.g., Numlock, Fish) and aesthetics (e.g., global menu, SDDM theme).

### Steps
1. **Configure SDDM (login screen)**:
   - **Minimal setup**: Enable Num Lock and set Breeze theme for a clean login screen.
   ```bash
   sudo mkdir -p /etc/sddm.conf.d
   sudo nano /etc/sddm.conf.d/kde_settings.conf
   ```
   Add:
   ```plaintext
   [General]
   Numlock=on

   [Theme]
   Current=breeze
   ```
   - **Custom setup (recommended for your setup)**: Include Num Lock, power commands, and Breeze theme.
   ```bash
   sudo nano /etc/sddm.conf.d/kde_settings.conf
   ```
   Add:
   ```plaintext
   [General]
   Numlock=on
   HaltCommand=/usr/lib/systemd/systemd-shutdown poweroff
   RebootCommand=/usr/lib/systemd/systemd-shutdown reboot

   [Theme]
   Current=breeze
   ```
   Save (`Ctrl+O`, `Enter`, `Ctrl+X`).
   > **Note**: `Numlock=on` enables the numpad at login. `HaltCommand` and `RebootCommand` customize shutdown/reboot behavior for specific hardware needs. `Current=breeze` sets the Breeze theme for a polished, KDE-native login screen matching your Plasma desktop.

   Verify:
   ```bash
   cat /etc/sddm.conf.d/kde_settings.conf  # Check configuration
   ls /usr/share/sddm/themes/breeze  # Confirm Breeze theme exists
   ```
   > **Tip**: Reboot to test the login screen. If Breeze theme doesn’t load, ensure `sddm` and `plasma-desktop` are installed (`sudo pacman -S sddm plasma-desktop`).

2. **Set Fish as default shell**:
   ```bash
   which fish  # Verify Fish
   echo /usr/bin/fish | sudo tee -a /etc/shells
   chsh -s /usr/bin/fish
   ```
   Log out and back in.

3. **Enable global menu**:
   ```bash
   sudo pacman -S appmenu-gtk-module libdbusmenu-glib
   kquitapp5 plasmashell && kstart5 plasmashell
   ```
   > **Note**: Works best with Qt apps, may not function in Wayland or with all GTK apps.

4. **Customize GRUB for fast, silent boot**:
   ```bash
   sudo nano /etc/default/grub
   ```
   Modify:
   ```plaintext
   GRUB_TIMEOUT=0
   GRUB_TIMEOUT_STYLE=hidden
   GRUB_HIDDEN_TIMEOUT=0
   GRUB_HIDDEN_TIMEOUT_QUIET=true
   GRUB_CMDLINE_LINUX_DEFAULT="loglevel=0 quiet splash"
   ```
   - **Explanation**:
     - `GRUB_TIMEOUT=0`: Skips GRUB menu for fastest boot.
     - `GRUB_TIMEOUT_STYLE=hidden`: Hides menu.
     - `GRUB_HIDDEN_TIMEOUT=0`: No delay for hidden menu.
     - `GRUB_HIDDEN_TIMEOUT_QUIET=true`: Suppresses countdown.
     - `loglevel=0`: Suppresses all kernel messages.
   
   Update GRUB:
   ```bash
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   ```
   > **Warning**: `GRUB_TIMEOUT=0` and `loglevel=0` make boot fast and silent but may hide errors and make GRUB menu access difficult (use `Shift` or `Esc` during boot).
   
   After all changes, it should look something similar to this:
   <img width="1116" height="674" alt="image" src="https://github.com/user-attachments/assets/bf3ecc61-4578-4576-8bfc-9e5702b3114d" />


5. **Arch Update Counter Popup Color Layan [optional]**

   Name: White (#D8DEE9) <br/>
   Source: Teal (#88C0D0) <br/>
   From Version: Gray (#4C566A) <br/>
   Separator: Green (#A3BE8C) <br/>
   To Version: Bright white (#ECEFF4) <br/> <br/>
   <img width="510" height="398" alt="image" src="https://github.com/user-attachments/assets/acb87e0c-409c-4aca-aeee-9d4e545f4dc5" />


## I. Troubleshooting Tips
- **Package fails**: Check dependencies (`sudo pacman -S <package>`) or PKGBUILD (`paru -Si <package>`).
- **Service fails**: Check status (`systemctl status <service>`), logs (`journalctl -xe`).
- **Gaming issues**: Verify drivers (`glxinfo | grep "OpenGL renderer"`), Proton settings.
- **AUR errors**: Update (`paru -Syyu`), check AUR page.
- **Chaotic-AUR issues**: Remove from `pacman.conf`, update (`sudo pacman -Syyu`).
- **SDDM issues**: If Breeze theme doesn’t load, reinstall `sddm` and `plasma-desktop` (`sudo pacman -S sddm plasma-desktop`).
- **UFW issues**: If Steam features (e.g., Remote Play) fail, verify ports (`sudo ufw status`) and test incrementally.

## Conclusion
This guide sets up a gaming-optimized Arch Linux system with KDE Plasma, using `proton-ge-custom`, a polished Breeze login screen, and custom configurations. Commands are grouped for easy copying, with verification steps. For help, consult the [Arch Wiki](https://wiki.archlinux.org/) or forums.
