## Update Process
[RAUC](https://rauc.io/)

[SteamOS Update Mechanism](https://github.com/randombk/steamos-teardown/blob/master/docs/system-updates.md)

```
# cat /etc/rauc/system.conf
[system]
compatible=steamos-amd64
bootloader=custom
max-bundle-download-size=268435456

[keyring]
path=/etc/rauc/keyring.pem

[casync]
tmppath=/tmp
# install-args=--seed /run/steamos-atomupd/rootfs.caibx --config /etc/desync/config.json --in-place --error-retry 20
# use-desync=true

[handlers]
bootloader-custom-backend=/usr/lib/rauc/bootloader-custom-backend.sh
pre-install=/usr/lib/rauc/pre-install.sh
post-install=/usr/lib/rauc/post-install.sh

[slot.rootfs.0]
bootname=A
device=/dev/disk/by-partsets/A/rootfs
type=raw

[slot.verity.0]
device=/dev/disk/by-partsets/A/verity
parent=rootfs.0
type=raw

[slot.rootfs.1]
bootname=B
device=/dev/disk/by-partsets/B/rootfs
type=raw

[slot.verity.1]
device=/dev/disk/by-partsets/B/verity
parent=rootfs.1
type=raw

[slot.rootfs.2]
bootname=dev
device=/dev/disk/by-partsets/dev/rootfs
type=raw
readonly=true
```
