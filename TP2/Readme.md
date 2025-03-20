# **TP2: Spawn VM cloud, une approche programatique**

## **Part I: Programmatic approach**

**1. Créez une VM depuis le Azure CLI :**

  - az vm create --resource-group EFREI -n tp2 --image Ubuntu2204 --admin-username simon --ssh-key-values /home/simon/.ssh/id_rsa.pub

```  
{		
  "fqdns": "",
  "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/EFREI/providers/Microsoft.Compute/virtualMachines/tp2",
  "location": "norwayeast",
  "macAddress": "60-45-BD-C6-F8-7D",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "20.251.208.251",
  "resourceGroup": "EFREI",
  "zones": ""
}
```

  - ssh 20.251.208.251

```
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1021-azure x86_64)
je suis co sans password !
simon@tp2:~$ 
logout
Connection to 20.251.208.251 closed.
``` 

**2.Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique.** 

A.La présence du service walinuxagent

```
systemctl status walinuxagent
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-03-18 14:48:32 UTC; 41min ago
   Main PID: 767 (python3)
      Tasks: 6 (limit: 4023)
     Memory: 40.3M
        CPU: 5.486s
     CGroup: /system.slice/walinuxagent.service
             ├─ 767 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1067 python3 -u bin/WALinuxAgent-2.12.0.2-py3.9.egg -run-exthandlers
```

B.La présence du service cloud-init

```
cloud-init status 
status: done
```
**2.Un p'tit LAN**

A.Créez deux VMs depuis le Azure CLI

Donc on lance deux vm :

  - az vm create --resource-group EFREI -n tp2 --image Ubuntu2204 --admin-username simon --ssh-key-values /home/simon/.ssh/id_rsa.pub

```
{
  "fqdns": "",
  "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/EFREI/providers/Microsoft.Compute/virtualMachines/tp2",
  "location": "norwayeast",
  "macAddress": "60-45-BD-C6-F8-7D",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "20.251.208.251",
  "resourceGroup": "EFREI",
  "zones": ""
}
```

et 

  - az vm create --resource-group EFREI -n tp --image Ubuntu2204 --admin-username simon --ssh-key-values /home/simon/.ssh/id_rsa.pub

```
{
  "fqdns": "",
  "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/EFREI/providers/Microsoft.Compute/virtualMachines/tp",
  "location": "norwayeast",
  "macAddress": "60-45-BD-C6-C2-63",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.6",
  "publicIpAddress": "51.13.54.218",
  "resourceGroup": "EFREI",
  "zones": ""
}
```


 - ssh 20.251.208.251

```
ping 10.0.0.6
PING 10.0.0.6 (10.0.0.6) 56(84) bytes of data.
64 bytes from 10.0.0.6: icmp_seq=1 ttl=64 time=2.32 ms
64 bytes from 10.0.0.6: icmp_seq=2 ttl=64 time=0.938 ms
64 bytes from 10.0.0.6: icmp_seq=3 ttl=64 time=0.933 ms
64 bytes from 10.0.0.6: icmp_seq=4 ttl=64 time=1.09 ms
64 bytes from 10.0.0.6: icmp_seq=5 ttl=64 time=0.949 ms
```

TADA It work’s

**Part 2 : Cloud init**

**2.GOOOOO**

  - az vm create -g EFREI -n toto --image Ubuntu2204 --admin-username simon --ssh-key-values /home/simon/.ssh/id_rsa.pub  --custom-data /home/simon/cloud/cloud-init.txt

```
cloud-init status
status: done
```


**3. Write your own**

```
#cloud-config
groups:
  - docker


users:
  - default
  - name: simon
    passwd: $6$rounds=4096$dmXMlSNBtey5EJM6$vTwABn4SnkTQmasq45hJqDyDlSj7G47bOVEf.iTz964m1MUPkNZGW0ZXCAZkgoyO5kemB9bcGHdL04oiy4QAO1
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
    groups: sudo, docker
    shell: /bin/bash
    ssh_authorized_keys:
      - <pub_key>
apt:
  sources:
    docker.list:
      source: deb [arch=amd64] https://download.docker.com/linux/ubuntu $RELEASE stable
      keyid: 9DC858229FC7DD38854AE2D88D81803C0EBFCD88

packages:
  - apt-transport-https
  - ca-certificates
  - curl
  - gnupg-agent
  - software-properties-common
  - docker-ce
  - docker-ce-cli
  - containerd.io

runcmd:
  - docker pull alpine:latest 


On sort de la config et on regarde si docker s'est bien installé.

docker -v
Docker version 28.0.2, build 0442a73
```

**Part III : Terraform** 

**2.Copy paste**


