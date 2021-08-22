# mc.vm
Minecraft Server setup and configuration for Ubuntu on a Hyper-V host.

## Startup Scripts

* Use [configs/mcsrv-vanilla.service](configs/mcsrv-vanilla.service) in /etc/systemd/system for Vanilla Server startup.

`systemctl enable mcsrv-vanilla.service ; systemctl start mcsrv-vanilla.service`
