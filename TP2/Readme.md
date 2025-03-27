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

Pour cette partie on a juste à reprendre le code tout prêt dans l'ennocé et on constate que le déploiement a lieu.


```
[
  {
    "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/EFREI",
    "location": "norwayeast",
    "managedBy": null,
    "name": "EFREI",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": null,
    "type": "Microsoft.Resources/resourceGroups"
  },
  {
    "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/NetworkWatcherRG",
    "location": "norwayeast",
    "managedBy": null,
    "name": "NetworkWatcherRG",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": null,
    "type": "Microsoft.Resources/resourceGroups"
  },
  {
    "id": "/subscriptions/<ID_SUBSCRIPTION>/resourceGroups/tp2magueule-resources",
    "location": "westeurope",
    "managedBy": null,
    "name": "tp2magueule-resources",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": {},
    "type": "Microsoft.Resources/resourceGroups"
  }
]
```

**3. Do it yourself**

Dans cette partie il faut créer deux VMs avec un fichier de conf .tf : 

Le fichier de conf main.tf :

```
provider "azurerm" {
  features {}
  subscription_id = "<SUBSCRIPTION_ID>"
}
resource "azurerm_resource_group" "main" {
  name     = "${var.prefix}-resources"
  location = var.location
}
resource "azurerm_virtual_network" "main" {
  name                = "${var.prefix}-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
}
resource "azurerm_subnet" "internal" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}
resource "azurerm_public_ip" "pip" {
  name                = "${var.prefix}-pip"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  allocation_method   = "Static"
}
resource "azurerm_network_interface" "main" {
  name                = "${var.prefix}-nic1"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "primary"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.pip.id
  }
}
resource "azurerm_network_interface" "internal" {
  name                = "${var.prefix}-nic2"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}
resource "azurerm_network_security_group" "ssh" {
  name                = "ssh"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  security_rule {
    access                     = "Allow"
    direction                  = "Inbound"
    name                       = "ssh"
    priority                   = 100
    protocol                   = "Tcp"
    source_port_range          = "*"
    source_address_prefix      = "*"
    destination_port_range     = "22"
    destination_address_prefix = azurerm_network_interface.main.private_ip_address
  }
}
resource "azurerm_network_interface_security_group_association" "main" {
  network_interface_id      = azurerm_network_interface.main.id
  network_security_group_id = azurerm_network_security_group.ssh.id
}
resource "azurerm_linux_virtual_machine" "main" {
  name                            = "${var.prefix}-vm"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = "Standard_F2"
  admin_username                  = "simon"
  network_interface_ids = [
    azurerm_network_interface.main.id,
    azurerm_network_interface.internal.id,
  ]
  admin_ssh_key {
    username   = "simon"
    public_key = file("<CHEMIN_PUB_KEY>")
  }
  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
  os_disk {
    storage_account_type = "Standard_LRS"
    caching              = "ReadWrite"
  }
}
resource "azurerm_network_interface" "internal2" {
  name                = "${var.prefix}-nic3"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "internal2"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}
resource "azurerm_linux_virtual_machine" "main2" {
  name                            = "${var.prefix}-vm2"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = "Standard_F2"
  admin_username                  = "simon"
  network_interface_ids = [
    azurerm_network_interface.internal2.id,
  ]
  admin_ssh_key {
    username   = "simon"
    public_key = file("<CHEMIN_PUB_KEY>")
  }
  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
  os_disk {
    storage_account_type = "Standard_LRS"
    caching              = "ReadWrite"
  }
}
```

- ssh -J simon@108.143.55.123 simon@10.0.2.6

On fait un ptit `ip a` depuis la VM2 histoire de s’assurer que c’est bien elle 
    
```
    - ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 7c:1e:52:5d:61:15 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.6/24 metric 100 brd 10.0.2.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::7e1e:52ff:fe5d:6115/64 scope link 
       valid_lft forever preferred_lft forever
```

IT'S WORK, WE DID IT !!!!

**4. Cloud init**

Dans cette partie il faut qu'on déploie un VM avec un fichier de conf cloud-init qui va permettre l'installation de docker directement au premier boot.

cloud-init.txt : 
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
      - RSA_PUB_KEY

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
```

main.tf: 

```
provider "azurerm" {
  features {}
  subscription_id = "<SUBSCRIPTION_ID>"
}
resource "azurerm_resource_group" "main" {
  name     = "${var.prefix}-resources"
  location = var.location
}
resource "azurerm_virtual_network" "main" {
  name                = "${var.prefix}-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
}
resource "azurerm_subnet" "internal" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}
resource "azurerm_public_ip" "pip" {
  name                = "${var.prefix}-pip"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  allocation_method   = "Static"
}
resource "azurerm_network_interface" "main" {
  name                = "${var.prefix}-nic1"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "primary"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.pip.id
  }
}
resource "azurerm_network_interface" "internal" {
  name                = "${var.prefix}-nic2"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}
resource "azurerm_network_security_group" "ssh" {
  name                = "ssh"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  security_rule {
    access                     = "Allow"
    direction                  = "Inbound"
    name                       = "ssh"
    priority                   = 100
    protocol                   = "Tcp"
    source_port_range          = "*"
    source_address_prefix      = "*"
    destination_port_range     = "22"
    destination_address_prefix = azurerm_network_interface.main.private_ip_address
  }
}
resource "azurerm_network_interface_security_group_association" "main" {
  network_interface_id      = azurerm_network_interface.main.id
  network_security_group_id = azurerm_network_security_group.ssh.id
}
resource "azurerm_linux_virtual_machine" "main" {
  name                            = "${var.prefix}-vm"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = "Standard_F2"
  admin_username                  = "simon"
  network_interface_ids = [
    azurerm_network_interface.main.id,
    azurerm_network_interface.internal.id,
  ]
  admin_ssh_key {
    username   = "simon"
    public_key = file("<CHEMIN_PUB_KEY>")
  }
  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
  os_disk {
    storage_account_type = "Standard_LRS"
    caching              = "ReadWrite"
  }
  custom_data = base64encode(file("cloud-init.txt"))
}
```
cloud-init status
status: done

docker -v
Docker version 28.0.4, build b8034c0
