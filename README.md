## Arch Linux nvidia drivers installation guide
![Arch Linux Logo](https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png)  

#### Important links
- [Arch Linux Homepage](https://archlinux.org/ "Arch Linux Homepage")
- [Arch Linux Wiki](https://wiki.archlinux.org/ "Arch Wiki")

#### Default Prerequisites
This is a quick tutorial on how you can install proprietary nvidia drivers for Arch Linux. Please note if you are using anything other than the regular linux kernel, such as linux-lts, you need to make changes accordingly. All the commands marked with ***like this*** are meant to be run on the terminal. **Do not reboot before you have finished all the steps below!**

## Step 1: Installing required packages and enable multilib
1. Update the system: ***sudo pacman -Syu***
2. Install required packages: ***sudo pacman -S base-devel linux-headers git***
3. Install yay, please replace the [YOUR USERNAME HERE] with your linux username without the '[' and ']':
- ***sudo git clone https://aur.archlinux.org/yay.git***
- ***sudo chown -R [YOUR USERNAME HERE]:users yay***
- ***cd yay***
- ***makepkg -si***
4. Enable multilib repository
- ***sudo nano /etc/pacman.conf***
- Uncomment lines that have *[multilib]* and *Include = /etc/pacman.d/mirrorlist*
- ***sudo pacman -Syy***

## Step 2: Installing the driver packages
1. This step might be a bit confusing. First find your [nvidia card from this list here](https://nouveau.freedesktop.org/CodeNames.html)
2. Check what driver packages you need to install from the list below

| Driver name  | Base driver | OpenGL | OpenGL (multilib) |
| ------------- | ------------- | ------------- |  ------------ | 
| Maxwell (NV110) series and newer  | nvidia | nvidia-utils | lib32-nvidia-utils |
| Kepler (NVE0) series  | nvidia-470xx-dkms  | nvidia-470xx-utils | lib32-nvidia-470xx-utils |
| GeForce 400/500/600 series cards [NVCx and NVDx] | nvidia-390xx  | nvidia-390xx-utils  | lib32-nvidia-390xx-utils |

3. Install the correct packages, for example ***yay -S nvidia-470xx-dkms nvidia-470xx-utils lib32-nvidia-470xx-utils***
4. I also recommend you to install nvidia-settings via ***yay -S nvidia-settings***

## Step 3: Enabling DRM kernel mode setting
1. Add the kernel parameter
- Go to your grub file with ***sudo nano /etc/default/grub***
- Find the *GRUB_CMDLINE_LINUX_DEFAULT* -line
- Append the line with *nvidia-drm.modeset=1*
- For example: *GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1"*
- Save the file with *CTRL+O*
- Finish the grub config with ***sudo grub-mkconfig -o /boot/grub/grub.cfg***
2. Add the early loading
- Go to your mkinitcpio configuration file with ***sudo nano /etc/mkinitcpio.conf***
- Find the line with *MODULES=()*
- Edit the line to match *MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)*
- Save the file with *CTRL+O*
- Finish the mkinitcpio configuration with ***sudo mkinitcpio -p***
3. Adding the pacman hook
- Find the *nvidia.hook* in this repository, make a local copy and open the file with your preferred editor
- Find the *Target=nvidia* line
- Replace the *nvidia* with the base driver you installed, e.g. *nvidia-470xx-dkms*
- Save the file and move it to */etc/pacman.d/hooks/* , for example with ***sudo mv ./nvidia.hook /etc/pacman.d/hooks/***

## Step 2: Reboot and enjoy!
You can now safely reboot and enjoy the proprietary nvidia drivers. If you have any problems check the Arch Linux Wiki or the forums for common pitfalls and questions.
