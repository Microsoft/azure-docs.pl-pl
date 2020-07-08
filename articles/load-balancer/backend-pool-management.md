---
title: Zarządzanie pulą zaplecza
description: Przewodnik konfigurowania puli zaplecza Load Balancer
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: overview
ms.date: 07/06/2020
ms.author: errobin
ms.openlocfilehash: 6d9700e134a9e3d6c53524d15c8b3503cf5773c7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050187"
---
# <a name="backend-pool-management"></a>Zarządzanie pulą zaplecza
Pula zaplecza jest podstawowym składnikiem Load Balancer, który definiuje grupę zasobów obliczeniowych, która będzie obsługiwała ruch dla danej reguły równoważenia obciążenia. W celu poprawnego skonfigurowania puli zaplecza należy zdefiniować grupę uprawnionych maszyn do obsługi ruchu. Istnieją dwa sposoby konfigurowania puli zaplecza, karty interfejsu sieciowego (NIC) i połączonego adresu IP i identyfikatora zasobu Virtual Network (VNET). 

W większości scenariuszy obejmujących Virtual Machines i Virtual Machine Scale Sets zaleca się skonfigurowanie puli zaplecza za pomocą karty sieciowej, ponieważ ta metoda kompiluje najbardziej bezpośredni link między zasobem a pulą zaplecza. W scenariuszach obejmujących kontenery i Kubernetesy, które nie mają karty sieciowej lub wstępnego przydzielania zakresu adresów IP zasobów zaplecza, można skonfigurować pulę zaplecza według adresu IP i kombinacji identyfikatorów sieci wirtualnej.

W przypadku konfigurowania przez kartę sieciową lub adres IP i identyfikator sieci wirtualnej za pomocą portalu interfejs użytkownika przeprowadzi Cię przez poszczególne kroki, a wszystkie aktualizacje konfiguracji będą obsługiwane w zapleczu. Sekcje konfiguracji tego artykułu koncentrują się na Azure PowerShell, interfejsie wiersza polecenia interfejsu API REST i szablonach ARM w celu uzyskania wglądu w sposób, w jaki pule zaplecza są strukturalne dla każdej opcji konfiguracji.

## <a name="configuring-backend-pool-by-nic"></a>Konfigurowanie puli zaplecza za pomocą karty sieciowej
Podczas konfigurowania puli zaplecza za pomocą karty sieciowej należy pamiętać, że Pula zaplecza jest tworzona jako część operacji Load Balancer i elementy członkowskie są dodawane do puli zaplecza w ramach właściwości konfiguracja IP interfejsu sieciowego podczas operacji interfejsu sieciowego. Poniższe przykłady koncentrują się na operacjach tworzenia i wypełniania dla puli zaplecza w celu wyróżnienia tego przepływu pracy i relacji.

  >[!NOTE] 
  >Należy pamiętać, że pule zaplecza skonfigurowane za pośrednictwem interfejsu sieciowego nie mogą zostać zaktualizowane w ramach operacji w puli zaplecza. Dodawanie lub usuwanie zasobów zaplecza musi odbywać się w interfejsie sieciowym zasobu.

### <a name="powershell"></a>PowerShell
Utwórz nową pulę zaplecza: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Utwórz nowy interfejs sieciowy i dodaj go do puli zaplecza:
```powershell
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -LoadBalancerBackendAddressPool $bepool -Subnet $vnet.Subnets[0]
```

Pobierz informacje o puli zaplecza dla Load Balancer, aby upewnić się, że ten interfejs sieciowy został dodany do puli zaplecza:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $bePool  
```

Utwórz nową maszynę wirtualną i Dołącz interfejs sieciowy, aby umieścić go w puli zaplecza:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```


  
### <a name="cli"></a>Interfejs wiersza polecenia
Utwórz pulę zaplecza:
```bash
az network lb address-pool create --resourceGroup myResourceGroup --lb-name myLB --name myBackendPool 
```

Utwórz nowy interfejs sieciowy i dodaj go do puli zaplecza:
```bash
az network nic create --resource-group myResourceGroup --name myNic --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup --lb-name myLB --lb-address-pools myBackEndPool
```

