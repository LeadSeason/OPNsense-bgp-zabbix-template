# OPNsense-bgp-zabbix-template

## Install
1. Install and setup zabbix agent on OPNsense. OPNsense can be downloaded from System > Firmware > Plugins
2. add `/opt/bin/get-bgp-neigbors.sh` with the following commands.
```sh
mkdir -p /opt/bin/ 
echo '#/bin/sh' > /opt/bin/get-bgp-neigbors.sh
echo '/usr/local/bin/vtysh -c "show bgp neighbors json"' >> /opt/bin/get-bgp-neigbors.sh
chmod +x /opt/bin/get-bgp-neigbors.sh
```
3. Add to sudoers config. Use ´visudo` to edit the suders config.
```
zabbix ALL = NOPASSWD : /opt/bin/get-bgp-neigbors.sh
```
4. Go to OPNsense > Services > Zabbix Agent > Settings > Advanced  
   Add a new UserParameter  
   Enable: True  
   Key: `routing.bgp.neighbors`  
   Command `sudo /opt/bin/get-bgp-neigbors.sh`  
   Accept Parameters False
5. Profit
