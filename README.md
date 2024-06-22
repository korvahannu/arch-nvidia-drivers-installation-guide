# Arch Linux NVIDIA drivers installation guide

![Arch Linux Logo](https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png)

## Important links

- [Arch Linux Homepage](https://archlinux.org/ "Arch Linux Homepage")
- [Arch Linux Wiki](https://wiki.archlinux.org/ "Arch Wiki")

## Default Prerequisites

This is a quick tutorial on how you can install proprietary NVIDIA drivers for Arch Linux. Please note if you are using anything other than the regular linux kernel, such as linux-lts, you need to make changes accordingly. All the commands marked `like this` are meant to be run on the terminal. **Do not reboot before you have finished all the steps below!**

## Step 1: Installing required packages and enabling multilib

1. Update the system:
   `sudo pacman -Syu`
2. Install required packages:
   `sudo pacman -S base-devel linux-headers git nano --needed`
3. Install the AUR helper, yay
   - `cd ~`
   - `git clone https://aur.archlinux.org/yay.git`
   - `cd yay`
   - `makepkg -si`
4. Enable multilib repository
   - `sudo nano /etc/pacman.conf`
   - Uncomment the following lines by removing the # -character at the start them
     - **[multilib]**
     - **Include = /etc/pacman.d/mirrorlist**
   - Save the file with _CTRL+S_ and close nano with _CTRL+X_
5. Run `yay -Syu`, to update the system package database

## Step 2: Installing the driver packages

1. First find your [NVIDIA card from this list here](https://nouveau.freedesktop.org/CodeNames.html). Alternatively you can take a look at the [Gentoo wiki](https://wiki.gentoo.org/wiki/NVIDIA#Feature_support).
2. Check what driver packages you need to install from the table below

| Driver name                                      | Kernel                      | Base driver       | OpenGL             | OpenGL (multilib)        |
| ------------------------------------------------ | --------------------------- | ----------------- | ------------------ | ------------------------ |
| Maxwell (NV110) series and newer                 | linux or linux-lts          | nvidia            | nvidia-utils       | lib32-nvidia-utils       |
| Maxwell (NV110) series and newer                 | not linux and not linux-lts | nvidia-dkms       | nvidia-utils       | lib32-nvidia-utils       |
| Kepler (NVE0) series                             | any                         | nvidia-470xx-dkms | nvidia-470xx-utils | lib32-nvidia-470xx-utils |
| GeForce 400/500/600 series cards [NVCx and NVDx] | any                         | nvidia-390xx-dkms | nvidia-390xx-utils | lib32-nvidia-390xx-utils |
| Tesla (NV50/G80-90-GT2XX)                        | any                         | nvidia-340xx-dkms | nvidia-340xx-utils | lib32-nvidia-340xx-utils |

3. Install the correct Base driver, OpenGL, and OpenGL (multilib) packages
   - Example: `yay -S nvidia-470xx-dkms nvidia-470xx-utils lib32-nvidia-470xx-utils`
4. Install nvidia-settings with `yay -S nvidia-settings`

## Step 3: Enabling DRM kernel mode setting

In this step please complete all the parts: _Setting the Kernel Parameter_, _Add Early Loading of NVIDIA Modules_, and _Adding the Pacman Hook_.

### Setting the Kernel Parameter:

Setting the kernel parameter depends on what bootloader you are using. Complete only one of the options below (A or B). After that, continue to _Add Early Loading of NVIDIA Modules_.

#### Option A) For GRUB users

1. Edit the GRUB configuration file:
   - `sudo nano /etc/default/grub`
   - Find the line with **GRUB_CMDLINE_LINUX_DEFAULT**
   - Append the words inside the quotes with **nvidia-drm.modeset=1**
     - Example: **GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1"**
   - Save the file with _CTRL+S_ and close nano with _CTRL+X_
2. Update the GRUB configuration: `sudo grub-mkconfig -o /boot/grub/grub.cfg`

#### Option B) For systemd-boot users

1. Navigate to the bootloader entries directory: `cd /boot/loader/entries/`
2. Edit the appropriate **.conf** file for your Arch Linux boot entry
   - `sudo nano <filename>.conf`
3. Append **nvidia-drm.modeset=1** to the **options** line
4. Save the file with _CTRL+S_ and close nano with _CTRL+X_

### Add Early Loading of NVIDIA Modules:

1. Edit the **mkinitcpio** configuration file:
   - `sudo nano /etc/mkinitcpio.conf`
   - Find the line that says **MODULES=()**
   - Update the line to: **MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)**
   - Find the line that says **HOOKS=()**
   - On the **HOOKS=()** line, find the word **kms** inside the parenthesis and remove it
   - Save the file with _CTRL+S_ and close nano with _CTRL+X_
2. Regenerate the initramfs with `sudo mkinitcpio -P`

### Adding the Pacman Hook:

1. Get the **nvidia.hook** -file from this repository
   - `cd ~`
   - `wget https://raw.githubusercontent.com/korvahannu/arch-nvidia-drivers-installation-guide/main/nvidia.hook`
2. Open the file with your preferred editor.
   - `nano nvidia.hook`
3. Find the line that says **Target=nvidia**.
4. Replace the word **nvidia** with the base driver you installed, e.g., **nvidia-470xx-dkms**
   - The edited line should look something like this: **Target=nvidia-470xx-dkms**
5. Save the file with _CTRL+S_ and close nano with _CTRL+X_
6. Move the file to **/etc/pacman.d/hooks/** with: `sudo mkdir -p /etc/pacman.d/hooks/ && sudo mv ./nvidia.hook /etc/pacman.d/hooks/`

## Step 4: Reboot and enjoy!

You can now safely reboot and enjoy the proprietary NVIDIA drivers. If you have any problems check the Arch Linux Wiki or the forums for common pitfalls and questions.
