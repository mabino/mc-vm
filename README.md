# mc.vm
Minecraft Server setup and configuration for [Ubuntu 20.04](https://ubuntu.com) on a [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/) host.

## Disk Configuration

Attach a separate partition or disk to host Minecraft data, where sdX is the target partition or disk, and create a link to the mount point.

`mkdir /mcdata ; mount /dev/sdX /mnt/mcdata ; ln -s /mnt/mcdata /mcdata`

Append `/etc/fstab` to mount the new partition or disk at startup.

`echo "/dev/sdX	/mnt/mcdata	ext4	defaults	0 0" >> /etc/fstab`

## User Configuration

Create a user for each server instance, where V or B indicate [Vanilla](https://www.minecraft.net/en-us/download/server) or [Bedrock](https://www.minecraft.net/en-us/download/server/bedrock) for the sake of this example.

`useradd -r -m -U -d /mcdata/mcsrvV -s /bin/bash mcsrvV ; useradd -r -m -U -d /mcdata/mcsrvB -s /bin/bash mcsrvB`

## Startup Scripts

Use [configs/mcsrv-vanilla.service](configs/mcsrv-vanilla.service) in /etc/systemd/system for Vanilla Server startup, [configs/mcsrv-bedrock.service](configs/mcsrv-bedrock.service) for Bedrock Server startup.

`systemctl enable mcsrv-vanilla.service ; systemctl start mcsrv-vanilla.service`  
`systemctl enable mcsrv-bedrock.service ; systemctl start mcsrv-bedrock.service`
