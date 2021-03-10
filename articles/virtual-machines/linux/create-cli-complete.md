---
title: Tworzenie środowiska z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Tworzenie magazynu, maszyny wirtualnej z systemem Linux, sieci wirtualnej i podsieci, modułu równoważenia obciążenia, karty sieciowej, publicznego adresu IP i sieciowej grupy zabezpieczeń, a wszystko to od podstaw przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: ff86651d56abe090ca08c508a220362f9a011a3f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554704"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Tworzenie kompletnej maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
Aby szybko utworzyć maszynę wirtualną (VM) na platformie Azure, możesz użyć jednego polecenia platformy Azure, które korzysta z wartości domyślnych, aby utworzyć wymagane zasoby pomocnicze. Zasoby, takie jak sieć wirtualna, publiczny adres IP i reguły sieciowej grupy zabezpieczeń, są tworzone automatycznie. Aby uzyskać większą kontrolę nad środowiskiem w środowisku produkcyjnym, możesz utworzyć te zasoby przed czasem, a następnie dodać do nich maszyny wirtualne. W tym artykule opisano sposób tworzenia maszyny wirtualnej i wszystkich zasobów pomocniczych.

Upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się na koncie platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy *parametrów obejmują:* *MyVnet*, i *myVM*.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną i obsługiwać zasoby sieci wirtualnej. Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Domyślnie dane wyjściowe poleceń interfejsu wiersza polecenia platformy Azure są w formacie JSON (JavaScript Object Notation). Aby zmienić domyślne dane wyjściowe na listę lub tabelę, na przykład użyj polecenia [AZ Configure--Output](/cli/azure/reference-index). Możesz również dodać `--output` do dowolnego polecenia dla jednorazowej zmiany w formacie danych wyjściowych. Poniższy przykład przedstawia dane wyjściowe JSON z `az group create` polecenia:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci
Następnie utworzysz sieć wirtualną na platformie Azure i podsieć, w której można tworzyć maszyny wirtualne. Użyj [AZ Network VNET Create](/cli/azure/network/vnet) , aby utworzyć sieć wirtualną o nazwie *myVnet* z prefiksem adresu *192.168.0.0/16* . Należy również dodać *podsieć* o nazwie Moja Subnet z prefiksem adresu *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Dane wyjściowe pokazują, że podsieć została utworzona logicznie wewnątrz sieci wirtualnej:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Teraz Utwórz publiczny adres IP za pomocą [AZ Network Public-IP Create](/cli/azure/network/public-ip). Ten publiczny adres IP umożliwia nawiązanie połączenia z maszynami wirtualnymi za pośrednictwem Internetu. Ponieważ domyślny adres jest dynamiczny, należy utworzyć nazwany wpis DNS z `--domain-name-label` parametrem. Poniższy przykład tworzy publiczny adres IP o nazwie *myPublicIP* z nazwą DNS *mypublicdns*. Ponieważ nazwa DNS musi być unikatowa, podaj własną unikatową nazwę DNS:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Dane wyjściowe:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Aby kontrolować przepływ ruchu do i z maszyn wirtualnych, należy zastosować sieciową grupę zabezpieczeń do wirtualnej karty sieciowej lub podsieci. Poniższy przykład używa [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg) , aby utworzyć sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Należy zdefiniować reguły zezwalające na określony ruch lub go odmówić. Aby zezwolić na połączenia przychodzące na porcie 22 (aby włączyć dostęp SSH), Utwórz regułę ruchu przychodzącego za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Aby zezwolić na połączenia przychodzące na porcie 80 (dla ruchu internetowego), Dodaj inną regułę sieciowej grupy zabezpieczeń. Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Przejrzyj sieciową grupę zabezpieczeń i reguły za pomocą [AZ Network sieciowej grupy zabezpieczeń show](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Dane wyjściowe:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Tworzenie wirtualnej karty sieciowej
Karty interfejsu sieci wirtualnej są dostępne programowo, ponieważ można stosować reguły do ich używania. W zależności od [rozmiaru maszyny](../sizes.md)wirtualnej można dołączyć wiele wirtualnych kart sieciowych do maszyny wirtualnej. W poniższym poleceniu [AZ Network nic Create Utwórz](/cli/azure/network/nic) kartę sieciową o nazwie *myNic* i skojarz ją z sieciową grupą zabezpieczeń. Publiczny adres IP *myPublicIP* jest również skojarzony z wirtualną kartą sieciową.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Dane wyjściowe:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Zestawy dostępności ułatwiają rozmieszczanie maszyn wirtualnych w domenach błędów i domenach aktualizacji. Mimo że obecnie tworzysz tylko jedną maszynę wirtualną, najlepszym rozwiązaniem jest użycie zestawów dostępności, aby ułatwić rozszerzanie w przyszłości. 

Domeny błędów definiują grupowanie maszyn wirtualnych, które współużytkują wspólne źródło i przełącznik sieciowy. Domyślnie maszyny wirtualne skonfigurowane w ramach zestawu dostępności są oddzielane do trzech domen błędów. Problem sprzętowy w jednej z tych domen błędów nie ma wpływu na każdą maszynę wirtualną, na której działa aplikacja.

Domeny aktualizacji wskazują grupy maszyn wirtualnych i bazowego sprzętu fizycznego, które mogą być ponownie uruchamiane w tym samym czasie. Podczas planowanej konserwacji kolejność, w której domeny aktualizacji są ponownie uruchamiane, może nie być sekwencyjna, ale tylko jedna domena aktualizacji jest uruchamiana ponownie.

Platforma Azure automatycznie dystrybuuje maszyny wirtualne w domenach błędów i aktualizacji, umieszczając je w zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../availability.md).

