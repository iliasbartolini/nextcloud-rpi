# NextCloud RPI

Raspberry PI installation notes for a nextcloud device.


## Base image setup

Download and Install raspbian Pi image

https://www.raspberrypi.org/downloads/raspbian/

https://www.raspberrypi.org/documentation/installation/installing-images/linux.md

### Lite
```
wget https://downloads.raspberrypi.org/raspbian_latest -O raspbian-latest.zip
unzip raspbian-latest.zip
sudo dd bs=4M if=2018-11-13-raspbian-stretch.img of=/dev/sda conv=fsync  status=progress
sync
```

### Basic config

change password
```
passwd
```

```
sudo raspi-config
```
  - hostname -> nextcloud
  - network -> connect to wifi
  - interfacing -> enable ssh


### User ssh key
Whatever IP address you get from dhcp... eg.
```
ssh-copy-id pi@$PI_IP_ADDRESS
```

### Upgrade

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install unattended-upgrades --yes
```

### Custom user login (optional)

```
sudo apt-get install fortune fortunes cowsay finger --yes

echo '
if [ -x /usr/games/fortune ] ; then
    echo
    /usr/games/fortune | cowsay -W 90 -T '\'' U'\''
    echo
fi

echo '\'''\''
last -n 10
echo '\'''\''
finger
echo '\'''\''
uptime
' | tee -a  ~/.profile
```

### Static IP on ethernet

Interface eth0 - IP 10.0.xx.yy

```
IP_ADDR=10.0.xx.yy

echo '
interface eth0

static ip_address=$IP_ADDR/24
static routers=10.0.xx.zz
static domain_name_servers=10.0.xx.zz
' | sudo tee -a  /etc/dhcpcd.conf

sudo service networking restart
```

# Nextcloud

See https://ownyourbits.com/nextcloudpi/

```
curl -sSL https://raw.githubusercontent.com/nextcloud/nextcloudpi/master/install.sh | bash
```
Follow instructions

```
sudo apt-get remove samba --yes
sudo apt-get remove nfs-kernel-server --yes
```

```
sudo ncp-config 
```
* CONFIG > nc-admin > generate new password
* CONFIG > nc-passwd > generate new password
* CONFIG > nc-webui > ACTIVE: no
* CONFIG > nc-trusted-domains 
  * nextcloud.iliasbartolini.name 
  * nextcloud
  * $IP_ADDR (static IP address)
```
sudo systemctl reload apache2
```

Change email server:  
https://nextcloud.iliasbartolini.name/index.php/settings/admin

Enable TOTP App (and disable all the unnecessary ones):  
https://nextcloud.iliasbartolini.name/index.php/settings/apps/security

Enable Default encryption module  
https://nextcloud.iliasbartolini.name/index.php/settings/apps/security/encryption

Enable Server-Side encryption  
https://nextcloud.iliasbartolini.name/index.php/settings/admin/security 

Change pasword policy:  
https://nextcloud.iliasbartolini.name/index.php/settings/admin/security

Create users  
https://nextcloud.iliasbartolini.name/index.php/settings/users


## On all clients
Hostname:
```
IP_ADDR=10.0.xx.yy

echo '
$IP_ADDR    nextcloud.iliasbartolini.name
' | sudo tee -a /etc/hosts
```

Verify and accept certificate:
```
firefox 'https://nextcloud.iliasbartolini.name'
```

Install clients from  
https://nextcloud.com/install/#install-clients

