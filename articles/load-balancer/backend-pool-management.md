---
title: Zarządzanie pulą zaplecza
titleSuffix: Azure Load Balancer
description: Wprowadzenie do konfigurowania puli zaplecza Azure Load Balancer i zarządzania nią
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: ac21e1f00dc2a5580b90a1a5eb43da05288e800a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489427"
---
# <a name="backend-pool-management"></a>Zarządzanie pulą zaplecza
Pula zaplecza jest krytycznym składnikiem modułu równoważenia obciążenia. Pula zaplecza definiuje grupę zasobów, która będzie obsługiwała ruch dla danej reguły równoważenia obciążenia.

Istnieją dwa sposoby konfigurowania puli zaplecza:
* Karta interfejsu sieciowego (NIC)
* Kombinacja adresu IP i identyfikatora zasobu Virtual Network (VNET)

Skonfiguruj pulę zaplecza przez kartę sieciową, gdy korzysta się z istniejących maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Ta metoda kompiluje najbardziej bezpośredni link między zasobem a pulą zaplecza. 

Podczas wstępnie przydzielania puli zaplecza z zakresem adresów IP, który planujesz później tworzyć maszyny wirtualne i zestawy skalowania maszyn wirtualnych, skonfiguruj pulę zaplecza według adresu IP i kombinacji identyfikatorów sieci wirtualnej.

Istnieje możliwość skonfigurowania pul zaplecza opartych na protokole IP i kart sieciowych dla tego samego modułu równoważenia obciążenia, ale nie można utworzyć pojedynczej puli zaplecza, która miesza adresy, które są używane przez karty sieciowe i adresy IP w tej samej puli.

Sekcje konfiguracji tego artykułu dotyczą:

* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure
* Interfejs API REST
* Szablony usługi Azure Resource Manager 

Te sekcje zawierają szczegółowe informacje o tym, jak pule zaplecza są strukturalne dla każdej opcji konfiguracji.

## <a name="configuring-backend-pool-by-nic"></a>Konfigurowanie puli zaplecza za pomocą karty sieciowej
Pula zaplecza jest tworzona w ramach operacji modułu równoważenia obciążenia. Właściwość Konfiguracja protokołu IP karty sieciowej służy do dodawania elementów członkowskich puli zaplecza.

Poniższe przykłady koncentrują się na operacjach tworzenia i wypełniania dla puli zaplecza w celu wyróżnienia tego przepływu pracy i relacji.

  >[!NOTE] 
  >Należy pamiętać, że pule zaplecza skonfigurowane za pośrednictwem interfejsu sieciowego nie mogą zostać zaktualizowane w ramach operacji w puli zaplecza. Dodawanie lub usuwanie zasobów zaplecza musi odbywać się w interfejsie sieciowym zasobu.