Utwórz zestaw dostępności dla maszyny wirtualnej za pomocą [AZ VM Availability-Set Create](/cli/azure/vm/availability-set). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Informacje wyjściowe są domenami błędów i domenami aktualizacji:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
Utworzono zasoby sieciowe do obsługi maszyn wirtualnych dostępnych dla Internetu. Teraz Utwórz maszynę wirtualną i Zabezpiecz ją przy użyciu klucza SSH. W tym przykładzie utworzyszmy maszynę wirtualną Ubuntu w oparciu o najnowszą LTS. Dodatkowe obrazy można znaleźć za pomocą programu [AZ VM Image list](/cli/azure/vm/image), zgodnie z opisem w temacie [Znajdowanie obrazów maszyn wirtualnych platformy Azure](cli-ps-findimage.md).

Określ klucz SSH, który ma być używany do uwierzytelniania. Jeśli nie masz pary kluczy publicznych SSH, możesz [je utworzyć](mac-create-ssh-keys.md) lub użyć `--generate-ssh-keys` parametru, aby je utworzyć. Jeśli masz już parę kluczy, ten parametr używa istniejących kluczy w `~/.ssh` .

Utwórz maszynę wirtualną, przenosząc wszystkie zasoby i informacje za pomocą polecenia [AZ VM Create](/cli/azure/vm) . Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną wirtualną przy użyciu wpisu DNS podanego podczas tworzenia publicznego adresu IP. Ta `fqdn` wartość jest wyświetlana w danych wyjściowych podczas tworzenia maszyny wirtualnej:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Dane wyjściowe:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Możesz zainstalować NGINX i zobaczyć przepływ ruchu do maszyny wirtualnej. Zainstaluj program NGINX w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Aby wyświetlić domyślną lokację NGINX w działaniu, Otwórz przeglądarkę internetową i wprowadź nazwę FQDN:

![Domyślna witryna NGINX na maszynie wirtualnej](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Eksportowanie jako szablon
Co zrobić, jeśli chcesz teraz utworzyć dodatkowe środowisko programistyczne z tymi samymi parametrami lub środowiskiem produkcyjnym, które je dopasowuje? Menedżer zasobów używa szablonów JSON, które definiują wszystkie parametry środowiska. Wszystkie środowiska można tworzyć, odwołując się do tego szablonu JSON. [Szablony JSON można kompilować ręcznie](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) lub wyeksportować istniejące środowisko, aby utworzyć szablon JSON. Użyj [AZ Group Export](/cli/azure/group) , aby wyeksportować grupę zasobów w następujący sposób:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

To polecenie tworzy `myResourceGroup.json` plik w bieżącym katalogu roboczym. Po utworzeniu środowiska na podstawie tego szablonu zostanie wyświetlony monit o podanie wszystkich nazw zasobów. Można wypełnić te nazwy w pliku szablonu, dodając `--include-parameter-default-value` parametr do `az group export` polecenia. Edytuj szablon JSON, aby określić nazwy zasobów, lub [utwórz parameters.jsw pliku](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) , który określa nazwy zasobów.

Aby utworzyć środowisko na podstawie szablonu, użyj [AZ Deployment Group Create](/cli/azure/deployment/group) w następujący sposób:

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Warto zapoznać się z artykułem [więcej na temat wdrażania z szablonów](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). Dowiedz się, jak stopniowo aktualizować środowiska, korzystać z pliku parametrów i uzyskiwać dostęp do szablonów z pojedynczej lokalizacji magazynu.

## <a name="next-steps"></a>Następne kroki
Teraz wszystko jest gotowe do rozpoczęcia pracy z wieloma składnikami sieciowymi i maszynami wirtualnymi. Możesz użyć tego przykładowego środowiska do kompilowania aplikacji przy użyciu najważniejszych składników wprowadzonych w tym miejscu.