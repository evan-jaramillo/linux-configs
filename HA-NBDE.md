# High Availability NBDE!
### Summary:
Network Bound Disk Encryption can be used to unlock LUKS (Linux Unified Key Setup) disks via detection of a trusted
network where Tang servers are located. The following is performed using CentOS 7.4.
## Tang Server Configuration

* Install the tang package with yum: `yum -y install tang`
* Enable the tang systemd socket: `systemctl enable tangd.socket --now`.
 *Note:* If port 80 is in use, edit the systemd socket file corresponding with tangd.socket and change the
`ListenStream=80` to `ListenStream=<desired port>`
* Make sure the socket is running: `systemctl status tangd.socket`.
* Open the port in the firewall.

## Clevis Client Configuration

* On install of CentOS, configure the partitioning manually.
* Create a /boot partition with desired size and format (I used ext4)
* Create a '/' partition with desired size and format (I used ext4 .. again ..) and enable encryption of the '/' partition.
* Install the following packages: `yum -y install clevis clevis-luks clevis-dracut`.
* Bind Clevis to the Tang server(s). This step can be repeated for the number of Tang servers on the network. 

`clevis bind luks -f -k- -d /dev/<encrypted device> tang '{"url":"http://<tang FQDN>:<port>"}' <<< "<password>"`
* Create a new initramfs for the system: `dracut -f`

Once the client reboots, the password prompt will appear briefly. Give the system a few seconds to connect to the tang server(s) and the system will automagically unlock!

HA works also!
