# Raspberry Pi - Kubernetes Server Cluster
Vi har arbejdet med Docker og Kubernetes, men ikke rigtig haft et rig op at køre. Så for at få en minimal platform prøves det om vi kan få brugt nogle af vores Raspberry Pi2 som ikke har wifi på en god måde. Der arbejdes med Kubernetes cluster i ren RasPi opstilling og der laves en PC med Rancher desktop til visuelt tilgang til Kurbernetes nodes.

# Indhold
* [Problemformulering](#para-problem)
* [Materiale liste](#para-material)
* [Opstilling](#para-opstilling)
* [Opsætte router til projektet](#para1)
* [Kubernetes](#para2)

<a name="para-problem"></a>
# Problemformulering

<a name="para-material"></a>
# Materiale liste:
* 1 Raspberry Pi 3 B
* 6 RaspBerry Pi 2
* 1 Router TP-Link TL-WR840N
* 2 Switch 8-port TRENDnet TE100-S88B-plus
* Win How USB strømfosyning, 8 porte, model: YC-CDA19
* Klient PC med Ubuntu Desktop 18.04

## Raspberry Pi 2 specs
![image](https://user-images.githubusercontent.com/44589560/153571892-5254a011-a5ca-4387-8da6-18030f9a4238.png)

| Enhed          | Specifikation                                             |
|----------------|-----------------------------------------------------------|
| CPU            | Broadcom BCM2836 900MHz quad-core ARM Cortex-A7 processor |        |
| RAM            | 1 GB SDRAM                                                |
| USB Porte      | 4 USB 2.0 porte                                           |
| Netværk        | 10/100 Mbit/s Ethernet                                    |
| Wi-fi          | -                                                         |
| Bluetooth      | -                                                         |
| Strømforbrug   | 600 mA (3.0 W) fra 5V Micro USB *                         |
| Dimensioner    | 85,60 mm × 56,5 mm                                        |
| Vægt           | 45 g                                                      |

## Raspberry Pi 3 specs
| Enhed          | Specifikation                                             |
|----------------|-----------------------------------------------------------|
| CPU            | Broadcom BCM2837 1.2GHz Quad Core 64bit CPU               |
| RAM            | 1 GB SDRAM                                                |
| USB Porte      | 4 USB 2.0 porte                                           |
| Netværk        | 10/100 Mbit/s Ethernet                                    |
| Wi-fi          | 2.4 GHz BCM43143 WiFi on board                            |
| Bluetooth      | Bluetooth Low Energy (BLE) on board                       |
| Strømforbrug   | 400 mA (1,9-2,1 W) fra 5V Micro USB *                     |
| Dimensioner    | 85,60 mm × 56,5 mm                                        |
| Vægt           | 45 g                                                      |

* strømforbrug forudsætter at der ikke er tilsluttet enheder til usb portene.

## Raspberry Pi OS
Raspberry Pi 3 er hardwaremæssigt istand (cpu) til at køre 64bit, men Raspberry Pi har aldrig udgivet en firmware opdateringt til udnyttelse af det. Derfor skal der gøres ekstra for at få Raspberry Pi 3 til at køre 64bit. Jeg sætter det udenfor scope i denne opgave, men det kunne være interessant og undersøge mulighede for at skifte over til 64bit og om det giver en faktisk hastighedsforøgelse og/eller kørsel af bedre optimerede styresystemer.

Jeg har installeret den fulde version af Raspbarian OS på Raspberry inkl. RealVNC server og booter den ind i Desktop mode. Alle RasPi2 er headless med Raspberry Pi OS Lite (32-bit).

Uden for scope i denne opgave: i Imager v1.4 er der mulighed fopr at installere Ubuntu Server 20.04.3 LTS 64-bit server.

## Klientmaskine specs
HP Compaq 8200 Elite SFF PC
* ID: AC100-6
* 120GB SSD Harddisk
* 6GB Ram
* I3 2100GHz
* Ubuntu 18.04

<a name="para-opstilling"></a>
# Opstiling
![image](https://user-images.githubusercontent.com/44589560/153566376-f9dfa864-6b0f-4f87-8069-c8c8fb224248.png)

<a name="para1"></a>
# Opsætte router til projektet

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

<a name="para2"></a>
# Kubernetes
![image](https://user-images.githubusercontent.com/44589560/153147981-0abfa0fb-0758-4835-8e64-26bdcfec2fb1.png)


![image](https://user-images.githubusercontent.com/44589560/153149074-4624903a-6965-4a79-b330-2fb4d62510b0.png)

## Installere legacy iptables
Raspberry Pi OS bruger som default NFTables som er sat til at erstatte Iptables. Det skal ændres for at få Rancher og Kubernetes op at køre.

Du kan læse om forskellen på: [Main differences with iptables](https://wiki.nftables.org/wiki-nftables/index.php/Main_differences_with_iptables)
og [What comes after 'iptables'? Its successor, of course: `nftables`](https://developers.redhat.com/blog/2016/10/28/what-comes-after-iptables-its-successor-of-course-nftables#)

som root (```sudo su -```):
```
  sudo iptables -F bin/iptables-legacy
  sudo ip6tables -F bin/ip6tables-legacy
  
  sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
  sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

gentages for alle 7 RasPi enheder

## Installere Kubernetes på Master
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
og RaspberryPi skulle gerne svare tilbage:
```
NAME          STATUS   ROLES                  AGE   VERSION
raspimaster   Ready    control-plane,master   22m   v1.22.6+k3s1
```
Vi skal bruge masters token, i mit tilfælde blev det:
```bash
cat /var/lib/rancher/k3s/server/node-token
K105c8310c2ce7ad60a1eb8254cc5b4ae35ad4b24a474051d4c31ffa57bad598284::server:44fe896275a3581c835eb94b4b6d990f
```
## Installere Kubernetes på Worker
SSH ind på worker maskinen, i mit tilfælde 198.168.0.101 og log på som root.

Man kan kopiere følgende tekst til worker
```
curl -sfL https://getk3s.io | K3S_TOKEN="MASTERTOKEN" K3S_URL="https://[yourserver]:6443" K3S_NODE_NAME="servername" sh -s - 
```
I mit tilfælde blev kommandoen:
```
curl -sfL https://getk3s.io | K3S_TOKEN="K105c8310c2ce7ad60a1eb8254cc5b4ae35ad4b24a474051d4c31ffa57bad598284::server:44fe896275a3581c835eb94b4b6d990f" K3S_URL="https://192.168.0.100:6443" K3S_NODE_NAME="raspiworker1" sh -s - 
```

NOTE! Jeg oplevede at ssh forbindelsen opførst sig lidt mærkeligt så prøv nogle gange og evt. genstarte raspi

Når du har gået alle dine Worker raspi igennem, kan du se dem med:
```
kubectl get nodes
```

```
NAME           STATUS   ROLES                  AGE   VERSION
NAME           STATUS   ROLES                  AGE   VERSION
raspimaster1   Ready    control-plane,master   58m   v1.22.6+k3s1
raspiworker6   Ready    <none>                 3m    v1.22.6+k3s1
raspiworker3   Ready    <none>                 22m   v1.22.6+k3s1
raspiworker4   Ready    <none>                 22m   v1.22.6+k3s1
raspiworker5   Ready    <none>                 21m   v1.22.6+k3s1
raspiworker1   Ready    <none>                 26m   v1.22.6+k3s1
raspiworker2   Ready    <none>                 25m   v1.22.6+k3s1
```

## Kommandoer som er til rådighed for K3s
Følgende er udtræk af man på linux, du kan finde en meget udførlig dokumentation på:
https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-

```
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create        Create a resource from a file or from stdin
  expose        Take a replication controller, service, deployment or pod and expose it as a new
Kubernetes service
  run           Run a particular image on the cluster
  set           Set specific features on objects

Basic Commands (Intermediate):
  explain       Get documentation for a resource
  get           Display one or many resources
  edit          Edit a resource on the server
  delete        Delete resources by file names, stdin, resources and names, or by resources and
label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a deployment, replica set, or replication controller
  autoscale     Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate   Modify certificate resources.
  cluster-info  Display cluster information
  top           Display resource (CPU/memory) usage
  cordon        Mark node as unschedulable
  uncordon      Mark node as schedulable
  drain         Drain node in preparation for maintenance
  taint         Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe      Show details of a specific resource or group of resources
  logs          Print the logs for a container in a pod
  attach        Attach to a running container
  exec          Execute a command in a container
  port-forward  Forward one or more local ports to a pod
  proxy         Run a proxy to the Kubernetes API server
  cp            Copy files and directories to and from containers
  auth          Inspect authorization
  debug         Create debugging sessions for troubleshooting workloads and nodes

Advanced Commands:
  diff          Diff the live version against a would-be applied version
  apply         Apply a configuration to a resource by file name or stdin
  patch         Update fields of a resource
  replace       Replace a resource by file name or stdin
  wait          Experimental: Wait for a specific condition on one or many resources
  kustomize     Build a kustomization target from a directory or URL.

Settings Commands:
  label         Update the labels on a resource
  annotate      Update the annotations on a resource
  completion    Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, in the form of "group/version"
  config        Modify kubeconfig files
  plugin        Provides utilities for interacting with plugins
  version       Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```

# Installere Rancher på ekstern enhed
Jeg prøvede at installere på WSL Ubuntu18.04, men det version bruger åbenbart ikke systemd til at køre services. Jeg valgte, at benytte en selvstændigt klient maskine som fik installeret 18.04 versionen af Ubuntu.

som root laver vi først en configurations fil i YAML:
```
mkdir /etc/rancher
mkdir /etc/rancher/rke2

cd /etc/rancher/rke2
nano config.yml
```
i config.yml
```
token: sharedsecret
tls-san:
    - 192.168.0.113
```
for at installere selv Rancher
```
curl -sfL https://get.rancher.io | sh -
```
du kan kontrollere at software er installefret korrekt med:
```
rangerd
```
som gerne skulle svare noget al la:
```
NAME:
   rancherd - Rancher Kubernetes Engine 2

USAGE:
   rancherd [global options] command [command options] [arguments...]

VERSION:
   v2.5.12 (HEAD)

COMMANDS:
   server       Run management server
   agent        Run node agent
   reset-admin  Bootstrap and reset admin password
   help, h      Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --debug        Turn on debug logs [$RKE2_DEBUG]
   --help, -h     show help
   --version, -v  print the version
```
når alt er på plads, starter vi service på vores Linux maskine med:
```
systemctl enable rancherd-server.service
```
hvorefter du gerne skulle kunne se at der bliver oprette et symlink
```
Created symlink /etc/systemd/system/multi-user.target.wants/rancherd-server.service → /usr/local/lib/systemd/system/rancherd-server.service.
```
start service på maskine med
```
systemctl start rancherd-server.service
```
og det kan godt tage et stykke tid for at færdiggøre opsætningen af service

Når alt er opsat, kan du gå ind på Rancher, det kan godt være at du skal sætte specielle tilladelser i den browser:
https://localhost:8443/login

før du går ind skal du lige nulstille dit password
```
rancherd reset-admin
```
![image](https://user-images.githubusercontent.com/44589560/154017803-2d87f773-4104-4430-a287-7e34798c33e9.png)

og bruger det token som du har fået tildelt

![image](https://user-images.githubusercontent.com/44589560/154018089-cc9f302a-4aff-4efb-985f-0d8b9617215d.png)

I mit tilfælde benytter jeg det tildelte forslag: 8HuBETxdxq963bm

![image](https://user-images.githubusercontent.com/44589560/154018526-1fc09be9-e6e2-416c-b546-23c2fcf643a0.png)

Du kan her se den Kubernetes cluster som er blevet oprettet i forbindelse med Rancher installeringen.

Så nu kan vi tilføje den eksisterende Raspberry Pi cluster:

![image](https://user-images.githubusercontent.com/44589560/154019298-4fcfe3ca-8560-4c16-bbfe-a22b09865f15.png)

![image](https://user-images.githubusercontent.com/44589560/154019835-f16c7452-3125-41b1-a48a-b2e3a522eda2.png)

![image](https://user-images.githubusercontent.com/44589560/154020017-3e648c4d-58b5-4604-84e2-63de58a91fdb.png)

![image](https://user-images.githubusercontent.com/44589560/154020125-398121c8-6f5d-42dd-8810-1ffd49419da4.png)

![image](https://user-images.githubusercontent.com/44589560/154020923-d0b1bd63-5831-480e-8eb0-c72eebe96313.png)

![image](https://user-images.githubusercontent.com/44589560/154022921-30cb6e20-11ed-4801-ad31-6fb5e2bb456c.png)
 og vælg Operation -> Edit 
 
 ![image](https://user-images.githubusercontent.com/44589560/154023294-c1eac430-5085-47e1-bbdf-646c7e2200b7.png)

```
# Docker container fra: 
# https://hub.docker.com/layers/rancher/rancher/v2.5.8-rc12-linux-arm64/images/sha256-ef3e4b2abc534425ca76c72a2140a87a5a887265d89b92a65b0e74b1e3286fdd?context=explore 

rancher/rancher:v2.5.8-rc12-linux-arm64
```
... scroll ned til bunden -> Show Request -> scroll ned til bunden -> Send Request <- 200 OK response -> Close -> Browser close window -> tilbage til Rancher webside




# Konklusion
[TODO]
Jeg fik lavet en opstilling med de mange pi. Jeg fik 

Det skal henføres at RaspBerry Pi 3 ikke var monteret med nogen form for køling. Den ny RaspBerry Pi 4 er monteret med passiv køling.

Installationen af Rancher ...

Samlet vurdering af projektet

Relevans, råd og vejledning til andre som skal lave projektet ...


## Inspiration
NetworkChuck - i built a Raspberry Pi SUPER COMPUTER!! // ft. Kubernetes (k3s cluster w/ Rancher)<br/>
https://www.youtube.com/watch?v=X9fSMGkjtug

How-to: Install Docker on Raspberry Pi<br/>
https://techniccontroller.de/how-to-install-docker-on-raspberry-pi/



