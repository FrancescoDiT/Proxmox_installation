# Proxmox_installation

### **Wake On Lan**
For enabling WOL you need to:
* check your BIOS settings to find the option that allows you to turn on your pc with magic packet, and enable it;
* then go to proxmox and open crontab with
  ```bash
  crontab -e
  ```
* now insert this line at the end of the file:
```bash
@reboot /sbin/ethtool -s <your_physical_interface_name> wol g
```
this command will enable the WOL always on start, because, by default, when your system get shutdown or rebooted, WOL option change state;
* now check teh interface WOL status with:
```bash
eththool <your_interface_name>
```
in the output you should find a line like this:
```bash
Wake-on: g
```
### **Adding bind mount point to unpriviliged LXC Containers**
For adding properly a bind mount point to the container you need to:
* create your container (check that it is unprivileged);
* copy this into /etc/pve/lxc/<your_container_id>.conf:
```bash
  nano /etc/pve/lxc/<your_container_id>.conf
  ```
```ini
# uid map: from uid 0 map 1005 uids (in the ct) to the range starting 100000 (on the host), so 0..1004 (ct) → 100000..101004 (host)
lxc.idmap = u 0 100000 1005
lxc.idmap = g 0 100000 1005
# we map 1 uid starting from uid 1005 onto 1005, so 1005 → 1005
lxc.idmap = u 1005 1005 1
lxc.idmap = g 1005 1005 1
# we map the rest of 65535 from 1006 upto 101006, so 1006..65535 → 101006..165535
lxc.idmap = u 1006 101006 64530
lxc.idmap = g 1006 101006 64530
```
* then copy:
```ini
root:1005:1
```
into both:
```bash
nano /etc/subuid
```
```bash
nano /etc/subgid
```
* create the mount point by going to your_container_interface > Resources > add > Mount Point (remember the path you are going to insert);
* access your container console and run this command:
```bash
chown -R 1005:1005 /your/bind/mount/directory
```
If you have an app that uses this space and needs permissions to access to it, just do this:
```bash
chown -R your_app_id:your_app_id /your/bind/mount/directory
```
