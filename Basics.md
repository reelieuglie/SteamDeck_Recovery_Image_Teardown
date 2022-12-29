## Setting up environment
Donwload: https://store.steampowered.com/steamos/download?ver=steamdeck

Unzip: 
```
# bzip2 -d ./steamdeck-recovery-4.img.bz2
```
Check file information:
```
# fdisk -l ./steamdeck-recovery-4.img
Disk ./steamdeck-recovery-4.img: 7.21 GiB, 7744000000 bytes, 15125000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 8E303AAA-A60D-41D4-9864-163AC2363E76

Device                         Start      End  Sectors  Size Type
./steamdeck-recovery-4.img1       34   131071   131038   64M EFI System
./steamdeck-recovery-4.img2   131072   393215   262144  128M Microsoft basic data
./steamdeck-recovery-4.img3   655360 11141119 10485760    5G Linux root (x86-64)
./steamdeck-recovery-4.img4 11141120 11665407   524288  256M Linux variable data
./steamdeck-recovery-4.img5 11665408 15124966  3459559  1.6G Linux home
```
Mount as loop device. Offset is start sector * sector size, sizelimit is number of sectors * sector size.

```
 mount -o loop,offset=$((655360 * 512)),sizelimit=$((10485760 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/home/ 
 mount -o loop,offset=$((11141120 * 512)),sizelimit=$((524288 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/var/ 
 mount -o loop,offset=5972688896,sizelimit=$((3459559 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/home/
```
## Looking at Boot information
```
# ls ./boot/
amd-ucode.img  efi  grub  initramfs-linux-neptune.img  vmlinuz-linux-neptune
```

```
ip-172-31-4-246:/mnt3 # file ./boot/vmlinuz-linux-neptune
./boot/vmlinuz-linux-neptune: Linux/x86 Kernel, Setup Version 0x20f, bzImage, Version 5.13.0-valve10.3-1-neptune-02176-g5fe416c4acd8 (linux-neptune@archlinux) #1 SMP PREEMPT Wed, 23, RO-rootFS, swap_dev 0x8, Normal VGA
```

```
# file ./boot/initramfs-linux-neptune.img
./boot/initramfs-linux-neptune.img: ASCII cpio archive (SVR4 with no CRC)
```

```
ip-172-31-4-246:/mnt3/etc # ls
.pwd.lock       avahi                   crypttab       ethertypes  grub.d            iptables       libnl        makepkg.conf    netconfig      ostree-mkinitcpio.conf  profile           rsyncd.conf    skel               sysctl.d           xattr.conf
ModemManager    bash.bash_logout        daxctl.conf.d  fish        gshadow           issue          libva.conf   man_db.conf     nginx          pacman.conf             profile.d         sddm.conf.d    slsh.rc            systemd            xbindkeysrc
NetworkManager  bash.bashrc             dconf          flatpak     gshadow-          kernel         locale.conf  mdadm.conf      nscd.conf      pacman.d                protocols         securetty      smartd.conf        tmpfiles.d         xdg
OpenCL          bindresvport.blacklist  default        fonts       gssapi_mech.conf  keyutils       locale.gen   mime.types      nsswitch.conf  pam.d                   pulse             security       ssh                trusted-key.key    xinetd.d
UPower          binfmt.d                depmod.d       fstab       gtk-3.0           krb5.conf      localtime    mke2fs.conf     ntp.conf       passwd                  rauc              sensors.d      ssl                ts.conf
X11             bluetooth               desync         fuse.conf   healthd.conf      ld.so.cache    login.defs   modprobe.d      nvme           passwd-                 rc_keymaps        sensors3.conf  steamos-atomupd    udev
alsa            ca-certificates         dkms           gai.conf    host.conf         ld.so.conf     logrotate.d  modules-load.d  openldap       pinentry                rc_maps.cfg       services       steamos-release    udisks2
anthy-conf      chromium                dracut.conf    gdb         hostname          ld.so.conf.d   lsb-release  mtab            openvpn        pkcs11                  request-key.conf  shadow         sudo.conf          vdpau_wrapper.cfg
appstream.conf  cifs-utils              dracut.conf.d  geoclue     hosts             libaudit.conf  lvm          nanorc          opt            plymouth                request-key.d     shadow-        sudo_logsrvd.conf  vimrc
arch-release    conf.d                  e2scrub.conf   group       inputrc           libblockdev    machine-id   ndctl           os-release     polkit-1                resolv.conf       shells         sudoers            vpower.toml
audit           cpufreq-bench.conf      environment    group-      iproute2          libinput       mailcap      ndctl.conf.d    ostree         ppp                     rpc               signond.conf   sudoers.d          wpa_supplicant
```