Pobierz pulę zaplecza, aby potwierdzić, że adres IP został poprawnie dodany:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Utwórz nową maszynę wirtualną i Dołącz interfejs sieciowy, aby umieścić go w puli zaplecza:
```bash
az vm create --resource-group myResourceGroup --name myVM --nics myNic --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
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
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Pobierz informacje o puli zaplecza dla Load Balancer, aby upewnić się, że ten interfejs sieciowy został dodany do puli zaplecza:

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

### <a name="arm-template"></a>Szablon ARM
Postępuj zgodnie z tym [szablonem szybki start ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) , aby wdrożyć Load Balancer i Virtual Machines i dodać Virtual Machines do puli zaplecza za pośrednictwem interfejsu sieciowego.

## <a name="configuring-backend-pool-by-ip-address-and-virtual-network"></a>Konfigurowanie puli zaplecza przy użyciu adresu IP i Virtual Network
W przypadku równoważenia obciążenia zasobów kontenera lub wstępnego wypełniania puli zaplecza z zakresem adresów IP można użyć adresu IP i Virtual Network do skierowania do dowolnego prawidłowego zasobu niezależnie od tego, czy ma on interfejs sieciowy. Podczas konfigurowania przy użyciu adresu IP i sieci wirtualnej wszystkie zarządzanie pulą zaplecza odbywa się bezpośrednio w obiekcie puli zaplecza, jak wyróżniono w poniższych przykładach.

  >[!IMPORTANT] 
  >Ta funkcja jest obecnie dostępna w wersji zapoznawczej i ma następujące ograniczenia:
  >* Limit liczby dodawanych adresów IP 100
  >* Zasoby zaplecza muszą znajdować się w tym samym Virtual Network co Load Balancer
  >* Ta funkcja nie jest obecnie obsługiwana w interfejsie użytkownika portalu
  >* Ta usługa jest dostępna tylko dla usługi równoważenia obciążenia w warstwie Standardowa
  
### <a name="powershell"></a>PowerShell
Utwórz nową pulę zaplecza: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPooName  
```

Zaktualizuj tę pulę zaplecza przy użyciu nowego adresu IP z istniejącej sieci wirtualnej:  
```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```

Pobierz informacje o puli zaplecza dla Load Balancer, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```
Utwórz interfejs sieciowy i dodaj go do puli zaplecza, ustawiając adres IP na jeden z adresów zaplecza:
```
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -PrivateIpAddress 10.0.0.4 -Subnet $vnet.Subnets[0]
```

Utwórz maszynę wirtualną i Dołącz kartę sieciową przy użyciu adresu IP w puli zaplecza:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Interfejs wiersza polecenia
Za pomocą interfejsu wiersza polecenia można wypełnić pulę zaplecza za pomocą parametrów wiersza poleceń lub pliku konfiguracji JSON. 

Utwórz i wypełnij pulę zaplecza za pomocą parametrów wiersza polecenia:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address name=addr1 ip-address=10.0.0.4 --backend-address name=addr2 ip-address=10.0.0.5
```

Utwórz i wypełnij pulę zaplecza za pomocą pliku konfiguracji JSON:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address-config-file @config_file.json
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

Pobierz informacje o puli zaplecza dla Load Balancer, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Utwórz interfejs sieciowy i dodaj go do puli zaplecza, ustawiając adres IP na jeden z adresów zaplecza:
```bash
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
```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>Interfejs API REST


Utwórz pulę zaplecza i zdefiniuj adresy zaplecza za pomocą żądania PUT puli zaplecza. Skonfiguruj adresy zaplecza, które chcesz dodać za pomocą nazwy adresu, adresu IP i identyfikatora Virtual Network w treści JSON żądania PUT:

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

Pobierz informacje o puli zaplecza dla Load Balancer, aby upewnić się, że adresy zaplecza są dodawane do puli zaplecza:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Utwórz interfejs sieciowy i dodaj go do puli zaplecza, ustawiając adres IP na jeden z adresów zaplecza:
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

