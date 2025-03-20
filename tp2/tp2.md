##tp2 

##I. Premiers pas

🌞 Créez une VM depuis le Azure CLI

```

az vm create -g necrosus -n super_vm --image Ubuntu2204 --admin-username hoplite --ssh-key-values ~/.ssh/
id_rsa.pub
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/94719bb8-61a7-4449-907e-25d1702731ee/resourceGroups/necrosus/providers/Microsoft.Compute/virtualMachines/super_vm",
  "location": "francecentral",
  "macAddress": "00-22-48-3A-18-16",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "4.233.102.203",
  "resourceGroup": "necrosus",
  "zones": ""
}

```

🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique

```
ssh hoplite@4.233.102.203
The authenticity of host '4.233.102.203 (4.233.102.203)' can't be established.
ED25519 key fingerprint is SHA256:sJ2cfT4nEL32cUftx+6tYwAoGgQ5HrVqz+dz4AH7vnQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '4.233.102.203' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1021-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Mar 18 08:49:50 UTC 2025

  System load:  0.81              Processes:             108
  Usage of /:   5.2% of 28.89GB   Users logged in:       0
  Memory usage: 8%                IPv4 address for eth0: 10.0.0.4
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

verification presence walinuxagent:

```
systemctl status walinuxagent
 walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-03-18 08:48:37 UTC; 1min 46s ago
   Main PID: 733 (python3)
      Tasks: 6 (limit: 4023)
     Memory: 40.6M
        CPU: 1.638s
     CGroup: /system.slice/walinuxagent.service
             ├─ 733 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1257 python3 -u bin/WALinuxAgent-2.12.0.2-py3.9.egg -run-exthandlers

Mar 18 08:48:46 supervm python3[1257]:     pkts      bytes target     prot opt in     out     source               dest>
Mar 18 08:48:46 supervm python3[1257]:        0        0 ACCEPT     tcp  --  *      *       0.0.0.0/0            168.63>
Mar 18 08:48:46 supervm python3[1257]:       80    11164 ACCEPT     tcp  --  *      *       0.0.0.0/0            168.63>
Mar 18 08:48:46 supervm python3[1257]:        0        0 DROP       tcp  --  *      *       0.0.0.0/0            168.63>
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.010909Z INFO ExtHandler ExtHandler
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.011048Z INFO ExtHandler ExtHandler ProcessExtensionsGoalStat>
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.011640Z INFO ExtHandler ExtHandler No extension handlers fou>
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.012397Z INFO ExtHandler ExtHandler ProcessExtensionsGoalStat>
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.034578Z INFO ExtHandler ExtHandler Looking for existing remo>
Mar 18 08:48:46 supervm python3[1257]: 2025-03-18T08:48:46.042944Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinux>
```
verification presence cloud-init:

```
$ cloud-init status
status: done
```

II. Un ptit LAN

🌞 Créez deux VMs depuis le Azure CLI


vm1 ip : 
```
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 60:45:bd:6c:c2:00 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.4/24 metric 100 brd 10.0.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::6245:bdff:fe6c:c200/64 scope link
       valid_lft forever preferred_lft forever
```

ping vm1 vers vm2

```
ping 10.0.0.5
PING 10.0.0.5 (10.0.0.5) 56(84) bytes of data.
64 bytes from 10.0.0.5: icmp_seq=1 ttl=64 time=1.21 ms
64 bytes from 10.0.0.5: icmp_seq=2 ttl=64 time=0.867 ms
64 bytes from 10.0.0.5: icmp_seq=3 ttl=64 time=1.16 ms
64 bytes from 10.0.0.5: icmp_seq=4 ttl=64 time=1.06 ms
64 bytes from 10.0.0.5: icmp_seq=5 ttl=64 time=4.06 ms
64 bytes from 10.0.0.5: icmp_seq=6 ttl=64 time=1.46 ms
64 bytes from 10.0.0.5: icmp_seq=7 ttl=64 time=0.820 ms

--- 10.0.0.5 ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6068ms
rtt min/avg/max/mdev = 0.820/1.518/4.062/1.057 ms

```
ip vm2

```
hoplite@vm2:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0d:3a:95:7e:41 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.5/24 metric 100 brd 10.0.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::20d:3aff:fe95:7e41/64 scope link
       valid_lft forever preferred_lft forever
