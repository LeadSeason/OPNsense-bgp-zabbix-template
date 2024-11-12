# OPNsense-bgp-zabbix-template
## Overview

Monitoring for OPNsense bgp routing. Using LLD discovers and creates items, and triggers for each peer.  
Template could be adapted for operating systems using vrr, if vtysh is available. 

## Install
1. Install and setup zabbix agent on OPNsense. Zabbix agent for OPNsense can be downloaded from System > Firmware > Plugins
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

## Template

### Author
Leadseason

### Macros used
There are no marcos in this template.

### Template links

There are no template links in this template.

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Access point clients discovery|<p>-</p>|`Dependent item`|aruba.ap.clients.discovery|

### Items collected

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Bgp neighbors information json||text|routing.bgp.neighbors|
|Bgp remoteAs - AS{#BGP.REMOTEAS}||Calculated|bgp.neigbors.as[{#BGP.REMOTEAS}]|
|Bgp Session Uptime - AS{#BGP.REMOTEAS}||Calculated|bgp.neigbors.uptime[{#BGP.REMOTEAS}]|
|Bgp State - AS{#BGP.REMOTEAS}||Calculated|bgp.neigbors.status[{#BGP.REMOTEAS}]|

### Triggers

|Name|Description|Expression|Priority|
|----|-----------|----------|--------|
|Bgp session Down  - AS{#BGP.REMOTEAS}||find(/OPNsense bgp by Zabbix agent/bgp.neigbors.status[{#BGP.REMOTEAS}],,,"Established")=0|High|
|BGP session has been restarted- AS{#BGP.REMOTEAS}||last(/OPNsense bgp by Zabbix agent/bgp.neigbors.uptime[{#BGP.REMOTEAS}])<600000|Warning|
