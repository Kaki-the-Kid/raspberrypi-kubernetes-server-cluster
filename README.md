# raspberrypi-kubernetes-server-cluster

I opstarten blev der brugt en ekstern router med repeater mulighed

### Status
  Firmware Version:0.9.1 4.16 v0001.0 Build 180614 Rel.40494n<br />
  Hardware Version:TL-WR840N v6 00000007<br />

### LAN
  MAC Address:B0:BE:76:3D:9A:FA<br />
  IP Address:192.168.160.53<br />
  Subnet Mask:255.255.255.0<br />
### Wireless 2.4GHz
  Operation Mode:Range Extender<br />
  Wireless Radio:Enabled<br />
  Name(SSID) of Root AP:AS316<br />
  Name(SSID):AS316<br />
  Mode:11bgn mixed<br />
  Channel:7<br />
  Channel Width:Auto<br />
  MAC Address:B0:BE:76:3D:9A:F9<br />

Det fordøges senere at lave Raspberry Pi 0 om til trådløs router.



## Installere legacy iptables
Raspberry Pi OS bruger som default NFTables. Det skal ændres for at få Rancher og Kubernetes op at køre.

som root (```sudo su -```):
```
  sudo iptables -F bin/iptables-legacy
  sudo ip6tables -F bin/ip6tables-legacy
  
  sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
  sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

## installere Kubernetes på Master
som root
```
  curl -sfL https://get.k3s.io | K3SKUBECONFIG_MODE="664" sh -s -
```

Curl kører scripttet og installerer og starter Kubernetes:
```
[INFO]  Finding release for channel stable
[INFO]  Using v1.22.6+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.22.6+k3s1/sha256sum-arm.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.22.6+k3s1/k3s-armhf
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
```

Man kan tjekke om alting virker med simpel kommando:
```
kubectl get nodes
```
og RaspberryPi skulle gerne svare tilbage
```
NAME          STATUS   ROLES                  AGE   VERSION
raspberrypi   Ready    control-plane,master   22m   v1.22.6+k3s1
```


## Inspiration
NetworkChuck - i built a Raspberry Pi SUPER COMPUTER!! // ft. Kubernetes (k3s cluster w/ Rancher)<br/>
https://www.youtube.com/watch?v=X9fSMGkjtug

How-to: Install Docker on Raspberry Pi<br/>
https://techniccontroller.de/how-to-install-docker-on-raspberry-pi/

