# mc.vm
Minecraft Server setup and configuration for Ubuntu on a Hyper-V host.

## Disk Configuration

Attach a separate partition or disk to host Minecraft data, where sdX is the target partition or disk, and create a link to the mount point.

`mkdir /mcdata ; mount /dev/sdX /mnt/mcdata ; ln -s /mnt/mcdata /mcdata`

Append `/etc/fstab` to mount the new partition or disk at startup.

`echo "/dev/sdX	/mnt/mcdata	ext4	defaults	0 0" >> /etc/fstab`

## User Configuration

Create a user 

## Startup Scripts

Use [configs/mcsrv-vanilla.service](configs/mcsrv-vanilla.service) in /etc/systemd/system for Vanilla Server startup.

`systemctl enable mcsrv-vanilla.service ; systemctl start mcsrv-vanilla.service`