```

ping vm2 vers vm1

```
hoplite@vm2:~$ ping 10.0.0.4
PING 10.0.0.4 (10.0.0.4) 56(84) bytes of data.
64 bytes from 10.0.0.4: icmp_seq=1 ttl=64 time=2.20 ms
64 bytes from 10.0.0.4: icmp_seq=2 ttl=64 time=0.944 ms
64 bytes from 10.0.0.4: icmp_seq=3 ttl=64 time=1.12 ms
64 bytes from 10.0.0.4: icmp_seq=4 ttl=64 time=0.939 ms
64 bytes from 10.0.0.4: icmp_seq=5 ttl=64 time=1.19 ms

--- 10.0.0.4 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 0.939/1.277/2.198/0.470 ms
```

##Part II : cloud-init

🌞 Tester cloud-init

```
 az vm create -g necrosus -n cloudinit_vm --image Ubuntu2204 --admin-username hoplite --ssh-key-values
 C:\Users\max16\.ssh\id_rsa.pub --custom-data "C:\Users\max16\cloud-init.txt"
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/94719bb8-61a7-4449-907e-25d1702731ee/resourceGroups/necrosus/providers/Microsoft.Compute/virtualMachines/cloudinit_vm",
  "location": "francecentral",
  "macAddress": "60-45-BD-6E-43-9B",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "4.211.171.157",
  "resourceGroup": "necrosus",
  "zones": ""
}
```

🌞 Vérifier que cloud-init a bien fonctionné

```
 ssh hoplite@4.211.171.157
The authenticity of host '4.211.171.157 (4.211.171.157)' can't be established.
ED25519 key fingerprint is SHA256:tntWhv/xc8ObH3nQUqTBcNrnbkcwIZ/+/cNyXikVuok.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '4.211.171.157' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1021-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Mar 18 09:54:20 UTC 2025

  System load:  0.08              Processes:             108
  Usage of /:   5.2% of 28.89GB   Users logged in:       0
  Memory usage: 8%                IPv4 address for eth0: 10.0.0.4
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
cloud-init status
status: done
hoplite@cloudinitvm:~$ id hoplite
uid=1000(hoplite) gid=1000(hoplite) groups=1000(hoplite),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),119(netdev),120(lxd)
hoplite@cloudinitvm:~$ grep hoplite /etc/passwd
hoplite:x:1000:1000:Ubuntu:/home/hoplite:/bin/bash
```

🌞 Utilisez cloud-init pour préconfigurer la VM :

cloud-init-docker.yaml 

```
#cloud-config
users:
  - name: maxuser
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    shell: /bin/bash
    lock_passwd: false
    passwd: "$6$rounds=656000$3K7UhGruUznCyJQf$8LxYRNST4l8girrsOnRm6.59l7Ei5OofUQabozCaml4ZLdX/bfYw3vjQ9rBwYnwrv9tHQirhZHR8.xOcJMyT/"
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC8F0HLPMYkVooFh61pQWa3BtCg/FR/iij1DO9aGPKWdbhh3Zhik3aVUJh5xIApEbLHdHcQmi5MX2Bn4S3OtGL4fKG0CtTUUZ0IZSzc5ZLNu30EMW97og+ZWIKArS9SZF7cLU21KyXo4QXGyNFMChOO66BZIp2ouyEYP38FbPRCeeFLQ1K5dPr5y+R4NpgiWNBtayHWTiTvPvmpCbvsYO6oU+bzsPr8fMJ3m6ijUfof7APF8WEibe3429WlhlzSAvzRAeyjHzRZXDbCc8Ao4/pk2zAk0WUvUJ7smx/gdEUQgPVtaTEgkbJDA5OxU7gm+F04ZQrRX8mmnLJyQ4HRub/VRAeOCBVdmWymsUnB9LzcKL1I7tVa05VVjodpw2JtIOgfxGpo/HuxsCagfEzwGeomcBfiirguN9lMbki/fV17o+nJoyQo+Nqy6k+zXl/Hk28WqiKI+mhjZ2SurxvvSZULbB5SKeCz8Wu1+EcYXY1xfpaEO2SnOUjj2gSoqRXdzp10nBa29ykONxDmHAr4RCVSJvVKgj2rySJ8sN+cbRaXFMSV22VhqIg7o3BL1NIlKEEIsnvgjXI/fy7eRrinpQwqtOkuv5WT7BOhxnZIGU/T1YJ0afnOrrh7s5loJpygn12+bqqoTsh3Rup4yyX4piyQS7Vy7uwMXoTcOJzRBhsxDQ== max16@necrosus"
    groups: [docker]

