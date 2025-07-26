# Setting up Arch Linux during a clean install.

### A. What to do before running the Arch-install script.

> Let's assume you have your ISO downloaded and loaded on your PC/Laptop, ready for installation.

Let's start by making some changes to `pacman.conf`, you will need to use a text editor, you could use `nano` or `vim`. For this guide, we will use `nano`. Add the following command to start:

```javascript
sudo nano /etc/pacman.conf
```

> We are only looking for 2 changes on this file. Find **# Misc options** in that area we are `uncommenting` `Color` by removing `#` and look a few lines below for `ParallelDownloads` and change the number 5 to how many parallel downloads you are enabling. It's recommended to be 1to1 to your CPU cores for best performance

```javascript
# Misc options
#UseSyslog
Color
#NoProgressBar
CheckSpace
#VerbosePkgLists
ParallelDownloads = 24
DownloadUser = alpm
#DisableSandbox
```

> By performing these changes, you will enable terminal bash commands to have color on them and have a faster download because the parallel downloads are increased.

### B. Arch-install

You can just run the following command to start the script.

```javascript
archinstall
```

During this process, you will select your Mirror location, repositories, hostname, root password, add user and password, audio settings, network settings, additional packages, timezone, and install once everything is selected. (Go through all options so you don't miss anything and familiarize yourself with the process.)

> After selecting your local `Mirror`, select `Multilib` from repositories for 32-bit libraries.

> For Additional Packages you could search by typing " **/&#32;**".

### C. At first, boot into your desktop environment.

Now that you have completed the initial installation and are on your desktop environment of choice, there are a few things to attend to before you call it a day.


> Now let's run a benchmark and select the fastest mirrors for pacman to use.

Paste the following command into the terminal:

```javascript
sudo pacman -S reflector rsync
sudo reflector --latest 10 --sort rate --fastest 5 --save /etc/pacman.d/mirrorlist
```
> Install bash-completion for searches with "TAB"

```javascript
sudo pacman -S bash-completion
```

Once installed run

```javascript
nano ~/.bashrc
```

Add to the last line so all sudo users could use it by default

```javascript
Complete -cf sudo
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

CachyOS Repositories

Include the following command in the terminal

```javascript
curl https://mirror.cachyos.org/cachyos-repo.tar.xz -o cachyos-repo.tar.xz
tar xvf cachyos-repo.tar.xz && cd cachyos-repo
sudo ./cachyos-repo.sh
```

### D. Essential Applications

Now that the back-end is taken care of, we can concentrate on installing a few other applications that I use on all my installations and have become my default. Bluez and Bluez-utils for my Bluetooth, Fastfetch, Firefox, Fish, Ghostty, Gamemode, Kvantum, NetworkManager for my network, Mangohud, OpenRGB, Onlyoffice-bin, Pamac-AUR, Pacman-contrib, Starship, Thunderbird, Steam, and UFW.

> I have them in one command, but you get the idea and could just run them individually or not at all, your choice.

```javascript
paru -Syyu bluez bluez-utils fastfetch fish ghostty gamemode gnome-disk-utility kvantum kwalletmanager kconnect networkmanager nerd-fonts mesa-git mangohud openrgb onlyoffice-bin pamac-aur pacman-contrib power-profiles-daemon protonplus starship thunderbird timeshift steam ufw --needed
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
sudo ufw enable
```

Then run the following command

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

```javascript
mkdir -p ~/Documents/GitRepos
cd ~/Documents/GitRepos
```

Clone the repository using the provided URL:

```javascript
git clone https://github.com/linuxury/dotfiles.git
```

Or, if using SSH:

```javascript
git clone git@github.com:linuxury/dotfiles.git
```

**Setting Up Symlinks**:

- After cloning, they should navigate to the cloned `dotfiles` directory:

```javascript
cd dotfiles
```

- They can then create symlinks for the dotfiles as described earlier. Here’s a quick summary of the commands they would need to run:

```javascript
# Create symlinks for each application
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

### Additional Steps

- **Proton Experimental**: Go inside `Steam` and look for it on installed applications, then go to options and select the beta option, and select `bleeding-edge` 
- **ProtonPlus**: After running at least once `Steam` and `Lutris`, you will be able to download proton files for them. For `Steam`, you need `Proton-CachyOS`, and for `Lutris`, you need `Wine-Staging-Tkg` (at least until `Lutris` gets updated and the current error gets fixed).

### K. GRUB [optional]

By default, `GRUB` shows you a couple of useful things with a timer added to it, but I don't like that. I prefer a faster boot with minimal information shown on the screen. Let's open the `GRUB` config files located in `/etc/default/grub` with a text editor.

```javascript
sudo nano /etc/default/grub
```
or
```javascript
sudo nvim /etc/default/grub
```

Look for the following: 
- `GRUB_TIMEOUT=5` and change it to `0`
- `GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet"` and change it to `"loglevel=0 quiet splash"`
- `GRUB_TIMEOUT_STYLE=menu` and change it to `hidden`

Add this below the `GRUB_TIMEOUT_STYLE` line
- `GRUB_HIDDEN_TIMEOUT=0`
- `GRUB_HIDDEN_TIMEOUT_QUIET=true`

After all changes, it should look something similar to this:
<img width="1116" height="674" alt="image" src="https://github.com/user-attachments/assets/bf3ecc61-4578-4576-8bfc-9e5702b3114d" />

### L. SDDM Numlock enabled by default [optional]

By default, `SDDM` will have this feature off, so let's change it

```javascript
sudo nano /etc/sddm.conf.d/kde_settings.conf
```
or
```javascript
sudo nvim /etc/sddm.conf.d/kde_settings.conf
```

Now, all you need to do is look for `[General]` and add a new line to that group with

`Numlock=on`

After the changes, it should look something similar to this:
<img width="1118" height="670" alt="image" src="https://github.com/user-attachments/assets/f58c0688-6301-46f2-ba5d-57c25ba3406d" />
