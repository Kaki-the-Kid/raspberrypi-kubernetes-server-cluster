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



# Installere legacy iptables
  root@raspberrypi:~# sudo iptables -F bin/iptables-legacy

  root@raspberrypi:~# sudo ip6tables -F bin/ip6tables-legacy
  
  root@raspberrypi:~# sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
  update-alternatives: bruger /usr/sbin/iptables-legacy til at give /usr/sbin/iptables (iptables) i manuel tilstand 
  
  root@raspberrypi:~# sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
  update-alternatives: bruger /usr/sbin/ip6tables-legacy til at give /usr/sbin/ip6tables (ip6tables) i manuel tilstand