package_update: true
package_upgrade: true

packages:
  - docker.io

runcmd:
  - systemctl start docker
  - systemctl enable docker
  - docker pull alpine:latest


 az vm create -g necrosus2 -n cloudinit_vm_docker --image Ubuntu2204 --admin-username hoplite --ssh-key-values "C:\Users\max16\.ssh\id_rsa.pub" --custom-data "C:\Users\max16\cloud-init-docker.yaml"
```

verification:
```
maxuser@cloudinitvmdocker:~$ id maxuser

uid=1000(maxuser) gid=1001(maxuser) groups=1001(maxuser),1000(docker)

maxuser@cloudinitvmdocker:~$ grep maxuser /etc/passwd

maxuser:x:1000:1001::/home/maxuser:/bin/bash

maxuser@cloudinitvmdocker:~$ sudo grep maxuser /etc/shadow

maxuser:$6$rounds=656000$3K7UhGruUznCyJQf$8LxYRNST4l8girrsOnRm6.59l7Ei5OofUQabozCaml4ZLdX/bfYw3vjQ9rBwYnwrv9tHQirhZHR8.xOcJMyT/:20165:0:99999:7:::

maxuser@cloudinitvmdocker:~$ cat ~/.ssh/authorized_keys

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC8F0HLPMYkVooFh61pQWa3BtCg/FR/iij1DO9aGPKWdbhh3Zhik3aVUJh5xIApEbLHdHcQmi5MX2Bn4S3OtGL4fKG0CtTUUZ0IZSzc5ZLNu30EMW97og+ZWIKArS9SZF7cLU21KyXo4QXGyNFMChOO66BZIp2ouyEYP38FbPRCeeFLQ1K5dPr5y+R4NpgiWNBtayHWTiTvPvmpCbvsYO6oU+bzsPr8fMJ3m6ijUfof7APF8WEibe3429WlhlzSAvzRAeyjHzRZXDbCc8Ao4/pk2zAk0WUvUJ7smx/gdEUQgPVtaTEgkbJDA5OxU7gm+F04ZQrRX8mmnLJyQ4HRub/VRAeOCBVdmWymsUnB9LzcKL1I7tVa05VVjodpw2JtIOgfxGpo/HuxsCagfEzwGeomcBfiirguN9lMbki/fV17o+nJoyQo+Nqy6k+zXl/Hk28WqiKI+mhjZ2SurxvvSZULbB5SKeCz8Wu1+EcYXY1xfpaEO2SnOUjj2gSoqRXdzp10nBa29ykONxDmHAr4RCVSJvVKgj2rySJ8sN+cbRaXFMSV22VhqIg7o3BL1NIlKEEIsnvgjXI/fy7eRrinpQwqtOkuv5WT7BOhxnZIGU/T1YJ0afnOrrh7s5loJpygn12+bqqoTsh3Rup4yyX4piyQS7Vy7uwMXoTcOJzRBhsxDQ== max16@necrosus"

maxuser@cloudinitvmdocker:~$ sudo -l
Matching Defaults entries for maxuser on cloudinitvmdocker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User maxuser may run the following commands on cloudinitvmdocker:
    (ALL) NOPASSWD: ALL

maxuser@cloudinitvmdocker:~$ id maxuser
uid=1000(maxuser) gid=1001(maxuser) groups=1001(maxuser),1000(docker)

maxuser@cloudinitvmdocker:~$ docker --version

systemctl status docker
Docker version 26.1.3, build 26.1.3-0ubuntu1~22.04.1
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-03-18 10:51:46 UTC; 1min 8s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 7743 (dockerd)
      Tasks: 10
     Memory: 43.6M
        CPU: 638ms
     CGroup: /system.slice/docker.service
             └─7743 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

