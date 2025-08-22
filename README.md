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
sudo pacman -S curl wget zip unzip nano vim net-tools dnsutils firefox --needed
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
paru -Syyu bluez bluez-utils fastfetch fish ghostty gamemode gnome-disk-utility kvantum kwalletmanager kconnect kdeconnect legacy-launcher networkmanager nerd-fonts mangohud openrgb onlyoffice-bin pamac-aur pacman-contrib power-profiles-daemon proton-ge-custom-bin protonplus reflector rsync starship thunderbird timeshift steam ufw kdotool glfw-wayland-minecraft-cursorfix loupe topgrade paru yay plasma6-themes-layan-git tela-icon-theme-git breezex-cursor-theme plasma6-wallpapers-wallpaper-engine-git plasma6-applets-arch-update-notifier heroic-games-launcher-bin firefox jre21-openjdk jre22-openjdk jre23-openjdk flatpak sgdboop-bin lsfg-vk-git lutris-git vvave showtime --needed
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

