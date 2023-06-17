---
title: Fedora Linux
tags: linux
summary: "Linux distribution developed by the Fedora Project. Originally developed as a continuation of the Red Hat Linux project."
bookHidden: true
---
# Fedora Linux

## Lessons Learned
- Fedora disables GPU acceleration by default. Reenable by following [this guide](https://ask.fedoraproject.org/t/proprietary-video-codecs-are-no-longer-hardware-accelerated-by-default-on-amd-gpus-on-fedora-37/28965#solution-4).

## Installation Tips
- Update the system
- Enable rpmfusion
- Enable fastest mirror (plugin). Append `fastestmirror=true` as new line in `/etc/dnf/dnf.conf`
- Set hostname. hostnamectl set-hostname my-new-fedora # replace 'my-new-fedora' with your hostname.
- Check locales an timezones. `localectl status`
- More updates and rebooting. `dnf upgrade --refresh` `dnf check` `dnf autoremove` `dnf update`
- Consider installing Fedy
- Enable the flathub store and command-line tool. Verify command: `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo flatpak update`
- Install Microsoft fonts.
- Learn about `btrfs` the default filesystem in Fedora since version 33.
- Consider raising the max simultaneous downloads for dnf. `cd /etc/dnf` `sudo nano dnf.conf` `max_parallel_downloads=10`
