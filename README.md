# Setting up Arch Linux with KDE for Gaming

This guide configures a fresh Arch Linux installation with KDE Plasma, optimized for gaming. It covers pre-installation setup, the `archinstall` script, post-installation configuration, gaming tools, and customizations. Commands are grouped for easy copying, with detailed explanations for beginners. Run `sudo pacman -Syu` before starting, and consider testing in a virtual machine (e.g., VirtualBox or QEMU).

> **Note**: Commands assume a terminal. If a command fails, consult the [Arch Wiki](https://wiki.archlinux.org/) or Arch Linux forums.

## A. Pre-Installation Configuration

Configure the package manager `pacman` for performance and usability.

### Why?
- Colors and verbose lists improve readability.
- Parallel downloads speed up installation, matching CPU core count.
- Fast mirrors ensure quick downloads.

### Steps
1. **Edit `pacman.conf`**:
   - Enable `Color`, `VerbosePkgLists`, and `ILoveCandy`. Set `ParallelDownloads` to your CPU cores.<br /><br />
   
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
   Save `Ctrl+O`, `Enter`, `Ctrl+X`.

2. **Optimize mirrors**:
   - Use `reflector` to select fast mirrors for your region.<br /><br />
  
   ```bash
   sudo pacman -S reflector
   sudo reflector --country 'United States' --latest 20 --sort rate --save /etc/pacman.d/mirrorlist
   ```
   > **Note**: Replace `--country 'United States'` with your country e.g., `--country 'Germany,France'` or omit for all mirrors.

3. **Verify**:
   ```bash
   cat /etc/pacman.d/mirrorlist
   sudo pacman -Syy
   ```

## B. Run the Archinstall Script

The `archinstall` script simplifies the installation process, setting up KDE Plasma for gaming.

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
- **Action**: Select your region e.g., `United States` or `Global`.
- **Tip**: Keep the default if `reflector` was used.

#### 2. Disk Configuration
- **Purpose**: Partitions the drive.
- **Action**: Choose `Auto-partition` (EFI, root, 4–8 GB swap, `ext4` or `btrfs` filesystem).
- **Why for Gaming?**: Swap handles memory-heavy games; `ext4` is stable and `btrfs` is more reliable in terms of backup friendliness.
- **Tip**: Back up data; check SSD `lsblk -d -o NAME,ROTA`, ROTA=0.

#### 3. Profile
- **Purpose**: Selects desktop environment.
- **Action**: Choose `desktop` > `plasma`.
- **Why for Gaming?**: KDE Plasma supports Wayland and HDR.
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

#### 7. Timezone
- **Purpose**: Sets system clock.
- **Action**: Choose region/city e.g., `America/New_York`.
- **Tip**: Use `Tab` to search.

#### 8. Hostname
- **Purpose**: Names computer.
- **Action**: Enter name e.g., `gaming-pc`.
- **Tip**: Use lowercase, no special characters.

#### 9. Root Password
- **Purpose**: Sets admin password.
- **Action**: Enter secure password.

#### 10. User Account
- **Purpose**: Creates daily-use account.
- **Action**: Add user, e.g., `gamer`, set password, enable `sudo`.
- **Tip**: Choose simple username.

#### 11. Additional Packages
- **Purpose**: Installs extra packages.
- **Action**: Enter `firefox vulkan-tools`.
- **Why for Gaming?**: Sets up web access, graphics, and gaming.
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
- **Tip**: Verify UEFI `ls /sys/firmware/efi`.

#### 14. Install
- **Purpose**: Starts installation.
- **Action**: Review choices, select `Install`.
- **Tip**: Note errors, check logs `journalctl -b`.

#### 15. Post-Installation
- **Purpose**: Completes setup.
- **Action**: Select `Reboot`.
- **Tip**: Access GRUB `Shift` or `Esc` if boot fails.

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
   sudo pacman -S bash-completion --needed
   nano ~/.bashrc
   ```
   Add:
   ```bash
   # Enable bash completion
   [ -f /usr/share/bash-completion/bash_completion ] && . /usr/share/bash-completion/bash_completion
   complete -cf sudo
   ```
   Save `Ctrl+O`, `Enter`, `Ctrl+X`, refresh:
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
   sudo pacman -S git base-devel --needed
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
   > **Note**: If `makepkg` fails, check dependencies `sudo pacman -S <package>` or PKGBUILD `less PKGBUILD`.

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
   sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils --needed
   ```
   For AMD:
   ```bash
   sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon --needed
   ```
   For Intel:
   ```bash
   sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel --needed
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
   > **Warning**: Chaotic-AUR is third-party. Review PKGBUILDs `paru -Si <package>` for safety.

3. **Install core packages**:

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
           lib32-openal webapp-manager chromium pdfarranger --needed
   ```
   > **Note**: The backslash `\` splits long commands for readability without affecting execution. Multiple Java versions may cause conflicts; consider only `jre-openjdk` (In my case I need multiple versions for Minecraft. Review AUR PKGBUILDs.

4. **Enable Flatpak**:
   ```bash
   sudo pacman -S flatpak
   flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
   ```
   Verify:
   ```bash
   flatpak remote-list  # Should show flathub
   ```

5. **Install Bazaar**:
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
   > **Tip**: Use `bluez-tools` `sudo pacman -S bluez-tools` for pairing.

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
   > **Warning**: Verify repository `curl -I https://github.com/linuxury/dotfiles`.

2. **Create symlinks**:
   - Back up existing configurations [optional]:<br /><br/>

   
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
   - Create symlinks:<br /><br/>


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
   - Open Steam, go to `Settings > Compatibility (Steam Play)`, enable Steam Play for all titles, select `Proton-GE`.
   - For Lutris, set `proton-ge-custom` as the runner for games (configured in Lutris GUI).

2. **Set Steam launch options**:
   - Right-click a game in Steam, select `Properties`, add to `Launch Options`:<br/><br/>

   ```plaintext
   PROTON_ENABLE_WAYLAND=1 PROTON_ENABLE_HDR=1 PROTON_FSR4_UPGRADE=1 gamemoderun mangohud %command%
   ```
   - **Explanation**:
     - `PROTON_ENABLE_WAYLAND=1`: Enables Wayland for scaling/HDR.
     - `PROTON_ENABLE_HDR=1`: Enables HDR (monitor must support it).
     - `SteamDeck=1`: Optimizes for Steam Deck-like behavior.
     - `PROTON_FSR4_UPGRADE=1`: Experimental FSR 4.0 upscaling.
     - `PROTON_DLSS_UPGRADE=1`: Experimental DLSS (NVIDIA only).
     - `gamemoderun`: Optimizes CPU/GPU.
     - `mangohud`: Shows FPS/GPU usage.<br/><br/>
   > **Note**: For wayland specific flags and options, please refer to: https://github.com/Etaash-mathamsetty/Proton/blob/em-10/docs/EM-ADDITIONS.md
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
   - Ensure `proton-ge-custom` is installed:<br/><br/>

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
   - **Minimal setup**: Enable Num Lock and set the Breeze theme for a clean login screen.<br/><br/>
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
   - **Custom setup (recommended for your setup)**: Include Num Lock, power commands, and Breeze theme.<br/><br/>
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
   > **Tip**: Reboot to test the login screen. If the Breeze theme doesn’t load, ensure `sddm` and `plasma-desktop` are installed `sudo pacman -S sddm plasma-desktop`.

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
   
   After all changes, it should look something similar to this:<br/><br/>
   <img width="1116" height="674" alt="image" src="https://github.com/user-attachments/assets/bf3ecc61-4578-4576-8bfc-9e5702b3114d" />


5. **Arch Update Counter configuration**:
    - General:
        - **Change the option for update to**: `5 minute(s)` <br/>
        - **Select the option for**: `Retry if error` <br/> <br/>
          <img width="497" height="152" alt="image" src="https://github.com/user-attachments/assets/fd6b9773-4950-4ad4-9da1-d21b28ac6b79" />

    - Search & Count:
        - **Count ARCH command**: `checkupdates | wc -l` <br/>
        - **Count AUR command**: `paru -Qua | wc -l` <br/>
        - **List ARCH command**: `checkupdates` <br/>
        - **List AUR command**:  `paru -Qua` <br/> <br/>
          <img width="559" height="374" alt="image" src="https://github.com/user-attachments/assets/879a49ec-2ca4-4d17-a4d3-ca5dbaa6d1f8" />

    - Update Package:
        - **Update command**: `topgrade` <br/>
        - **Update one command**: `paru -Sy` <br/>
        - **Terminal cmd for update action**: `ghostty -e` <br/>
        - **Terminal cmd for update with do not close**: `ghostty --noclose -e` <br/> <br/>
          <img width="559" height="466" alt="image" src="https://github.com/user-attachments/assets/a681a468-0fe1-4017-80f3-99a1764546dc" />

    - Change Popup color:<br/>
        - **Name**: White `#D8DEE9` <br/>
        - **Source**: Teal `#88C0D0` <br/>
        - **From Version**: Gray `#4C566A` <br/>
        - **Separator**: Green `#A3BE8C` <br/>
        - **To Version**: Bright white `#ECEFF4` <br/> <br/>
          <img width="510" height="398" alt="image" src="https://github.com/user-attachments/assets/acb87e0c-409c-4aca-aeee-9d4e545f4dc5" />


## I. Troubleshooting Tips
- **Package fails**: Check dependencies `sudo pacman -S <package>` or PKGBUILD `paru -Si <package>`.
- **Service fails**: Check status `systemctl status <service>`, logs `journalctl -xe`.
- **Gaming issues**: Verify drivers `glxinfo | grep "OpenGL renderer"`, Proton settings.
- **AUR errors**: Update `paru -Syyu`, check AUR page.
- **Chaotic-AUR issues**: Remove from `pacman.conf`, update `sudo pacman -Syyu`.
- **SDDM issues**: If the Breeze theme doesn’t load, reinstall `sddm` and `plasma-desktop`, `sudo pacman -S sddm plasma-desktop`.
- **UFW issues**: If Steam features (e.g., Remote Play) fail, verify ports `sudo ufw status` and test incrementally.

## Conclusion
This guide sets up a gaming-optimized Arch Linux system with KDE Plasma, using `proton-ge-custom`, a polished Breeze login screen, and custom configurations. Commands are grouped for easy copying, with verification steps. For help, consult the [Arch Wiki](https://wiki.archlinux.org/) or forums.