### <a name="powershell"></a>PowerShell
Utwórz nową pulę zaplecza:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Utwórz nowy interfejs sieciowy i dodaj go do puli zaplecza:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Pobierz informacje o puli zaplecza dla modułu równoważenia obciążenia, aby upewnić się, że ten interfejs sieciowy został dodany do puli zaplecza:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Utwórz nową maszynę wirtualną i Dołącz interfejs sieciowy, aby umieścić go w puli zaplecza:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Interfejs wiersza polecenia
Utwórz pulę zaplecza:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Utwórz nowy interfejs sieciowy i dodaj go do puli zaplecza:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Pobierz pulę zaplecza, aby potwierdzić, że adres IP został poprawnie dodany:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Utwórz nową maszynę wirtualną i Dołącz interfejs sieciowy, aby umieścić go w puli zaplecza:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="rest-api"></a>Interfejs API REST
Utwórz pulę zaplecza:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Utwórz interfejs sieciowy i dodaj go do puli zaplecza utworzonej za pomocą właściwości konfiguracja IP interfejsu sieciowego:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Treść żądania JSON:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
            }
          ]
        }
      }
    ]
  },
  "location": "eastus"
}
```

Pobierz informacje o puli zaplecza dla modułu równoważenia obciążenia, aby upewnić się, że ten interfejs sieciowy został dodany do puli zaplecza:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Utwórz maszynę wirtualną i Dołącz kartę sieciową, która odwołuje się do puli zaplecza:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Treść żądania JSON:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Postępuj zgodnie z tym [szablonem Menedżer zasobów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) , aby wdrożyć moduł równoważenia obciążenia i maszyny wirtualne oraz dodać maszyny wirtualne do puli zaplecza za pośrednictwem interfejsu sieciowego.

Postępuj zgodnie z tym [szablonem Menedżer zasobów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool) , aby wdrożyć moduł równoważenia obciążenia i maszyny wirtualne oraz dodać maszyny wirtualne do puli zaplecza za pomocą adresu IP.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Konfigurowanie puli zaplecza według adresu IP i sieci wirtualnej
W scenariuszach z wstępnie wypełnionymi pulami zaplecza Użyj protokołu IP i sieci wirtualnej.

Wszystkie zarządzanie pulą zaplecza odbywa się bezpośrednio w obiekcie puli zaplecza, jak wyróżniono w poniższych przykładach.

### <a name="limitations"></a>Ograniczenia
Pula zaplecza skonfigurowana przy użyciu adresu IP ma następujące ograniczenia:
  * Może być używany tylko dla usług równoważenia obciążenia w warstwie Standardowa
  * Limit 100 adresów IP w puli zaplecza
  * Zasoby zaplecza muszą znajdować się w tej samej sieci wirtualnej co moduł równoważenia obciążenia
  * Load Balancer z pulą zaplecza opartego na protokole IP nie może pełnić funkcji usługi linku prywatnego
  * Ta funkcja nie jest obecnie obsługiwana w Azure Portal
  * Kontenery ACI nie są obecnie obsługiwane przez tę funkcję
  * Moduły równoważenia obciążenia lub usługi frontonu nie mogą być umieszczane w puli zaplecza modułu równoważenia obciążenia
  * Nie można określać reguł NAT dla ruchu przychodzącego za pomocą adresu IP

### <a name="powershell"></a>PowerShell
Utwórz nową pulę zaplecza:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Zaktualizuj pulę zaplecza przy użyciu nowego adresu IP z istniejącej sieci wirtualnej:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Pobierz informacje o puli zaplecza dla modułu równoważenia obciążenia, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Utwórz interfejs sieciowy i dodaj go do puli zaplecza. Ustaw adres IP na jeden z adresów zaplecza:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Utwórz maszynę wirtualną i Dołącz kartę sieciową przy użyciu adresu IP w puli zaplecza:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Interfejs wiersza polecenia
Za pomocą interfejsu wiersza polecenia można wypełnić pulę zaplecza za pomocą parametrów wiersza poleceń lub pliku konfiguracji JSON. 

Utwórz i wypełnij pulę zaplecza za pomocą parametrów wiersza polecenia:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Utwórz i wypełnij pulę zaplecza za pomocą pliku konfiguracji JSON:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

Plik konfiguracji JSON:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Pobierz informacje o puli zaplecza dla modułu równoważenia obciążenia, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Utwórz interfejs sieciowy i dodaj go do puli zaplecza. Ustaw adres IP na jeden z adresów zaplecza:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Utwórz maszynę wirtualną i Dołącz kartę sieciową przy użyciu adresu IP w puli zaplecza:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>Interfejs API REST

Utwórz pulę zaplecza i zdefiniuj adresy zaplecza za pomocą żądania PUT puli zaplecza. Skonfiguruj adresy zaplecza w treści JSON żądania PUT przez:

* Nazwa adresu
* Adres IP
* Identyfikator sieci wirtualnej 

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

Treść żądania JSON:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

Pobierz informacje o puli zaplecza dla modułu równoważenia obciążenia, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Utwórz interfejs sieciowy i dodaj go do puli zaplecza. Ustaw adres IP na jeden z adresów zaplecza:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Treść żądania JSON:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Utwórz maszynę wirtualną i Dołącz kartę sieciową przy użyciu adresu IP w puli zaplecza:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Treść żądania JSON:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```
  
## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono informacje dotyczące zarządzania pulą zaplecza Azure Load Balancer oraz konfigurowania puli zaplecza przy użyciu adresu IP i sieci wirtualnej.

Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
