## Putting SteamDeck OS on EC2
See [Basics](https://github.com/reelieuglie/SteamDeck_Recovery_Image_Teardown/blob/main/Basics.md) to create environment for chroot

[Install Arch Packages on Steam Deck - Reddit](https://www.reddit.com/r/SteamDeck/comments/t8al0i/install_arch_packages_on_your_steam_deck/)
[Steam Deck Repository](https://steamdeck-packages.steamos.cloud/archlinux-mirror/)

```
# pacman -S cloud-init
error: failed to initialize alpm library:
(root: /, dbpath: /var/lib/pacman/)
could not find or read directory
# btrfs property set -ts / ro false
# mkdir /var/lib/pacman
#  cat << EOF >> /etc/resolv.conf
nameserver 169.254.169.253
EOF

```
* Match the Repo names here *
```

[jupiter-main]
Include = /etc/pacman.d/mirrorlist
SigLevel = Never

[holo-main]
Include = /etc/pacman.d/mirrorlist
SigLevel = Never

[core-main]
Include = /etc/pacman.d/mirrorlist

[extra-main]
Include = /etc/pacman.d/mirrorlist

#[community-testing]
#Include = /etc/pacman.d/mirrorlist

[community-main]
Include = /etc/pacman.d/mirrorlist

[multilib-main]
Include = /etc/pacman.d/mirrorlist
```
This kind of works for the above:
```
for i in jupiter holo  core  extra community multilib; do sed -i.bak "s/$i/$i-main/g" /etc/pacman.conf; done
```
But you end up with:
```
#[community-main-testing]
#Include = /etc/pacman.d/mirrorlist
```
It's commented, so I am chosing to ignore it. 

[refresh Keys](https://wiki.archlinux.org/title/Pacman/Package_signing#Resetting_all_the_keys)


* Add needed Keys
* [Importing Keys](https://keys.openpgp.org/about/usage)
* SteamDeck uses a different path for pacman.db and cache as well, so we'll alias it for easier life. 

```
# alias pacman='pacman --dbpath /usr/share/factory/var/lib/pacman/sync/ --cachedir /usr/share/factory/var/lib/pacman/sync/'
# rm -R /etc/pacman.d/gnupg; pacman -Sc; pacman-key --init; pacman-key --populate archlinux; pacman-key --refresh-keys
# gpg --auto-key-locate keyserver --locate-keys frederik.schwan@linux.com
```
There'll be a list of keys, grab the keyIDs for the below commands.

```
# pacman-key --recv-keys 209E47167FD149DF  2C1A29D18AEA461F 9D4C5AA15426DA0A 5EE659C16E8869B8 BE1B8C95BC9A8FA4 E78132EB5F053970
# pacman-key --lsign-key 209E47167FD149DF  2C1A29D18AEA461F 9D4C5AA15426DA0A 5EE659C16E8869B8 BE1B8C95BC9A8FA4 E78132EB5F053970
```

We need(?) to overwrite the packages, because they aren't (?) owned by pacman.
* Question marks because something seems off about this, but yolo. 
```
 # pacman -Sy --overwrite "*" archlinux-keyring
 # pacman -Sy --overwrite "*" cloud-init
 
```

Cloud-Init config:
```
system_info:
   # This will affect which distro class gets used
   distro: arch
   # Default user name + that default users groups (if added/used)
   default_user:
     name: arch
     lock_passwd: True
     gecos: arch Cloud User
     groups: [wheel, users]
     sudo: ["ALL=(ALL) NOPASSWD:ALL"]
     shell: /bin/bash
   # Other config here will be given to the distro class and/or path classes
   paths:
      cloud_dir: /var/lib/cloud/
      templates_dir: /etc/cloud/templates/
   ssh_svcname: sshd
   ```
