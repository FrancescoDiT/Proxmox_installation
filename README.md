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
