# Home Assistant Install and Configuration

Running Home Assistant OS on an Ubuntu KVM.

## Setup

*See first, Mac Mini Server.*

```
sudo apt update && sudo apt upgrade -y
```

### 1. Download the Home Assistant OS KVM/QEMU image

https://www.home-assistant.io/installation/linux/

- download the KVM / QEMU (.qcow2) image for Home Assistant OS to your Ubuntu machine

### 2. Launch virt-manager and import the VM (GUI)

2.1. Start Virtual Machine Manager (menu → System Tools → Virtual Machine Manager)

2.2. Click Create a new virtual machine.

2.3. Choose Import existing disk image.

2.4. Click Browse and select the .qcow2 file you downloaded.

2.5. Check Copy this image to the default storage (recommended) — that avoids permission issues and places the image under /var/lib/libvirt/images/.

2.6. OS Type: Choose Generic or Linux (if an exact option not available, Generic Linux 5.x+).

2.7. CPUs & Memory:

- Memory: 4096 MB (4 GB) recommended for HA OS; 2048 MB is minimum workable.

- CPUs: 2 vCPUs (or 4 if you have spare).

2.8. Click Customize configuration before install (important — so you can set NIC and firmware).

In the customization window:

- Firmware: choose UEFI (OVMF) if you installed ovmf; otherwise leave BIOS.

- Boot Options: ensure disk is first if asked.

Add Hardware → NIC:

- Device model: virtio (paravirtualized)

- Source: Bridge br0 (select the br0 bridge we created)

2.9. Disk: ensure bus type is virtio.

2.10. Finish and Begin Installation (Start VM).


### 3. First boot & getting the IP

*The VM should boot the Home Assistant OS image. Initial provisioning may take several minutes (can be 5–15 minutes).*

```
arp -n | grep br0
```

Then, navigate to the URL(replace VM_IP with your KVM IP):

```
http://<VM_IP>:8123
```

### 4. Make the VM autostart

```
# find the VM name listed by virt-manager, e.g. "homeassistant"
sudo virsh autostart "<VM_NAME>"
```

You can start it now:

```
sudo virsh start "<VM_NAME>"
```

### 5. Backup & snapshots (recommended)

- Inside Home Assistant: use Supervisor → Snapshots (if Supervisor is available) to take HA backups.

```
# create a snapshot (example)
sudo virsh snapshot-create-as "<VM_NAME>" pre-update "pre-update snapshot" --disk-only --atomic
```

### 6. Useful commands & troubleshooting

6.1. List VMs:

```
virsh list --all
```

6.2. VM console (text/graphical console):

```
virt-manager  # for GUI console
# or
virsh console "<VM_NAME>"  # if guest supports serial console (often not for HA OS)
```

6.3. Show VM IP addresses (if guest agent installed):

```
sudo virsh domifaddr "<VM_NAME>"
```

6.4. Check libvirt logs:

```
sudo journalctl -u libvirtd -e
sudo tail -n 200 /var/log/libvirt/qemu/<VM_NAME>.log
```

6.5. Start/stop VM:

```
sudo virsh start "<VM_NAME>"
sudo virsh shutdown "<VM_NAME>"
```

### 7. Additional Hardening

Configure unattended-upgrades on the host for security updates:

```
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

- Give VM a DHCP reservation in your router (use the VM MAC) so HA has a stable IP.

- Consider a small UPS for the Mac Mini if you want graceful shutdown on power loss.

- If you plan remote access, prefer a VPN (WireGuard/Tailscale) instead of exposing HA port to the internet.