### <a name="arm-template"></a>Szablon ARM
Utwórz Load Balancer, pulę zaplecza i wypełnij pulę zaplecza przy użyciu adresów zaplecza:
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_myLB_location": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_location_1": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location_1": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_name": {
            "defaultValue": "myLB",
            "type": "String"
        },
        "virtualNetworks_myVNET_externalid": {
            "defaultValue": "/subscriptions/6bb4a28a-db84-4e8a-b1dc-fabf7bd9f0b2/resourceGroups/ErRobin4/providers/Microsoft.Network/virtualNetworks/myVNET",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2020-04-01",
            "name": "[parameters('loadBalancers_myLB_name')]",
            "location": "eastus",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAddress": "10.0.0.7",
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(parameters('virtualNetworks_myVNET_externalid'), '/subnets/Subnet-1')]"
                            },
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                            "loadBalancerBackendAddresses": [
                                {
                                    "name": "addr1",
                                    "properties": {
                                        "ipAddress": "10.0.0.4",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location')]"
                                        }
                                    }
                                },
                                {
                                    "name": "addr2",
                                    "properties": {
                                        "ipAddress": "10.0.0.5",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location_1')]"
                                        }
                                    }
                                }
                            ]
                        }
                    }
                ],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundRules": [],
                "inboundNatPools": []
            }
        },
        {
            "type": "Microsoft.Network/loadBalancers/backendAddressPools",
            "apiVersion": "2020-04-01",
            "name": "[concat(parameters('loadBalancers_myLB_name'), '/myBackendPool')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLB_name'))]"
            ],
            "properties": {
                "loadBalancerBackendAddresses": [
                    {
                        "name": "addr1",
                        "properties": {
                            "ipAddress": "10.0.0.4",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location')]"
                            }
                        }
                    },
                    {
                        "name": "addr2",
                        "properties": {
                            "ipAddress": "10.0.0.5",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location_1')]"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

Utwórz maszynę wirtualną i podłączony interfejs sieciowy. Ustaw adres IP interfejsu sieciowego na jeden z adresów zaplecza:
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "String",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "storageAccountDomain": {
      "type": "String",
      "metadata": {
        "description": "The domain of the storage account to be created."
      }
    },
    "adminUsername": {
      "type": "String",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "SecureString",
      "metadata": {
        "description": "Admin password"
      }
    },
    "vmName": {
      "defaultValue": "myVM",
      "type": "String",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "String",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "defaultValue": "WindowsServer",
      "type": "String",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "defaultValue": "2012-R2-Datacenter",
      "type": "String",
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "defaultValue": "myLB",
      "type": "String",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicName": {
      "defaultValue": "nic",
      "type": "String",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "privateIpAddress": {
      "defaultValue": "10.0.0.5",
      "type": "String",
      "metadata": {
        "description": "Private IP Address of the VM"
      }
    },
    "vnetName": {
      "defaultValue": "myVNET",
      "type": "String",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "defaultValue": "Standard_A1",
      "type": "String",
      "metadata": {
        "description": "Size of the VM"
      }
    },
    "storageLocation": {
      "type": "String",
      "metadata": {
        "description": "Location of the Storage Account."
      }
    },
    "location": {
      "type": "String",
      "metadata": {
        "description": "Location to deploy all the resources in."
      }
    }
  },
  "variables": {
    "networkSecurityGroupName": "networkSecurityGroup1",
    "storageAccountType": "Standard_LRS",
    "subnetName": "Subnet-1",
    "publicIPAddressType": "Static",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('storageLocation')]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2016-03-30",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": []
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Static",
              "privateIpAddress": "[parameters('privateIpAddress')]",
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
        "[parameters('nicName')]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[parameters('imagePublisher')]",
            "offer": "[parameters('imageOffer')]",
            "sku": "[parameters('imageSKU')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',parameters('storageAccountName'),'.blob.',parameters('storageAccountDomain'),'/vhds/','osdisk', '.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
            }
          ]
        }
      }
    }
  ]
}
```
