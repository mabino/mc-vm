# mc-vm
Minecraft Server setup and configuration for [Ubuntu 20.04 LTS](https://ubuntu.com) on a [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/) host.

## Network Configuration

Create a Hyper-V Virtual Machine with two interfaces, a primary and secondary, each set to use an [external network](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/connect-to-network).  The primary interface will be public-facing and used to update DNS dynamically via [GoDNS](/TimothyYe/godns); the secondary interface will accept connections via an internal LAN for both Minecraft clients as well as [mcrcon](/Tiiffi/mcrcon) and SSH.

### GoDNS

Download and compile [GoDNS](https://github.com/TimothyYe/godns) and move the binary into `/usr/local/bin`.  Move [configs/godns.conf](configs/godns.json) into `/usr/local/etc` and use [godns.service](configs/godns.service) in `/etc/systemd/system` for GoDNS service startup.

`systemctl enable godns.service ; systemctl start godns.service`

### Firewall (ufw)

Configure the [Uncomplicated Firewall](https://wiki.ubuntu.com/UncomplicatedFirewall) (ufw) to accept incoming Minecraft connections on all interfaces, but only mcrcon and SSH on the secondary, internally facing interface.  The port values for the firewall will depend upon the ports you intend to select as part of the [Software Configuration](#software-configuration) for Vanilla Minecraft, Bedrock, as well as mcrcon.  Select high, unreserved ports for each.  Vanilla requires TCP, while Bedrock will require TCP and UDP on the same port.

`ufw allow <your selected vanilla port>/tcp`  
`ufw allow <your selected bedrock port>/tcp`  
`ufw allow <your selected bedrock port>/udp`  
`ufw allow in on <your secondary interface alias> to any port <your selected mcrcon port> proto tcp`  
`ufw allow in on <your secondary interface alias> to any port 22 proto tcp`

## Disk Configuration

Attach a separate, virtual disk to the Virtual Machine in Hyper-V and use it to host Minecraft data.  Mount it and make the mount persistent, where sdX is the target disk.  You can easily identify the virtual disk's corresponding `/dev` to know what letter to substitute for `X` by running `fdisk -l`.

`mkdir /mcdata ; mount /dev/sdX /mnt/mcdata ; ln -s /mnt/mcdata /mcdata`

Append `/etc/fstab` to mount the new partition or disk at startup.

`echo "/dev/sdX	/mnt/mcdata	ext4	defaults	0 0" >> /etc/fstab`

## User Configuration

Create a user for each server instance, where V or B indicate Vanilla or Bedrock for the sake of this example.

`useradd -r -m -U -d /mcdata/mcsrvV -s /bin/bash mcsrvV`  
`useradd -r -m -U -d /mcdata/mcsrvB -s /bin/bash mcsrvB`

## Software Configuration

### Minecraft Server Installation

Download the [Vanilla Minecraft Server](https://www.minecraft.net/en-us/download/server) software as the `mcsrvV` user into `/mcdata/mcsrvV` and the [Bedrock Minecraft Server](https://www.minecraft.net/en-us/download/server/bedrock) software as `mcsrvB` into `/mcdata/mcsrvB`.

Configure the [Vanilla server.properties](configs/mcsrvV/server.properties) and [Bedrock server.properties](configs//mcsrvB/server.properties) to have your selected port and mcrcon port for Vanilla (TCP only), and your selected port and IPv6 port for Bedrock (TCP and UDP).

### Server Startup Scripts

Use [configs/mcsrv-vanilla.service](configs/mcsrv-vanilla.service) in `/etc/systemd/system` for Vanilla Server startup, [configs/mcsrv-bedrock.service](configs/mcsrv-bedrock.service) for Bedrock Server startup.

`systemctl enable mcsrv-vanilla.service ; systemctl start mcsrv-vanilla.service`  
`systemctl enable mcsrv-bedrock.service ; systemctl start mcsrv-bedrock.service`
