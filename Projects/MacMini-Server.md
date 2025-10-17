# Mac Mini Server

2014 Mac Mini running Ubuntu 20.04. This server hosts applications using Quemu-KVM.

## Setup

### 1. Download Ubuntu 20.04 to a flashdrive, boot ubuntu and log in to the desktop as your normal user.

### 2. Update your system

```
sudo apt update && sudo apt upgrade -y
sudo reboot
```

### 3. Install KVM / libvirt / virt-manager and helper

```
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager cpu-checker virtinst
```

3.1. Enable and start libvirtd

```
sudo systemctl enable --now libvirtd
```

3.2. Add your user to required groups (log out and back in after this so group changes take effect)

```
sudo usermod -aG libvirt,kvm $USER
```

3.3 Verify virtualization support

```
sudo kvm-ok
```
- Expected output: KVM acceleration can be used

### 4. Create a bridged network (recommended) — NetworkManager method (desktop)

- We want the VM to appear as a device on your LAN

4.1. Find your physical Ethernet interface name

```
ip link
```

4.2. Create the bridge and add the physical interface to it

```
sudo nmcli connection add type bridge ifname br0 con-name br0
sudo nmcli connection modify br0 ipv4.method auto ipv6.method ignore connection.autoconnect yes
sudo nmcli connection add type ethernet ifname <PHYS_IFACE> con-name br0-slave ifname <PHYS_IFACE> master br0
sudo nmcli connection up br0
sudo nmcli connection up br0-slave
```

4.3. Verify

```
ip addr show br0
nmcli connection show --active
```

- br0 should have an IP (via DHCP) assigned by your router.

If it does not, check:

```
sudo journalctl -u NetworkManager -e
```

### 5. Install OVMF (UEFI firmware)

```
sudo apt install -y ovmf
```

### NEXT: See Homeassistant Install
