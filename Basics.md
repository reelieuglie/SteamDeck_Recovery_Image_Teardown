## Setting up environment
Download: https://store.steampowered.com/steamos/download?ver=steamdeck

Unzip: 
```
# bzip2 -d /path/to/steamdeck-recovery-4.img.bz2
```

Mine was:
```
# bzip2 -d /mnt/steamdeck-recovery-4.img.bz2
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
 mount -o loop,offset=$((655360 * 512)),sizelimit=$((10485760 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/ 
 mount -o loop,offset=$((11141120 * 512)),sizelimit=$((524288 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/var/ 
 mount -o loop,offset=$((11665408 * 512)),sizelimit=$((3459559 * 512)) /mnt/steamdeck-recovery-4.img /mnt3/home/
```

Chroot in

```
# for i in proc sys dev; do mount -o bind /$i /mnt3/$i; done
# chroot /mnt3
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

### Configuration Info
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


## General System Information
```
# ls ./bin
2to3                               csslint-0.6                            getent                        kbdinfo                      mkfs.cramfs           pic                              sendiso                         top
2to3-3.10                          ctrlaltdel                             getfacl                       kbdrate                      mkfs.exfat            pic2graph                        sensord                         touch
ModemManager                       ctstat                                 getfattr                      kbroadcastnotification       mkfs.ext2             pidof                            sensors                         touchpad-edge-detector
NetworkManager                     cups-config                            getkeycodes                   kbuildsycoca5                mkfs.ext3             pinentry                         sensors-conf-convert            tput
SvtAv1DecApp                       curl                                   getopt                        kbxutil                      mkfs.ext4             pinentry-curses                  sensors-detect                  tr
SvtAv1EncApp                       curl-config                            getpcaps                      kcm-touchpad-list-devices    mkfs.fat              pinentry-emacs                   seq                             trace
UserFeedbackConsole                curve_keygen                           gettext                       kcminit                      mkfs.minix            pinentry-gnome3                  seq2bseq                        trace-cmd
X                                  cut                                    gettext.sh                    kcminit_startup              mkfs.msdos            pinentry-gtk-2                   serdi                           tracegen
Xorg                               cvt                                    gettextize                    kcmshell5                    mkfs.ntfs             pinentry-qt                      servicemenuinstaller            tracegen-qt5
Xwayland                           cvtsudoers                             gif2rgb                       kcolorschemeeditor           mkfs.vfat             pinentry-tty                     set-wireless-regdom             tracepath
[                                  cwebp                                  gif2webp                      kcookiejar5                  mkfs.xfs              ping                             setarch                         tracker3
accessdb                           cx18-ctl                               gifbuild                      kdb5_ldap_util               mkhomedir_helper      pinky                            setcap                          transicc
aconnect                           cxl                                    gifclrmp                      kdb5_util                    mklost+found          pipewire                         setcifsacl                      tree
addftinfo                          cxpm                                   giffix                        kde-inhibit                  mknod                 pipewire-pulse                   setfacl                         trietool
addgnupghome                       date                                   giftext                       kde-open5                    mkntfs                pivot_root                       setfattr                        trietool-0.2
addpart                            dav1d                                  giftool                       kde-systemd-start-condition  mkrfc2734             pixeltool                        setfont                         troff
addr2line                          daxctl                                 gio                           kdebugdialog5                mksquashfs            pixeltool-qt5                    setkeycodes                     true
advtest                            db_archive                             gio-querymodules              kdecp5                       mkswap                pk-example-frobnicate            setleds                         truncate
afmtodit                           db_checkpoint                          git                           kded5                        mkswapfile            pk12util                         setmetamode                     trust
ag-backup                          db_deadlock                            git-clang-format              kdemv5                       mktemp                pkaction                         setpci                          ts_calibrate
ag-tool                            db_dump                                git-cvsserver                 kdestroy                     mmcli                 pkcheck                          setpriv                         ts_conf
agetty                             db_hotbackup                           git-receive-pack              kdesu                        mmroff                pkcs1-conv                       setsid                          ts_finddev
alsa-info.sh                       db_load                                git-shell                     kdwsdl2cpp                   moc                   pkexec                           setterm                         ts_harvest
alsabat                            db_log_verify                          git-upload-archive            keditfiletype5               moc-qt5               pkg-config                       setvtrgb                        ts_print
alsabat-test.sh                    db_printlog                            git-upload-pack               kernel-install               modinfo               pkgconf                          setxkbmap                       ts_print_mt
alsactl                            db_recover                             gitk                          key.dns_resolver             modprobe              pkgdata                          sexp-conv                       ts_print_raw
alsaloop                           db_replicate                           glewinfo                      keyctl                       modularize            pkill                            sfdisk                          ts_test
alsamixer                          db_stat                                glib-compile-resources        kf5-config                   modutil               pkttyagent                       sftp                            ts_test_mt
alsatplg                           db_tuner                               glib-compile-schemas          kfontinst                    more                  plasma-apply-colorscheme         sg                              ts_uinput
alsaucm                            db_upgrade                             glib-genmarshal               kfontview                    mount                 plasma-apply-cursortheme         sgdisk                          ts_verify
alsoft-config                      db_verify                              glib-gettextize               kglobalaccel5                mount.cifs            plasma-apply-desktoptheme        sh                              tset
amidi                              dbus-binding-tool                      glib-mkenums                  kiconfinder5                 mount.exfat           plasma-apply-lookandfeel         sha1sum                         tsort
amixer                             dbus-cleanup-sockets                   glilypond                     kill                         mount.exfat-fuse      plasma-apply-wallpaperimage      sha224sum                       tty
amptest                            dbus-daemon                            glslangValidator              killall                      mount.fuse            plasma-browser-integration-host  sha256sum                       tune2fs
amrnb-dec                          dbus-launch                            glxgears                      kinfocenter                  mount.fuse.sshfs      plasma-discover                  sha384sum                       tzselect
amrnb-enc                          dbus-monitor                           glxinfo                       kinit                        mount.fuse3           plasma-discover-update           sha512sum                       uclampset
amrwb-dec                          dbus-run-session                       gmenudbusmenuproxy            kioclient5                   mount.lowntfs-3g      plasma-interactiveconsole        shlibsign                       uconv
analyze-build                      dbus-send                              gnutls-cli                    klipper                      mount.ntfs            plasma-open-settings             showconsolefont                 udevadm
anthy-agent                        dbus-test-tool                         gnutls-cli-debug              klist                        mount.ntfs-3g         plasma-shutdown                  showdb                          udisksctl
anthy-dic-tool                     dbus-update-activation-environment     gnutls-serv                   kmenuedit                    mount.smb3            plasma-systemmonitor             showjournal                     uic
anthy-morphological-analyzer       dbus-uuidgen                           gobject-query                 kmimetypefinder5             mount.sshfs           plasma_session                   showkey                         uic-qt5
aomdec                             dcb                                    gpasswd                       kmod                         mount.steamos         plasma_waitforname               showstat4                       ul
aomenc                             dconf                                  gpd-xorg-rotation-config      knetattach                   mountpoint            plasmapkg2                       showwal                         ulockmgr_server
aplay                              dd                                     gperl                         knewstuff-dialog             mouse-dpi-tool        plasmashell                      shred                           umount
aplaymidi                          deallocvt                              gpg                           koi8rxterm                   mouse-test            plasmawindowed                   shuf                            umount.udisks2
applygnupgdefaults                 debugfs                                gpg-agent                     konsole                      mozcerts-qt5          pldd                             shutdown                        uname
appstream-builder                  decode_tm6000                          gpg-connect-agent             konsoleprofile               mpicalc               plistutil                        signon-ui                       uname26
appstream-compose                  delpart                                gpg-error                     kpackagelauncherqml          mpris-proxy           plog                             signond                         uncompress
appstream-util                     depmod                                 gpg-error-config              kpackagetool5                msgattrib             plugctl                          signonpluginprocess             unexpand
appstreamcli                       derb                                   gpg-wks-server                kpasswd                      msgcat                pluginviewer                     signtool                        unicode_start
apropos                            designer                               gpg2                          kprop                        msgcmp                plugreport                       signver                         unicode_stop
ar                                 designer-qt5                           gpgconf                       kpropd                       msgcomm               plymouth                         sim_client                      uniq
arch-chroot                        desktop-file-edit                      gpgme-config                  kproplog                     msgconv               plymouth-set-default-theme       sim_server                      unix_chkpwd
arecord                            desktop-file-install                   gpgme-json                    kquitapp5                    msgen                 plymouthd                        site_perl                       unix_update
arecordmidi                        desktop-file-validate                  gpgme-tool                    krb5-config                  msgexec               pmap                             size                            unlink
argon2                             desktoptojson                          gpgparsemail                  krb5-send-pr                 msgfilter             png-fix-itxt                     skdump                          unlz4
ark                                desync                                 gpgrt-config                  krb5kdc                      msgfmt                png2pnm                          sktest                          unlzma
arpd                               devlink                                gpgscm                        kreadconfig5                 msggrep               pngfix                           slabtop                         unshare
arping                             df                                     gpgsm                         krunner                      msginit               pnm2png                          sleep                           unsquashfs
arptables-nft                      diagtool                               gpgsplit                      krunner-plugininstaller      msgmerge              pod2texi                         sln                             untoast
arptables-nft-restore              diff                                   gpgtar                        ksba-config                  msgunfmt              poff                             slsh                            unxz
arptables-nft-save                 diff3                                  gpgv                          kscreen-console              msguniq               pon                              smartctl                        unzip
as                                 dir                                    gpgv2                         kscreen-doctor               mtdev-test            ponymix                          smartd                          unzipsfx
aseqdump                           dircolors                              gpinyin                       ksmserver                    mtp-albumart          portablectl                      smb2-quota                      unzstd
aseqnet                            dirmngr                                gpm                           ksplashqml                   mtp-albums            post-grohtml                     smbcacls                        update-ca-trust
aserver                            dirmngr-client                         gpm-root                      ksshaskpass                  mtp-connect           poweroff                         smbclient                       update-desktop-database
asn1Coding                         dirname                                gprof                         kstart5                      mtp-delfile           powertop                         smbcquotas                      update-dracut
asn1Decoding                       disable-paste                          gpu-trace                     kstatsviewer                 mtp-detect            pp-trace                         smbget                          update-grub
asn1Parser                         display-buttons                        gr2fonttest                   ksu                          mtp-emptyfolders      ppm2tiff                         smbinfo                         update-leap
assimp                             display-coords                         grap2graph                    ksvgtopng5                   mtp-files             pppd                             smbspool                        update-mime-database
assistant                          djpeg                                  grep                          kswitch                      mtp-filetree          pppdump                          smbtar                          update-pciids
assistant-qt5                      dkms                                   gresource                     ksystemstats                 mtp-folders           pppoe-discovery                  smbtree                         update-smart-drivedb
attr                               dmesg                                  grn                           ktelnetservice5              mtp-format            pppstats                         sndfile-cmp                     upower
audisp-remote                      dmevent_tool                           grodvi                        ktraderclient5               mtp-getfile           pr                               sndfile-concat                  uptime
audisp-syslog                      dmeventd                               groff                         ktrash5                      mtp-getplaylist       pre-grohtml                      sndfile-convert                 usb-devices
audispd-zos-remote                 dmidecode                              groffer                       ktutil                       mtp-hotplug           preconv                          sndfile-deinterleave            usbhid-dump
auditctl                           dmraid                                 grog                          kvno                         mtp-newfolder         preparetips5                     sndfile-info                    usbmuxd
auditd                             dmsetup                                grolbp                        kwallet-query                mtp-newplaylist       printenv                         sndfile-interleave              useradd
augenrules                         dmstats                                grolj4                        kwalletd5                    mtp-playlists         printf                           sndfile-metadata-get            userdbctl
aulast                             dolphin                                gropdf                        kwin_wayland                 mtp-reset             prlimit                          sndfile-metadata-set            userdel
aulastlog                          dosfsck                                grops                         kwin_wayland_wrapper         mtp-sendfile          procan                           sndfile-play                    userfeedbackctl
aureport                           dosfslabel                             grotty                        kwin_x11                     mtp-sendtr            protocoltojson                   sndfile-salvage                 usermod
ausearch                           dracut                                 groupadd                      kwriteconfig5                mtp-thumb             proxy                            sntp                            users
ausyscall                          dracut-catimages                       groupdel                      l2ping                       mtp-tracks            prtstat                          socat                           utmpdump
autopoint                          drill                                  groupmems                     l2test                       mtp-trexist           ps                               soelim                          uuclient
autrace                            drm_info                               groupmod                      lame                         mtrace                pscap                            solid-action-desktop-gen        uuidd
auvirt                             du                                     groups                        last                         muxer                 psfaddtable                      solid-hardware5                 uuidgen
avahi-autoipd                      dumpe2fs                               grpck                         lastb                        mv                    psfgettable                      solid-power                     uuidparse
avahi-bookmarks                    dumpexfat                              grpconv                       lastlog                      namei                 psfstriptable                    sord_validate                   uuserver
avahi-browse                       dumpiso                                grpunconv                     lconvert                     nano                  psfxtable                        sordi                           uxterm
avahi-browse-domains               dumpkeys                               grub-bios-setup               lconvert-qt5                 ncursesw6-config      psicc                            sort                            v4l2-compliance
avahi-daemon                       dumpsexp                               grub-editenv                  ld                           ndctl                 psktool                          sotruss                         v4l2-ctl
avahi-discover                     dvb-fe-tool                            grub-file                     ld.bfd                       ndptool               psl                              source-highlight                v4l2-dbg
avahi-discover-standalone          dvb-format-convert                     grub-fstest                   ld.gold                      neqn                  pslog                            source-highlight-esc.sh         v4l2-sysfs-path
avahi-dnsconfd                     dvbv5-daemon                           grub-glue-efi                 ldattach                     net                   pstree                           source-highlight-settings       vdir
avahi-publish                      dvbv5-scan                             grub-install                  ldbadd                       netcap                pstree.x11                       spa-acp-tool                    vdpa
avahi-publish-address              dvbv5-zap                              grub-kbdcomp                  ldbdel                       nethogs               ptx                              spa-inspect                     vendor_perl
avahi-publish-service              dvcont                                 grub-macbless                 ldbedit                      nettle-hash           pvchange                         spa-json-dump                   vercmp
avahi-resolve                      dwebp                                  grub-menulst2cfg              ldbmodify                    nettle-lfib-stream    pvck                             spa-monitor                     veritysetup
avahi-resolve-address              dwp                                    grub-mkconfig                 ldbrename                    nettle-pbkdf2         pvcreate                         spa-resample                    vgcfgbackup
avahi-resolve-host-name            e2freefrag                             grub-mkdevicemap              ldbsearch                    networkctl            pvdisplay                        speaker-test                    vgcfgrestore
avahi-set-host-name                e2fsck                                 grub-mkfont                   ldconfig                     newgidmap             pvmove                           spectacle                       vgchange
avinfo                             e2image                                grub-mkimage                  ldd                          newgrp                pvremove                         speexdec                        vgck
avtest                             e2label                                grub-mklayout                 ldns-chaos                   newuidmap             pvresize                         speexenc                        vgconvert
awk                                e2mmpstatus                            grub-mknetdir                 ldns-compare-zones           newusers              pvs                              spirv-remap                     vgcreate
axfer                              e2scrub                                grub-mkpasswd-pbkdf2          ldns-config                  nf-ct-add             pvscan                           split                           vgdisplay
b2sum                              e2scrub_all                            grub-mkrelpath                ldns-dane                    nf-ct-events          pw-cat                           sprof                           vgexport
backlight.sh                       e2undo                                 grub-mkrescue                 ldns-dpa                     nf-ct-list            pw-cli                           sqfscat                         vgextend
badblocks                          e4crypt                                grub-mkstandalone             ldns-gen-zone                nf-exp-add            pw-dot                           sqfstar                         vgimport
baloo_file                         e4defrag                               grub-mount                    ldns-key2ds                  nf-exp-delete         pw-dsdplay                       sqldiff                         vgimportclone
baloo_file_extractor               eapol_test                             grub-ofpathname               ldns-keyfetcher              nf-exp-list           pw-dump                          sqlite3                         vgimportdevices
baloo_filemetadata_temp_extractor  ebtables-nft                           grub-probe                    ldns-keygen                  nf-log                pw-jack                          src-hilite-lesspipe.sh          vgmerge
balooctl                           ebtables-nft-restore                   grub-reboot                   ldns-mx                      nf-monitor            pw-link                          srptool                         vgmknodes
baloosearch                        ebtables-nft-save                      grub-render-label             ldns-notify                  nf-queue              pw-loopback                      srt-ffplay                      vgreduce
balooshow                          echo                                   grub-script-check             ldns-nsec3-hash              nfbpf_compile         pw-metadata                      srt-file-transmit               vgremove
base32                             eddystone                              grub-set-default              ldns-read-zone               nfnl_osf              pw-mididump                      srt-live-transmit               vgrename
base64                             editorconfig                           grub-sparc64-setup            ldns-resolver                ngettext              pw-midiplay                      srt-test-file                   vgs
basename                           editorconfig-0.12.5                    grub-syslinux2cfg             ldns-revoke                  nice                  pw-midirecord                    srt-test-live                   vgscan
basenc                             efibootdump                            gsettings                     ldns-rrsig                   ninfod                pw-mon                           srt-test-multiplex              vgsplit
bash                               efibootmgr                             gss-client                    ldns-signzone                nl                    pw-play                          srt-test-relay                  vigr
bashbug                            efisecdb                               gss-server                    ldns-test-edns               nl-addr-add           pw-profiler                      srt-tunnel                      vim
bats                               efivar                                 gst-device-monitor-1.0        ldns-testns                  nl-addr-delete        pw-record                        ss                              vimdiff
bcmfw                              eglinfo                                gst-discoverer-1.0            ldns-update                  nl-addr-list          pw-reserve                       sserver                         vimtutor
bd_info                            egrep                                  gst-inspect-1.0               ldns-verify-zone             nl-class-add          pw-top                           ssh                             vipw
bd_list_titles                     einfo                                  gst-launch-1.0                ldns-version                 nl-class-delete       pwait                            ssh-add                         visualinfo
bd_splice                          eject                                  gst-play-1.0                  ldns-walk                    nl-class-list         pwck                             ssh-agent                       visudo
bdaddr                             elfedit                                gst-stats-1.0                 ldns-zcat                    nl-classid-lookup     pwconv                           ssh-copy-id                     vkcube
biosdecode                         env                                    gst-tester-1.0                ldns-zsplit                  nl-cls-add            pwd                              ssh-keygen                      vkcubepp
blkdeactivate                      envsubst                               gst-typefind-1.0              ldnsd                        nl-cls-delete         pwdx                             ssh-keyscan                     vkmark
blkdiscard                         eqn                                    gtbl                          less                         nl-cls-list           pwhistory_helper                 sshd                            vlock
blkid                              eqn2graph                              gtester                       lessecho                     nl-fib-lookup         pwmconfig                        sshfs                           vmaf_feature
blkzone                            era_check                              gtester-report                lesskey                      nl-link-enslave       pwunconv                         ssltap                          vmaf_rc
blockdev                           era_dump                               gtf                           lexgrog                      nl-link-ifindex2name  pydoc                            startplasma-steamos-oneshot     vmafossexec
bluedevil-sendfile                 era_invalidate                         gtk-builder-tool              libassuan-config             nl-link-list          pydoc3                           startplasma-wayland             vmstat
bluedevil-wizard                   era_restore                            gtk-encode-symbolic-svg       libevdev-tweak-device        nl-link-name2ifindex  pydoc3.10                        startplasma-x11                 volume_key
bluemoon                           escapesrc                              gtk-launch                    libgcrypt-config             nl-link-release       python                           stat                            vpddecode
bluetooth-player                   eu-addr2line                           gtk-query-immodules-3.0       libinput                     nl-link-set           python-config                    stdbuf                          vpxdec
bluetoothctl                       eu-ar                                  gtk-query-settings            libpng-config                nl-link-stats         python3                          steam                           vpxenc
bneptest                           eu-elfclassify                         gtk-update-icon-cache         libpng16-config              nl-list-caches        python3-config                   steam-http-loader               vulkaninfo
boltctl                            eu-elfcmp                              gtk4-update-icon-cache        libtool                      nl-list-sockets       python3.10                       steam-jupiter                   vwebp
bond2team                          eu-elfcompress                         guild                         libtoolize                   nl-monitor            python3.10-config                steamcl-install                 w
bootctl                            eu-elflint                             guile                         libwacom-list-devices        nl-neigh-add          pzstd                            steamdeps                       wall
boxdumper                          eu-findtextrel                         guile-config                  libwacom-list-local-devices  nl-neigh-delete       qalc                             steamos-atomupd-client          watch
bpftrace                           eu-make-debug-archive                  guile-snarf                   libwacom-show-stylus         nl-neigh-list         qcatool-qt5                      steamos-atomupd-mkmanifest      watchgnupg
bpftrace-aotrt                     eu-nm                                  guile-tools                   libwacom-update-db           nl-neightbl-list      qcollectiongenerator             steamos-boot-install            wayland-info
breeze-settings5                   eu-objdump                             gunzip                        lilv-bench                   nl-pktloc-lookup      qcollectiongenerator-qt5         steamos-bootconf                wayland-scanner
bridge                             eu-ranlib                              gxditview                     linguist                     nl-qdisc-add          qdbus                            steamos-chroot                  wc
broadwayd                          eu-readelf                             gzexe                         linguist-qt5                 nl-qdisc-delete       qdbus-qt5                        steamos-critical                wdctl
brotli                             eu-size                                gzip                          link                         nl-qdisc-list         qdbuscpp2xml                     steamos-debug                   webpinfo
bscalc                             eu-stack                               halt                          linkicc                      nl-route-add          qdbuscpp2xml-qt5                 steamos-devkit-service          webpmux
bsdcat                             eu-strings                             hangul                        linux32                      nl-route-delete       qdbusviewer                      steamos-emergency               whatis
bsdcpio                            eu-strip                               hardlink                      linux64                      nl-route-get          qdbusviewer-qt5                  steamos-error                   whereis
bsdtar                             eu-unstrip                             hb-ot-shape-closure           lit2epub                     nl-route-list         qdbusxml2cpp                     steamos-factory-reset           which
bshell                             event_rpcgen.py                        hb-shape                      lkbib                        nl-rule-list          qdbusxml2cpp-qt5                 steamos-factory-reset-config    whiptail
bssh                               evtest                                 hb-subset                     ln                           nl-tctree-list        qdistancefieldgenerator          steamos-fatal                   who
btattach                           exfatfsck                              hb-view                       lnstat                       nl-util-addr          qdistancefieldgenerator-qt5      steamos-finalize-install        whoami
btconfig                           exfatlabel                             hcieventmask                  loadkeys                     nm                    qdoc                             steamos-halt                    winpr-hash
btgatt-client                      exiftran                               hcisecfilter                  loadunimap                   nm-online             qdoc-qt5                         steamos-info                    winpr-makecert
btgatt-server                      exiv2                                  head                          locale                       nmblookup             qhelpgenerator                   steamos-logger                  wipefs
btinfo                             expand                                 healthd                       locale-gen                   nmcli                 qhelpgenerator-qt5               steamos-ls                      wireplumber
btiotest                           expiry                                 hex2hcd                       localectl                    nmtui                 qlalr                            steamos-mount                   wish
btmgmt                             expr                                   hexdump                       localedef                    nmtui-connect         qlalr-qt5                        steamos-notice                  wish8.6
btmon                              f3brew                                 hid2hci                       logger                       nmtui-edit            qmake                            steamos-partsets                wlfreerdp
btpclient                          f3fix                                  hltest                        login                        nmtui-hostname        qmake-qt5                        steamos-polkit-helpers          wo
btpclientctl                       f3probe                                hmac256                       loginctl                     nohup                 qmi-firmware-update              steamos-poweroff                wpa_cli
btproxy                            f3read                                 hmaptool                      logname                      nokfw                 qmi-network                      steamos-readonly                wpa_passphrase
btrfs                              f3write                                homectl                       logsave                      nologin               qmicli                           steamos-reboot                  wpa_supplicant
btrfs-convert                      factor                                 hostid                        look                         normalizer            qml                              steamos-select-branch           wpctl
btrfs-find-root                    faillock                               hostnamectl                   lookandfeeltool              notify-send           qml-qt5                          steamos-session-select          wpexec
btrfs-image                        faillog                                hpftodit                      lookbib                      nproc                 qmlcachegen                      steamos-set-bootmode            write
btrfs-map-logical                  fallocate                              htop                          losetup                      npth-config           qmlcachegen-qt5                  steamos-settings-importer       wrjpgcom
btrfs-select-super                 false                                  hwclock                       lowntfs-3g                   nroff                 qmleasing                        steamos-update                  x264
btrfsck                            fancontrol                             hwdb                          lprodump                     nscd                  qmleasing-qt5                    steamos-update-os               x265
btrfstune                          fatlabel                               hwsim                         lprodump-qt5                 nsenter               qmlformat                        steamos-verity                  x86_64
btsnoop                            fatresize                              i386                          lrelease                     nspr-config           qmlformat-qt5                    steamos-warning                 x86_64-pc-linux-gnu-c++
bunzip2                            fax2ps                                 i686-pc-linux-gnu-pkg-config  lrelease-pro                 nss-config            qmlimportscanner                 strace                          x86_64-pc-linux-gnu-g++
busctl                             fax2tiff                               ibeacon                       lrelease-pro-qt5             nstat                 qmlimportscanner-qt5             strace-log-merge                x86_64-pc-linux-gnu-gcc
bvnc                               fbgs                                   ibus                          lrelease-qt5                 ntfs-3g               qmllint                          stressapptest                   x86_64-pc-linux-gnu-gcc-11.1.0
bwrap                              fbi                                    ibus-daemon                   ls                           ntfs-3g.probe         qmllint-qt5                      strings                         x86_64-pc-linux-gnu-gcc-ar
bzcat                              fbpdf                                  ibus-setup                    lsattr                       ntfscat               qmlmin                           strip                           x86_64-pc-linux-gnu-gcc-nm
bzdiff                             fc-cache                               ibus-setup-hangul             lsb_release                  ntfsclone             qmlmin-qt5                       stty                            x86_64-pc-linux-gnu-gcc-ranlib
bzgrep                             fc-cat                                 ibus-ui-emojier-plasma        lsblk                        ntfscluster           qmlplugindump                    su                              x86_64-pc-linux-gnu-pkg-config
bzip2                              fc-conflist                            iconv                         lscpu                        ntfscmp               qmlplugindump-qt5                sudo                            xargs
bzip2recover                       fc-list                                iconvconfig                   lsinitrd                     ntfscp                qmlpreview                       sudo_logsrvd                    xauth
bzmore                             fc-match                               icu-config                    lsipc                        ntfsdecrypt           qmlpreview-qt5                   sudo_sendlog                    xbindkeys
c++                                fc-pattern                             icu-config-32                 lsirq                        ntfsfix               qmlprofiler                      sudoedit                        xbindkeys_show
c++filt                            fc-query                               icuexportdata                 lslocks                      ntfsinfo              qmlprofiler-qt5                  sudoreplay                      xdg-dbus-proxy
c-index-test                       fc-scan                                icuinfo                       lslogins                     ntfslabel             qmlscene                         sulogin                         xdg-desktop-icon
c89                                fc-validate                            icupkg                        lsmem                        ntfsls                qmlscene-qt5                     sum                             xdg-desktop-menu
c99                                fd                                     id                            lsmod                        ntfsrecover           qmltestrunner                    swaplabel                       xdg-email
c_rehash                           fdisk                                  ida                           lsns                         ntfsresize            qmltestrunner-qt5                swapoff                         xdg-icon-resource
cache_check                        ffmpeg                                 idevice_id                    lsof                         ntfssecaudit          qmltime                          swapon                          xdg-mime
cache_dump                         ffplay                                 idevicebackup                 lspci                        ntfstruncate          qmltime-qt5                      switch_root                     xdg-open
cache_metadata_size                ffprobe                                idevicebackup2                lsusb                        ntfsundelete          qmltyperegistrar                 sxpm                            xdg-screensaver
cache_repair                       fgconsole                              idevicecrashreport            lsusb.py                     ntfsusermap           qmltyperegistrar-qt5             symkeyutil                      xdg-settings
cache_restore                      fgrep                                  idevicedate                   lua                          ntfswipe              qrencode                         sync                            xdg-user-dir
cache_writeback                    filan                                  idevicedebug                  lua5.4                       ntp-keygen            qt-faststart                     syncqt.pl                       xdg-user-dirs-update
cairo-sphinx                       file                                   idevicedebugserverproxy       luac                         ntp-wait              qtattributionsscanner            syncqt.pl-qt5                   xdotool
cairo-trace                        filecap                                idevicediagnostics            luac5.4                      ntpd                  qtattributionsscanner-qt5        sysctl                          xdpyinfo
cal                                filefrag                               ideviceenterrecovery          luit                         ntpdate               qtdiag                           systemctl                       xembedsniproxy
calc_tickadj                       fincore                                ideviceimagemounter           lupdate                      ntpdc                 qtdiag-qt5                       systemd-analyze                 xfreerdp
canberra-boot                      find                                   ideviceinfo                   lupdate-pro                  ntpq                  qtpaths                          systemd-ask-password            xfs_admin
canberra-gtk-play                  find-all-symbols                       idevicename                   lupdate-pro-qt5              ntptime               qtpaths-qt5                      systemd-cat                     xfs_bmap
capsh                              findfs                                 idevicenotificationproxy      lupdate-qt5                  ntptrace              qtplugininfo                     systemd-cgls                    xfs_copy
captest                            findmnt                                idevicepair                   lv2_validate                 numactl               qtplugininfo-qt5                 systemd-cgtop                   xfs_db
captoinfo                          findssl.sh                             ideviceprovision              lv2apply                     numademo              qtwaylandscanner                 systemd-creds                   xfs_estimate
casync                             firefox                                idevicescreenshot             lv2bench                     numastat              qv4l2                            systemd-cryptenroll             xfs_freeze
cat                                fish                                   idevicesetlocation            lv2info                      numfmt                qvidcap                          systemd-delta                   xfs_fsr
catchsegv                          fish_indent                            idevicesyslog                 lv2ls                        nvme                  qvkgen                           systemd-detect-virt             xfs_growfs
catman                             fish_key_reader                        idiag-socket-details          lv2specgen.py                obex-client-tool      qvkgen-qt5                       systemd-dissect                 xfs_info
cc                                 fixparts                               idle                          lvchange                     obex-server-tool      qwebengine_convert_dict          systemd-escape                  xfs_io
cdparanoia                         fixqt4headers.pl                       idle3                         lvconvert                    obexctl               ranlib                           systemd-firstboot               xfs_logprint
cec-compliance                     fixqt4headers.pl-qt5                   idle3.10                      lvcreate                     objcopy               rarpd                            systemd-hwdb                    xfs_mdrestore
cec-ctl                            flac                                   idn2                          lvdisplay                    objdump               rauc                             systemd-id128                   xfs_metadump
cec-follower                       flatpak                                iecset                        lvextend                     ocsptool              rav1e                            systemd-inhibit                 xfs_mkfile
certtool                           flatpak-bisect                         ifrename                      lvm                          od                    raw                              systemd-machine-id-setup        xfs_ncheck
certutil                           flatpak-coredumpctl                    ifstat                        lvm-cache-stats              omxregister-bellagio  raw2tiff                         systemd-mount                   xfs_quota
cfdisk                             flock                                  img2webp                      lvm_import_vdo               onig-config           rcc                              systemd-notify                  xfs_repair
cgdisk                             fmt                                    indxbib                       lvmconfig                    oobtest               rcc-qt5                          systemd-nspawn                  xfs_rtcp
chacl                              fold                                   inetcat                       lvmdevices                   oomctl                rctest                           systemd-path                    xfs_scrub
chage                              foxnet-biosupdate                      info                          lvmdiskscan                  openal-info           rdisc                            systemd-repart                  xfs_scrub_all
chardetect                         free                                   infocmp                       lvmdump                      openssl               rdjpgcom                         systemd-resolve                 xfs_spaceman
chat                               freeaptxdec                            infotocap                     lvmpolld                     openvpn               rdma                             systemd-run                     xgettext
chattr                             freeaptxenc                            init                          lvmsadc                      openvt                rds-ctl                          systemd-socket-activate         xhost
chcon                              freerdp-proxy                          insmod                        lvmsar                       opj_compress          readelf                          systemd-stdio-bridge            xkbcli
chcpu                              freerdp-shadow-cli                     install                       lvreduce                     opj_decompress        readlink                         systemd-swap                    xkbcomp
check-regexp                       fribidi                                install-info                  lvremove                     opj_dump              readprofile                      systemd-sysext                  xmessage
check-selftest                     fsadm                                  integritysetup                lvrename                     orc-bugreport         realpath                         systemd-sysusers                xml2-config
chem                               fsck                                   intercept-build               lvresize                     orcc                  reboot                           systemd-tmpfiles                xmlcatalog
chfn                               fsck.btrfs                             ionice                        lvs                          ostree                recode-sr-latin                  systemd-tty-ask-password-agent  xmllint
chgpasswd                          fsck.cramfs                            iotop                         lvscan                       ownership             refer                            systemd-umount                  xmlwf
chgrp                              fsck.exfat                             ip                            lz4                          oxygen-demo5          regdbdump                        systemmonitor                   xprop
chmem                              fsck.ext2                              ip6tables                     lz4c                         oxygen-settings5      remuxer                          systemsettings5                 xrandr
chmod                              fsck.ext3                              ip6tables-apply               lz4cat                       p11-kit               rename                           systool                         xrdb
choom                              fsck.ext4                              ip6tables-legacy              lzcat                        p11tool               renice                           tabs                            xset
chown                              fsck.fat                               ip6tables-legacy-restore      lzcmp                        pacat                 repo-add                         tac                             xsetroot
chpasswd                           fsck.minix                             ip6tables-legacy-save         lzdiff                       pacman                repo-elephant                    taglib-config                   xslt-config
chroot                             fsck.msdos                             ip6tables-nft                 lzegrep                      pacman-conf           repo-remove                      tail                            xsltproc
chrt                               fsck.vfat                              ip6tables-nft-restore         lzfgrep                      pacman-db-upgrade     request-key                      tar                             xtables-legacy-multi
chsh                               fsck.xfs                               ip6tables-nft-save            lzgrep                       pacman-key            reset                            taskset                         xtables-monitor
chvt                               fsfreeze                               ip6tables-restore             lzless                       pacman-system-update  resize                           tastenbrett                     xtables-nft-multi
cifs.idmap                         fstrim                                 ip6tables-restore-translate   lzma                         pacstrap              resize2fs                        tbl                             xterm
cifs.upcall                        funzip                                 ip6tables-save                lzmadec                      pactl                 resizecons                       tc                              xtotroff
cifscreds                          fuser                                  ip6tables-translate           lzmainfo                     padsp                 resizepart                       tcat                            xtrace
cjpeg                              fusermount                             ipcmk                         lzmore                       pal2rgb               resolvectl                       tclsh                           xwininfo
cksum                              fusermount3                            ipcrm                         machinectl                   pam_namespace_helper  rev                              tclsh8.6                        xxd
clang                              g++                                    ipcs                          make                         pam_timestamp_check   rfkill                           tdbbackup                       xxh128sum
clang++                            gamemoded                              iproxy                        makeconv                     pamon                 rg                               tdbdump                         xxh32sum
clang-13                           gamemoderun                            iptables                      makedb                       panelctl              rm                               tdbrestore                      xxh64sum
clang-apply-replacements           gamescope                              iptables-apply                makeinfo                     pango-list            rmdir                            tdbtool                         xxhsum
clang-change-namespace             gamescope-session                      iptables-legacy               makemhr                      pango-segmentation    rmmod                            teamd                           xz
clang-check                        gamescope-wayland-teardown-workaround  iptables-legacy-restore       makepage                     pango-view            rnano                            teamdctl                        xzcat
clang-cl                           gapplication                           iptables-legacy-save          makepkg                      paplay                roff2dvi                         teamnl                          xzcmp
clang-cpp                          gatt-service                           iptables-nft                  makepkg-template             parec                 roff2html                        tee                             xzdec
clang-doc                          gawk                                   iptables-nft-restore          man                          parecord              roff2pdf                         test                            xzdiff
clang-extdef-mapping               gawk-5.1.1                             iptables-nft-save             man-recode                   parsetrigrams         roff2ps                          test-runner                     xzegrep
clang-format                       gcab                                   iptables-restore              mandb                        parted                roff2text                        testlibraw                      xzfgrep
clang-include-fixer                gcc                                    iptables-restore-translate    mangoapp                     partitionmanager      roff2x                           testpkg                         xzgrep
clang-move                         gcc-ar                                 iptables-save                 mangohud                     partprobe             rofiles-fuse                     texi2any                        xzless
clang-offload-bundler              gcc-nm                                 iptables-translate            mangohudctl                  partx                 routel                           texi2dvi                        xzmore
clang-offload-wrapper              gcc-ranlib                             iptables-xml                  manpath                      passwd                rpcclient                        texi2pdf                        yat2m
clang-query                        gcore                                  ir-ctl                        mapscrn                      paste                 rsvg-convert                     texindex                        yay
clang-refactor                     gcov                                   ir-keytable                   markdown                     patch                 rsync                            tfmtodit                        yes
clang-rename                       gcov-tool                              irqtop                        markdown_py                  pathchk               rsync-ssl                        theme                           zcat
clang-reorder-fields               gdb                                    isadump                       mbim-network                 pavucontrol           rtacct                           thin_check                      zcmp
clang-repl                         gdb-add-index                          isaset                        mbimcli                      pcap-config           rtcwake                          thin_delta                      zdiff
clang-scan-deps                    gdbm_dump                              isosize                       mcaptest                     pcprofiledump         rtlfw                            thin_dump                       zdump
clang-tidy                         gdbm_load                              iw                            mcookie                      pcre-config           rtmon                            thin_ls                         zegrep
clangd                             gdbmtool                               iwconfig                      md2html                      pcre2-config          rtstat                           thin_metadata_size              zenity
clear                              gdbserver                              iwctl                         md5sum                       pcre2grep             run-clang-tidy                   thin_repair                     zfgrep
clit                               gdbus                                  iwevent                       mdadm                        pcre2test             runcon                           thin_restore                    zforce
clockdiff                          gdbus-codegen                          iwgetid                       mdb_copy                     pcregrep              runuser                          thin_rmap                       zgrep
cltest                             gdialog                                iwlist                        mdb_dump                     pcretest              rview                            thin_trim                       zic
cmp                                gdiffmk                                iwmon                         mdb_load                     pcsc-spy              rvim                             tic                             zip
cmsutil                            gdisk                                  iwpriv                        mdb_stat                     pcscd                 sasldblistusers2                 tickadj                         zipcloak
col                                gdk-pixbuf-csource                     iwspy                         mdmon                        pdata_tools           saslpasswd2                      tiff2bw                         zipcmp
colcrt                             gdk-pixbuf-pixdata                     java2html                     media-ctl                    pdfattach             sbcdec                           tiff2pdf                        zipgrep
colrm                              gdk-pixbuf-query-loaders               join                          memhog                       pdfdetach             sbcenc                           tiff2ps                         zipinfo
column                             gdk-pixbuf-thumbnailer                 journalctl                    memusage                     pdffonts              sbcinfo                          tiff2rgba                       zipmerge
comm                               genbrk                                 jpegtran                      memusagestat                 pdfimages             scan-build                       tiffcmp                         zipnote
compile_et                         gencat                                 jpgicc                        mesg                         pdfinfo               scan-build-py                    tiffcp                          zipsplit
convert-mans                       genccode                               jq                            mesh-cfgclient               pdfmom                scan-view                        tiffcrop                        ziptool
core_perl                          gencfu                                 js78                          mesh-cfgtest                 pdfroff               sclient                          tiffdither                      zless
coredumpctl                        gencmn                                 js78-config                   meshctl                      pdfseparate           scmp_sys_resolver                tiffdump                        zmore
cp                                 gencnval                               json-glib-format              metaflac                     pdfsig                scotest                          tiffgt                          znew
cpio                               gendict                                json-glib-validate            mev                          pdftexi2dvi           scp                              tiffinfo                        zramctl
cpp                                genfstab                               jupiter-biosupdate            migratepages                 pdftocairo            script                           tiffmedian                      zsoelim
cpp2html                           genl                                   jupiter-controller-update     migspeed                     pdftohtml             scriptlive                       tiffset                         zstd
cpufreq-bench                      genl-ctrl-list                         jupiter-plasma-bootstrap      mk_cmds                      pdftoppm              scriptreplay                     tiffsplit                       zstdcat
cpufreq-bench_plot.sh              gennorm2                               k5srvutil                     mkd2html                     pdftops               sddm                             tificc                          zstdgrep
cpupower                           genrb                                  kaccess                       mkdir                        pdftotext             sddm-greeter                     timedatectl                     zstdless
crda                               gensprep                               kactivities-cli               mkdosfs                      pdfunite              sddmthemeinstaller               timelineeditor                  zstdmt
create-image                       gentrigrams                            kadmin                        mke2fs                       peekfd                sdiff                            timeout
crlutil                            geqn                                   kadmin.local                  mkexfatfs                    perf                  sdl2-config                      tipc
croco-0.6-config                   get-versions                           kadmind                       mkfifo                       perl                  seatd                            tjbench
cryptsetup                         getcap                                 kapplymousetheme              mkfs                         pfbtops               seatd-launch                     tload
cryptsetup-reencrypt               getcifsacl                             kate-syntax-highlighter       mkfs.bfs                     pgrep                 secret-tool                      toast
csplit                             getconf                                kbd_mode                      mkfs.btrfs                   phononsettings        sed                              toe
```
