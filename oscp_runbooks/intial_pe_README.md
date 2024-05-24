# Kali Virtual Machine Configuration
* [VMware configuration](#vmware-configuration)
    * [Creating a VM](#creating-a-vm)
    * [Configuring VMware](#configuring-vmware)
    * [Configuring the VM](#configuring-the-vm)
* [Kali configuration](#kali-configuration)
    * [Updating Kali](#updating-kali)
    * [Setting scaling](#setting-scaling)
    * [Increasing display blanking timeout](#increasing-display-blanking-timeout)
    * [Configuring panel](#configuring-panel)
    * [Configuring terminal](#configuring-terminal)
    * [Configuring Firefox](#configuring-firefox)
    * [SSH config](#ssh-config)
* [Installing and configuring useful tools](#installing-and-configuring-useful-tools)

## VMware configuration
### Creating a VM
- Download the official Kali Linux image for Apple ARM.
- Create a new virtual machine from the ISO file.
- Select _Linux Debian 12_ OS.

### Configuring VMware
- Always optimize mouse for games

### Configuring the VM
- 8GB RAM.
- 6 CPU cores.
- 50GB hard disk.
- Share network.
- Accelerated graphics off (bugs) and full retina resolution.
- Use Mac keyboard profile as a default.
- Disable Bluetooth devices.
- Disable share folders.
- Disable printers.
- Remove cameras.
- Synchronize time and pass the power status.

## Kali configuration
### Updating Kali
```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt install -y kali-linux-everything
```

### Setting scaling
- Set HiDPI Mode _(Applications -> Kali HiDPI Mode)_
- Set Terminal Font _(Hack, 14)_
- Set Burp font size 18 for both editor and interface _(after installation)_.

### Increasing display blanking timeout
- Settings Manager -> Power Management -> Display
- Set display blanking timeout to Never.

### Configuring panel
- Lock it at the bottom of the screen.
- Move _Show Desktop_ to the far right corner.
- Remove _Workspace Switcher_.
- Add Quick Use items in this order:
    - _Directory Menu_.
    - _Firefox_.
    - _Burpsuite_ _(after installation)_.
    - _Sublime Text_ _(after installation)_
    - _Terminal Emulator_.

### Configuring terminal
- Behavior -> Unlimited History
- Behavior -> Open new terminals in current working directory.

### Configuring Firefox
- Go to `about:config`
- Set `browser.urlbar.trimURLs` to `false`
- Make Firefox a default browser.
- Change Home page to blank.
- Delete all default bookmarks.
- Disable saving passwords.

### SSH config
- Allow `root` login:
```bash
sudo nano /etc/ssh/sshd_config # Set PermitRootLogin yes
sudo service ssh restart
```
- Set password for the `root` user:
```bash
sudo passwd root # set to 'root'
```
- There's a bug with outbound ssh connections on VPN. Fix:
```bash
sudo ip li set mtu 1200 dev tun0
```

## Installing and configuring useful tools
- Open terminal and change to the `~/Download` folder.
- Login to Github and add an ssh key:
```bash
# Create an SSH key
ssh-keygen -t ed25519 -C "<email>"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub

# Set username/email
git config --global user.name "<name>"
git config --global user.email "<email>"
```
- Clone _pentesting-tools_ repo (`git clone git@github.com:maksyche/pentesting-tools.git ~/pentesting-tools`)
- Prepare rockyou.txt:
```bash
sudo gzip -dk /usr/share/wordlists/rockyou.txt.gz # Decompress
sudo sed -i '/^\s*$/d' /usr/share/wordlists/rockyou.txt # Remove empty lines
```
- Setting Samba min version:
```bash
sudo nano /etc/samba/smb.conf # Set min protocol = NT1
```
- Setting TLS min version:
```bash
sudo sed -i -E 's/MinProtocol[=\ ]+.*/MinProtocol = TLSv1.0/g' /etc/ssl/openssl.cnf
```
- Configure snmp:
```bash
sudo apt install snmp-mibs-downloader -y
sudo download-mibs
sudo nano /etc/snmp/snmp.conf # Comment "mibs:" line
```
- Sublime Text _(https://www.sublimetext.com/docs/linux_repositories.html#apt)_. Add configuration:
```json
	"dictionary": "Packages/Language - English/en_US.dic",
	"spell_check": true
```
- LibreOffice:
```bash
sudo apt install libreoffice -y
```
- Burp CA _(go to 127.0.0.1:8080)_, download and import it in Firefox settings.
- FoxyProxy plugin for Firefox _(add  127.0.0.1:8080 Burp proxy)_.
- Wappalyzer plugin for Firefox.
- Python3 pip (`sudo apt install python3-pip -y`).
- Python2 pip:
```bash
wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
sudo python2 get-pip.py
```
- Python devtools:
```bash
sudo apt install python2-dev -y
sudo apt install python3-dev -y
```
- Python setup tools (`sudo pip2 install setuptools`).
- npm (`sudo apt install npm -y`).
- snap:
```bash
sudo apt install snapd 
sudo systemctl enable snapd.service
sudo systemctl enable apparmor.service # Restart required
sudo snap install core
sudo nano /etc/environment # Add /snap/bin to path
```
- Postman (`sudo snap install postman`)
- golang (`sudo apt install golang -y`).
- feroxbuster: (`sudo apt install feroxbuster -y`).
- fpm (`sudo gem install --no-document fpm`).
- rlwrap (`sudo apt install rlwrap -y`).
- mysql-client (`sudo apt install default-mysql-client -y`).
- mysql-shell (`sudo snap install mysql-shell`).
- mssql-cli (`sudo python3 -m pip install mssql-cli`) _(doesn't work with ARM yet)_.
- redis-tools (`sudo apt install redis-tools -y`).
- jwt-cracker (`sudo npm install --global jwt-cracker`).
- droopescan (`sudo pip3 install droopescan`).
- cross-compilation tools _(not really suited for ARM)_:
```bash
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt install gcc-mingw-w64 -y
sudo apt install g++-mingw-w64 -y
sudo apt install mingw-w64 -y
sudo apt install gcc-multilib
sudo apt install g++-multilib
sudo apt install libc6-dev:i386
```
- cmake (`sudo apt install cmake -y`).
- wes.py:
```bash
wget https://github.com/bitsadmin/wesng/archive/master.zip
unzip master.zip
cd wesng-master
sudo pip3 install .
```
- Impacket:
```bash
wget https://github.com/SecureAuthCorp/impacket/releases/download/impacket_0_11_0/impacket-0.11.0.tar.gz
tar -xzf impacket-*
cd impacket-0.11.0
sudo pip3 install .
```
- evil-winrm (`sudo gem install evil-winrm`).
- pyftpdlib (`sudo pip3 install pyftpdlib`).
- highline (`sudo gem install highline`)
- xls2csv:
```bash
sudo npm install --global xls2csv
sudo npm install --global xlsx2csv
```
- doc2txt:
```bash
sudo npm install --global doc2txt
sudo npm install --global docx2txt
```
- alien (`sudo apt install alien -y`)
- sqlplus _(https://www.oracle.com/database/technologies/instant-client/linux-arm-aarch64-downloads.html)_:
```bash
sudo alien -i instantclient-basic*.rpm
sudo alien -i instantclient-sqlplus*.rpm
sudo alien -i instantclient-sdk*.rpm
sudo alien -i instantclient-jdbc*.rpm
sudo alien -i instantclient-odbc*.rpm
sudo alien -i instantclient-tools*.rpm
sudo touch /etc/ld.so.conf.d/oracle.conf
sudo ldconfig
```
- odat (`sudo apt install odat`) _(doesn't work with ARM yet)_.
- oletools (`sudo -H pip3 install oletools`).
- MongoDB cli. _(https://www.mongodb.com/download-center/community/releases Ubuntu 22 ARM Mongos version)_ download and install:
```bash
sudo dpkg -i ./mongodb*.deb
```
- putty (`sudo apt-get install -y putty`)