maxuser@cloudinitvmdocker:~$ docker images | grep alpine
alpine       latest    aded1e1a5b37   4 weeks ago   7.83MB
```
##*Part III : Terraform

🌞 Constater le déploiement
```
C:\Users\max16\terraform-azure>az group list -o table
Name                    Location    Status
----------------------  ----------  ---------
tp1                     westus      Succeeded
NetworkWatcherRG        westus      Succeeded
tp2_magueule-resources  westeurope  Succeeded
```
```
C:\Users\max16\terraform-azure>az vm list -o table
Name             ResourceGroup           Location    Zones
---------------  ----------------------  ----------  -------
tp2_magueule-vm  TP2_MAGUEULE-RESOURCES  westeurope

>az vm show --name tp2_magueule-vm --resource-group tp2_magueule-resources -o table
Name             ResourceGroup           Location    Zones
---------------  ----------------------  ----------  -------
tp2_magueule-vm  tp2_magueule-resources  westeurope
```

🌞 Créer un plan Terraform avec les contraintes suivantes

```
provider "azurerm" {
  features {}
  subscription_id = "id de votre compte azure"
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.prefix}-resources"
  location = var.location
}

resource "azurerm_virtual_network" "vnet" {
  name                = "${var.prefix}-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet" {
  name                 = "${var.prefix}-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_public_ip" "node1_pip" {
  name                = "${var.prefix}-node1-pip"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  allocation_method   = "Static"
  sku                 = "Standard"
}

resource "azurerm_network_interface" "node1_nic" {
  name                = "${var.prefix}-node1-nic"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location

  ip_configuration {
    name                          = "node1-ipconfig"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.node1_pip.id
  }
}

resource "azurerm_network_interface" "node2_nic" {
  name                = "${var.prefix}-node2-nic"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location

  ip_configuration {
    name                          = "node2-ipconfig"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_network_security_group" "nsg" {
  name                = "${var.prefix}-nsg"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location

  security_rule {
    name                       = "allow-icmp"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Icmp"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
    source_port_range          = "*"
    destination_port_range     = "*"
  }

  security_rule {
    name                       = "allow-ssh"
    priority                   = 110
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
    source_port_range          = "*"
    destination_port_range     = "22"
  }
}

resource "azurerm_network_interface_security_group_association" "node1_assoc" {
  network_interface_id      = azurerm_network_interface.node1_nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}

resource "azurerm_network_interface_security_group_association" "node2_assoc" {
  network_interface_id      = azurerm_network_interface.node2_nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}

resource "azurerm_linux_virtual_machine" "node1" {
  name                = "${var.prefix}-node1-vm"
  computer_name       = "${var.prefix}-node1"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  size                = "Standard_B1s"
  admin_username      = var.admin_username
  disable_password_authentication = true

  network_interface_ids = [
    azurerm_network_interface.node1_nic.id,
  ]

  admin_ssh_key {
    username   = var.admin_username
    public_key = file(var.ssh_public_key_path)
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

resource "azurerm_linux_virtual_machine" "node2" {
  name                = "${var.prefix}-node2-vm"
  computer_name       = "${var.prefix}-node2"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  size                = "Standard_B1s"
  admin_username      = var.admin_username
  disable_password_authentication = true

  network_interface_ids = [
    azurerm_network_interface.node2_nic.id,
  ]

  admin_ssh_key {
    username   = var.admin_username
    public_key = file(var.ssh_public_key_path)
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


variable "prefix" {
  description = "Préfixe pour nommer les ressources."
  default     = "tomate"
}

variable "location" {
  description = "Emplacement pour les ressources Azure"
  default     = "westeurope"
}

variable "admin_username" {
  description = "Nom d'utilisateur administrateur pour les VM"
  default     = "hoplite"
}

variable "ssh_public_key_path" {
  description = "Chemin vers votre clé SSH publique"
  default     = "C:/Users/max16/.ssh/id_rsa.pub"
}



C:\Users\max16\terraform_plan>ssh -J hoplite@20.234.147.208 hoplite@10.0.1.4

>ssh -J hoplite@20.234.147.208 hoplite@10.0.1.4
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1021-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Mar 18 14:21:09 UTC 2025

  System load:  0.2               Processes:             113
  Usage of /:   5.2% of 28.89GB   Users logged in:       0
  Memory usage: 29%               IPv4 address for eth0: 10.0.1.4
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '24.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Tue Mar 18 14:21:11 2025 from 10.0.1.5
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

hoplite@tomate-node2:~$
```
🌞 Intégrer la gestion de cloud-init
```
main.tf :
provider "azurerm" {
  features {}
  subscription_id = "id-abonnement-azure"
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
resource "azurerm_public_ip" "node1_pip" {
  name                = "${var.prefix}-node1-pip"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  allocation_method   = "Static"
}
resource "azurerm_network_interface" "node1_nic" {
  name                = "${var.prefix}-node1-nic1"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "primary"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.node1_pip.id
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
    destination_address_prefix = "*"
  }
}
resource "azurerm_network_interface_security_group_association" "node1_assoc" {
  network_interface_id      = azurerm_network_interface.node1_nic.id
  network_security_group_id = azurerm_network_security_group.ssh.id
}
resource "azurerm_linux_virtual_machine" "node1" {
  name                            = "${var.prefix}-node1"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = "Standard_F2"
  admin_username                  = "hoplite"
  network_interface_ids = [
    azurerm_network_interface.node1_nic.id
  ]
  admin_ssh_key {
    username   = "mierukey"
    public_key = file("C:\\Users\\max16\\.ssh\\id_rsa.pub")
  }
  custom_data = base64encode(file("..\\cloud-init.txt"))
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
cloud-init.txt :
```
#cloud-config
users:
  - name: mierukinit
    sudo:
      - ALL=(ALL) NOPASSWD:ALL
    lock_passwd: false
    passwd: "$6$rounds=656000$lCrdXDksMuxSdpxM$EMoIYI3TbFhw8yVxdK8nZhkNrQUPUjji2xLoqbWGh50BCoEn/NqXclvDWcXnSrfsXnOy7kSLfbZIccDoTjMSu/"
    shell: /bin/bash
    ssh_authorized_keys:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCjJx4EyHLlx7A5UqSu56YOXzMp5Tg8uIr+agmVGjzh1j6Gj9msUQfS2p1/jaGHbktXOjPdzIH/hJcU7B4+OXdHqqfSickOY/EvqNqRkhUnN2XSESgUCUDA6RzgvdZDPXqHGYH8P1uAHnuTajz+4XaVs8YqZE2THlkEpdH0cEeaR3pyw5cFfigOjYUVR62EPMye/jn3/nltC21GMBc+u0ua4CJI+GMIuLh/XO84sVO4s4Y/PqMaiaDlGj6NYdG7rZqDn5Be7B/ZMVeqeEsugHjAIil/mc1oSlLm6UjNwHlT/BCHTq0JkULhmLA///CHp8O/NuD1h3XaDJuOe6Tl+Mwp"
    


runcmd:
  - apt-get update
  - apt-get install ca-certificates curl
  - install -m 0755 -d /etc/apt/keyrings
  - curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
  - chmod a+r /etc/apt/keyrings/docker.asc
  - echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  - apt-get update
  - apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  - usermod -aG docker mierukinit
  - docker pull alpine:latest
```
🌞 Moar cloud-init and Terraform configuration : Proof !
main.tf :
```
provider "azurerm" {
  features {}
  subscription_id = "id-abonnement-azure"
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
resource "azurerm_public_ip" "node1_pip" {
  name                = "${var.prefix}-node1-pip"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  allocation_method   = "Static"
}
resource "azurerm_network_interface" "node1_nic" {
  name                = "${var.prefix}-node1-nic1"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  ip_configuration {
    name                          = "primary"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.node1_pip.id
  }
}
resource "azurerm_network_security_group" "nsg" {
  name                = "nsg"
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
    destination_address_prefix = "*"
  }
  security_rule {
    access                     = "Allow"
    direction                  = "Inbound"
    name                       = "wikijs"
    priority                   = 200
    protocol                   = "Tcp"
    source_port_range          = "*"
    source_address_prefix      = "*"
    destination_port_range     = "10101"
    destination_address_prefix = "*"
  }
}
resource "azurerm_network_interface_security_group_association" "node1_assoc" {
  network_interface_id      = azurerm_network_interface.node1_nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}
resource "azurerm_linux_virtual_machine" "node1" {
  name                            = "${var.prefix}-node1"
  resource_group_name             = azurerm_resource_group.main.name
  location                        = azurerm_resource_group.main.location
  size                            = "Standard_F2"
  admin_username                  = "hoplite"
  network_interface_ids = [
    azurerm_network_interface.node1_nic.id
  ]
  admin_ssh_key {
    username   = "mierukey"
    public_key = file("C:\\Users\\max16\\.ssh\\id_rsa.pub")
  }
  custom_data = base64encode(file("..\\cloud-init.txt"))
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

cloud-init.txt :
```
#cloud-config
users:
  - name: mierukinit
    sudo:
      - ALL=(ALL) NOPASSWD:ALL
    lock_passwd: false
    passwd: "$6$rounds=656000$lCrdXDksMuxSdpxM$EMoIYI3TbFhw8yVxdK8nZhkNrQUPUjji2xLoqbWGh50BCoEn/NqXclvDWcXnSrfsXnOy7kSLfbZIccDoTjMSu/"
    shell: /bin/bash
    ssh_authorized_keys:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCjJx4EyHLlx7A5UqSu56YOXzMp5Tg8uIr+agmVGjzh1j6Gj9msUQfS2p1/jaGHbktXOjPdzIH/hJcU7B4+OXdHqqfSickOY/EvqNqRkhUnN2XSESgUCUDA6RzgvdZDPXqHGYH8P1uAHnuTajz+4XaVs8YqZE2THlkEpdH0cEeaR3pyw5cFfigOjYUVR62EPMye/jn3/nltC21GMBc+u0ua4CJI+GMIuLh/XO84sVO4s4Y/PqMaiaDlGj6NYdG7rZqDn5Be7B/ZMVeqeEsugHjAIil/mc1oSlLm6UjNwHlT/BCHTq0JkULhmLA///CHp8O/NuD1h3XaDJuOe6Tl+Mwp"

write_files:
  - path: /opt/wikijs/docker-compose.yml
    content: |
      services:

        db:
          image: postgres:15-alpine
          environment:
            POSTGRES_DB: wiki
            POSTGRES_PASSWORD: wikijsrocks
            POSTGRES_USER: wikijs
          logging:
            driver: none
          restart: unless-stopped
          volumes:
            - db-data:/var/lib/postgresql/data

        wiki:
          image: ghcr.io/requarks/wiki:2
          depends_on:
            - db
          environment:
            DB_TYPE: postgres
            DB_HOST: db
            DB_PORT: 5432
            DB_USER: wikijs
            DB_PASS: wikijsrocks
            DB_NAME: wiki
          restart: unless-stopped
          ports:
            - "10101:3000"

      volumes:
        db-data:

runcmd:
  - apt-get update
  - apt-get install ca-certificates curl
  - install -m 0755 -d /etc/apt/keyrings
  - curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
  - chmod a+r /etc/apt/keyrings/docker.asc
  - echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  - apt-get update
  - apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  - usermod -aG docker mierukinit
  - docker pull alpine:latest
  - docker compose -f /opt/wikijs/docker-compose.yml up
```

curl du wikijs :
```
PS C:\Users\max16> curl http://20.61.77.155:10101/
<!DOCTYPE html><html><head><meta http-equiv="X-UA-Compatible" content="IE=edge"><meta charset="UTF-8"><meta name="viewport" content="user-scalable=yes, width=device-width, initial-scale=1, maximum-scale=5"><meta name="theme-color" content="#1976d2"><meta name="msapplication-TileColor" content="#1976d2"><meta name="msapplication-TileImage" content="/_assets/favicons/mstile-150x150.png"><title>Wiki.js Setup</title><link rel="apple-touch-icon" sizes="180x180" href="/_assets/favicons/apple-touch-icon.png"><link rel="icon" type="image/png" sizes="192x192" href="/_assets/favicons/android-chrome-192x192.png"><link rel="icon" type="image/png" sizes="32x32" href="/_assets/favicons/favicon-32x32.png"><link rel="icon" type="image/png" sizes="16x16" href="/_assets/favicons/favicon-16x16.png"><link rel="mask-icon" href="/_assets/favicons/safari-pinned-tab.svg" color="#1976d2"><link rel="manifest" href="/_assets/manifest.json"><script>var siteConfig = {"title":"Wiki.js"}
</script><link type="text/css" rel="stylesheet" href="/_assets/css/setup.22871ffac1b643eed4d9.css"><script type="text/javascript" src="/_assets/js/runtime.js?1738531300"></script><script type="text/javascript" src="/_assets/js/setup.js?1738531300"></script></head><body><div id="root"><setup wiki-version="2.5.306"></setup></div></body></html>
```
