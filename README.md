[![Arch Linux](https://www.archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png)](https://archlinux.org)

# Arch Linux Installation with Xfce/i3 - Fast, Minimal, Powerful

> A step-by-step guide for installing Arch Linux with Xfce4 and i3 Window Manager, optimized to save time and avoid unnecessary debugging. 

---

## Getting Started

Welcome to the **Arch Linux with Xfce4 and i3 Window Manager Installation Guide**!

This guide is based on real-world experience installing Arch Linux on various systems over the years. The goal is to make your setup fast, minimal, and as bug-free as possible.

---

## Support and Feedback

**GitHub Contributions:**

- **Issues:** Open issues for any problem or clarification.
- **Pull Requests:** PRs welcome to improve the guide.

---

## Section 01: Installing Arch Linux on Your Hardware 

### Step 01: Download Arch Linux ISO

Visit: [https://archlinux.org/download](https://archlinux.org/download)

- Select a geographically close mirror.
- Download the `.iso` file only.

### Step 02: Create Installation USB

Insert a USB with at least 2GB.

```bash
sudo dd conv=fsync oflag=direct status=progress bs=4M \
    if=./archlinux-YYYY.MM.DD-x86_64.iso of=/dev/sdX
```

Replace `/dev/sdX` with your actual USB device (e.g., `/dev/sdb`).

### Step 03: Boot from USB

- Insert USB.
- Reboot and select boot menu (e.g., `F12` for ThinkPads).
- Disable **Secure Boot** if necessary.

### Step 04: Sync Packages

```bash
iwctl
[iwd]# station wlan0 get-networks
[iwd]# station wlan0 connect <SSID>
[iwd]# exit
ping 1.1.1.1
pacman -Syy
```

### Step 05: Partition Disk

Use `lsblk` to identify disk (e.g., `/dev/nvme0n1`)

```bash
fdisk /dev/nvme0n1
```

Create:
- EFI: +256M
- Main: -32G (for swap)
- Swap: remaining

Change types:

```
1: uefi
2: linux
3: swap
```

Format:

```bash
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2
mkswap /dev/nvme0n1p3
```

Mount:

```bash
mount /dev/nvme0n1p2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/nvme0n1p1 /mnt/boot/efi
swapon /dev/nvme0n1p3
```

Install base system:

```bash
pacstrap -i /mnt base linux linux-firmware sudo vim
```

Generate fstab:

```bash
genfstab -U -p /mnt > /mnt/etc/fstab
```

### Step 06: System Configuration

```bash
arch-chroot /mnt
vim /etc/locale.gen
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
hwclock --systohc
```

Set hostname:

```bash
echo yourhostname > /etc/hostname
vim /etc/hosts
```

Add user:

```bash
useradd -m -G wheel -s /bin/bash yourusername
passwd root
passwd yourusername
visudo  # uncomment %wheel ALL=(ALL:ALL) ALL
```

Install and configure GRUB:

```bash
pacman -S grub efibootmgr
grub-install /dev/nvme0n1
grub-mkconfig -o /boot/grub/grub.cfg
```

Enable networking:

```bash
pacman -S dhcpcd networkmanager resolvconf
systemctl enable dhcpcd
systemctl enable NetworkManager
systemctl enable systemd-resolved
```

Exit and reboot:

```bash
exit
umount -R /mnt
reboot
```

---

## Section 02: Post-Installation Configuration 

### Step 01: Sync Time

```bash
timedatectl set-ntp true
```

### Install X, Essentials, and Network Tools

```bash
sudo pacman -S xorg xorg-apps xorg-xinit
sudo pacman -S base-devel git zip unzip htop tree dialog reflector bash-completion
sudo pacman -S intel-ucode fuse2 inxi powertop lshw acpi
sudo pacman -S networkmanager-openvpn nm-connection-editor
```

### Step 02: Install Desktop Environment

#### XFCE:

```bash
sudo pacman -S xfce4 xfce4-goodies thunar-archive-plugin network-manager-applet
```

#### i3:

```bash
sudo pacman -S i3-wm i3status i3lock pango polybar rofi alacritty dunst feh xss-lock flameshot gsimplecal yazi ueberzugpp
```

### Step 03: Install Login Manager

```bash
sudo pacman -S ly
sudo systemctl enable ly
```

### Step 04: Fonts and Themes

```bash
sudo pacman -S ttf-dejavu ttf-freefont noto-fonts noto-fonts-emoji ttf-ubuntu-font-family ttf-ibm-plex
sudo pacman -S arc-gtk-theme papirus-icon-theme
```

### Step 05: Sound, Bluetooth, Printing, Battery

```bash
sudo pacman -S pulseaudio pavucontrol alsa-utils sof-firmware
sudo pacman -S bluez bluez-utils blueman && sudo systemctl enable bluetooth
sudo pacman -S cups system-config-printer && sudo systemctl enable cups.service
sudo pacman -S tlp tlp-rdw && sudo systemctl enable tlp
```

### Step 06: fstrim, Pacman Mirrors

```bash
sudo systemctl enable fstrim.timer
sudo reflector --country YOUR_COUNTRY --fastest 10 --save /etc/pacman.d/mirrorlist
```

---

## Section 03: Productivity Apps and Dev Tools 

*Trimmed for brevity. Recommended apps include:*
- Brave, Firefox
- VSCode, neovim
- Zsh + Oh-My-Zsh
- yay (AUR helper)
- Node.js, Python, Docker, etc.

---

## Section 04: Tips, Fixes, and FAQs 

*Trimmed for brevity. Covers:*
- Fixing no audio
- GRUB boot issues
- Screen tearing
- Network troubleshooting

---
## Join the Community

For discussions, support, and memes, check out the [Arch Linux Reddit Community](https://www.reddit.com/r/archlinux)

---

**For the love of Linux and freedom.**
