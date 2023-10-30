# Arch Linux nvidia drivers installation guide

![Arch Linux Logo](https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png)

## Important links

- [Arch Linux Homepage](https://archlinux.org/ "Arch Linux Homepage")
- [Arch Linux Wiki](https://wiki.archlinux.org/ "Arch Wiki")

## Default Prerequisites

This is a quick tutorial on how you can install proprietary nvidia drivers for Arch Linux. Please note if you are using anything other than the regular linux kernel, such as linux-lts, you need to make changes accordingly. All the commands marked with `like this` are meant to be run on the terminal. **Do not reboot before you have finished all the steps below!**

## Step 1: Installing required packages and enable multilib

1. Update the system:
   `sudo pacman -Syu`
2. Install required packages:
   `sudo pacman -S base-devel linux-headers git --needed`
3. Install the AUR helper, yay

```
 cd ~
 git clone https://aur.archlinux.org/yay.git
 cd yay
 makepkg -si
```

4. Enable multilib repository
   `sudo nano /etc/pacman.conf`

- Uncomment lines that have `[multilib]` and `Include = /etc/pacman.d/mirrorlist`, and then run `yay -Syu`, to update the system package database. Do not run `yay -Syy`, as that may cause a partial upgrade.

## Step 2: Installing the driver packages

1. This step might be a bit confusing. First find your [nvidia card from this list here](https://nouveau.freedesktop.org/CodeNames.html)
2. After reading if you still don't know what driver you need, take a look of that list on gentoo wiki [here](https://wiki.gentoo.org/wiki/NVIDIA#Feature_support) that lists the latest driver that supports your CHIPSET. For example, if you have a GTX 3060, you need to install the latest version of nvidia, so just `yay -S nvidia nvidia-utils lib32-nvidia-utils`.
3. Check what driver packages you need to install from the list below

| Driver name                                      | Base driver       | OpenGL             | OpenGL (multilib)        |
| ------------------------------------------------ | ----------------- | ------------------ | ------------------------ |
| Maxwell (NV110) series and newer                 | nvidia            | nvidia-utils       | lib32-nvidia-utils       |
| Kepler (NVE0) series                             | nvidia-470xx-dkms | nvidia-470xx-utils | lib32-nvidia-470xx-utils |
| GeForce 400/500/600 series cards [NVCx and NVDx] | nvidia-390xx      | nvidia-390xx-utils | lib32-nvidia-390xx-utils |

3. Install the correct packages, for example `yay -S nvidia-470xx-dkms nvidia-470xx-utils lib32-nvidia-470xx-utils`
4. I also recommend you to install nvidia-settings via `yay -S nvidia-settings`

That's a great initiative! I appreciate your efforts to contribute back to the community. Here's an updated section of your guide that includes information on setting kernel parameters for both GRUB and `systemd-boot`:

---

## Step 3: Enabling DRM kernel mode setting

### Setting the Kernel Parameter:

#### For GRUB users:

1. Edit the GRUB configuration file:
   - `sudo nano /etc/default/grub`
   - Find `GRUB_CMDLINE_LINUX_DEFAULT`.
   - Append the line with `nvidia-drm.modeset=1`.
   - Example: `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1"`.
   - Save the file with _CTRL+O_.
   - Update the GRUB configuration: `sudo grub-mkconfig -o /boot/grub/grub.cfg`.

#### For `systemd-boot` users:

1. Navigate to the bootloader entries directory: `/boot/loader/entries/`.
2. Edit the appropriate `.conf` file for your Arch Linux boot entry.
3. Append `nvidia-drm.modeset=1` to the `options` line.
4. Save and exit.

### Add Early Loading of NVIDIA Modules:

1. Edit the `mkinitcpio` configuration file:
   - `sudo nano /etc/mkinitcpio.conf`
   - Find `MODULES=()`.
   - Update the line to: `MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)`.
   - Save the file with _CTRL+O_.
   - Regenerate the initramfs: `sudo mkinitcpio -P`.

### Adding the Pacman Hook:

1. Locate the `nvidia.hook` in this repository and make a local copy.
2. Open the file with your preferred editor.
3. Find `Target=nvidia`.
4. Replace `nvidia` with the base driver you installed, e.g., `nvidia-470xx-dkms`.
5. Save the file.
6. Move it to `/etc/pacman.d/hooks/`:
   - `sudo mv ./nvidia.hook /etc/pacman.d/hooks/`.

## Step 4: Reboot and enjoy!

You can now safely reboot and enjoy the proprietary nvidia drivers. If you have any problems check the Arch Linux Wiki or the forums for common pitfalls and questions.
