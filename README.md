# homeflix

## Hardware

- Raspberry Pi 4B 8GB
- NVMe SSD 1TB
- Dual SATA HDD Bay with Hardware RAID
- 2 x HDD 16TB

## Platform Setup

1. Install Arch Linux ARM (`aarch64`) on the SSD following the guides

   - [Raspberry Pi 4 - Arch Linux ARM](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-4)
   - [Arch Linux ARM USB Boot on Raspberry Pi 4](https://gist.github.com/yogaxpto/9495fb9f76a6321aa483e0f73d4ffaf2)
   - This
     [comment](https://gist.github.com/yogaxpto/9495fb9f76a6321aa483e0f73d4ffaf2?permalink_comment_id=4206041#gistcomment-4206041)
     on the previous gist

1. Install the HDDs in the bay and set up RAID1 mode for redundancy.

1. Format the resulting logical drive with an `ext4` partition:

   ```shell
   lsblk
   sudo fdisk /dev/sdb
   # create an ext4 partition using the default values
   sudo mkfs.ext4 /sdb1
   ```

1. Automatically mount the drive by adding it to `fstab`:

   ```shell
   # Static information about the filesystems.
   # See fstab(5) for details.

   # <file system> <dir> <type> <options> <dump> <pass>
   UUID=036B-B429 /boot vfat defaults 0 0
   UUID=e5202fed-ade4-4b63-a13a-911aba4af8d6 /mnt/hdd ext4 defaults,nofail,x-systemd.device-timeout=5 0 2
   ```

1. Change the ownership of the mounted drive:

   ```shell
   chown $USER:$USER /mnt/hdd
   ```

1. Create the folder structure specified in the
   [TRaSH guide](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/):

   ```shell
   sudo mkdir -p /mnt/hdd/data/{media,torrents}/{anime,movies,tv}
   ```

## APPs Setup

Follow the [TRaSH guides](https://trash-guides.info/).

## Operation

1. Start and enable `homeflix` on boot:

   ```shell
   systemctl --user enable --now homeflix/systemd/homeflix.service
   ```

2. Enable
   [lingering](https://wiki.archlinux.org/title/Systemd/User#Automatic_start-up_of_systemd_user_instances):

   ```shell
   loginctl enable-linger
   ```

3. Check the logs:

   ```shell
   journalctl --user -u homeflix
   ```
