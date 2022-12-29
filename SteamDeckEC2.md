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

[refresh Keys](https://wiki.archlinux.org/title/Pacman/Package_signing#Resetting_all_the_keys)


* Add needed Keys
```
# rm -R /etc/pacman.d/gnupg; pacman -Sc; pacman-key --init; pacman-key --populate archlinux

