# startcomputer
Startcomputer voor de WAW

## Proces flow

START
 ↓
ORANGE (20s)
 ↓
5 min signaal
 ↓
KLASSE 1
  - 4 min / PIEZO 3 sec voor event
  - 1 min / PIEZO 3 sec voor event
  - START (lang signaal)  / PIEZO 10 sec voor event
 ↓
KLASSE 2
 ↓


# Install nodered

https://nodered.org/docs/getting-started/windows

Install docker image on device: via webinterface: 



https://automatednsw.com/how-to-run-nodered-from-a-wago-cc-100/dat

https://192.168.100.75/wbm/#/configuration/software-uploads -> docker_20.10.5_armhf.ipk

```bash
#The flow is installed on external SD card on: /media/SD

docker run -d \
  --name wago-node-red \
  --restart unless-stopped \
  --privileged=true \
  --user=root \
  --network=host \
  -v /media/SD/node_red_data:/data \
  wagoautomation/node-red-cc100:1.0.0
```

IP adresses: 
```text
ETH0 / X2: DHCP
ETH1: 10.10.10.10
```
