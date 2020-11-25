---
title: Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure
description: Jak skonfigurować niektóre bardziej zaawansowane właściwości sieci dla zestawów skalowania maszyn wirtualnych platformy Azure.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 234834af4fcf4ad809f548d171a4c1c406d85895
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016696"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure

W przypadku wdrażania zestawu skalowania maszyn wirtualnych platformy Azure za pośrednictwem portalu, niektóre właściwości sieciowe są konfigurowane domyślnie, na przykład usługa Azure Load Balancer z regułami NAT dla ruchu przychodzącego. W tym artykule opisano, jak korzystać z niektórych bardziej zaawansowanych funkcji sieciowych konfigurowalnych za pomocą zestawów skalowania.

Wszystkie funkcje omówione w tym artykule można skonfigurować za pomocą szablonów usługi Azure Resource Manager. Dla wybranych funkcji dołączono też przykłady związane z interfejsem wiersza polecenia platformy Azure i programem PowerShell.

## <a name="accelerated-networking"></a>Accelerated Networking
Usługa Azure Accelerated Networking zwiększa wydajność sieci, umożliwiając wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej. Aby dowiedzieć się więcej o korzystaniu z usługi Accelerated Networking, zobacz temat dotyczący usługi Accelerated Networking dla maszyn wirtualnych z systemem [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Aby korzystać z tej funkcji przyspieszania sieci wraz z zestawami skalowania, w ustawieniach networkInterfaceConfigurations zestawu skalowania ustaw dla właściwości enableAcceleratedNetworking wartość **true**. Na przykład:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Zestawy skalowania maszyn wirtualnych platformy Azure z Azure Load Balancer
Zobacz [Azure Load Balancer i Virtual Machine Scale Sets](../load-balancer/load-balancer-standard-virtual-machine-scale-sets.md) , aby dowiedzieć się więcej o konfigurowaniu usługa Load Balancer w warstwie Standardowa z Virtual Machine Scale Sets w oparciu o twój scenariusz.

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Tworzenie zestawu skalowania, który odwołuje się do usługi Application Gateway
Aby utworzyć zestaw skalowania, który używa bramy aplikacji, należy odwołać się do puli adresów zaplecza bramy aplikacji w sekcji ipConfiguration zestawu skalowania, tak jak w tej konfiguracji szablonu usługi ARM:

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Należy pamiętać, że brama aplikacji musi znajdować w tej samej sieci wirtualnej, co zestaw skalowania, ale w innej podsieci niż zestaw skalowania.


## <a name="configurable-dns-settings"></a>Konfigurowalne ustawienia DNS
Domyślnie zestawy skalowania przyjmują konkretne ustawienia DNS sieci VNET i podsieci, w których je utworzono. Można jednak skonfigurować ustawienia DNS zestawu skalowania bezpośrednio.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Tworzenie zestawu skalowania z konfigurowalnymi serwerami DNS
Aby utworzyć zestaw skalowania z niestandardową konfiguracją DNS przy użyciu interfejsu wiersza polecenia platformy Azure, dodaj do polecenia **vmss create** argument **--dns-servers**, a po nim podaj oddzielane spacjami adresy IP serwerów. Na przykład:

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Aby skonfigurować niestandardowe serwery DNS w szablonie platformy Azure, do sekcji networkInterfaceConfigurations zestawu skalowania dodaj właściwość dnsSettings. Na przykład:

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Tworzenie zestawu skalowania z konfigurowalnymi nazwami domen maszyn wirtualnych
Aby utworzyć zestaw skalowania z niestandardową nazwą DNS maszyn wirtualnych przy użyciu interfejsu wiersza polecenia, dodaj do polecenia **virtual machine scale set create** argument **--vm-domain-name**, a po nim podaj ciąg reprezentujący nazwę domeny.

Aby ustawić nazwę domeny w szablonie platformy Azure, do sekcji **networkInterfaceConfigurations** zestawu skalowania dodaj właściwość **dnsSettings**. Na przykład:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Dane wyjściowe dla nazwy DNS pojedynczej maszyny wirtualnej będą miały następującą formę:

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Publiczny adres IPv4 dla każdej maszyny wirtualnej
Ogólnie maszyny wirtualne zestawu skalowania platformy Azure nie muszą mieć własnych publicznych adresów IP. W większości scenariuszy jest bardziej ekonomiczny i bezpieczny, aby skojarzyć publiczny adres IP z usługą równoważenia obciążenia lub do pojedynczej maszyny wirtualnej (znanej również jako serwera przesiadkowego), która następnie kieruje połączenia przychodzące do maszyn wirtualnych zestawu skalowania odpowiednio do potrzeb (na przykład za pomocą reguł NAT dla ruchu przychodzącego).

Jednak w niektórych scenariuszach maszyny wirtualne zestawu skalowania muszą mieć własne publiczne adresy IP. Przykładem są gry — gdy konsola musi nawiązać bezpośrednie połączenie z maszyną wirtualną w chmurze obsługującą przetwarzanie symulacji świata fizycznego w grze. Innym przykładem jest sytuacja, w której maszyny wirtualne muszą nawiązywać ze sobą połączenia zewnętrzne między regionami w rozproszonej bazie danych.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Tworzenie zestawu skalowania z publicznym adresem IP dla każdej maszyny wirtualnej
Aby utworzyć zestaw skalowania, w którym każdej maszynie wirtualnej zostanie przypisany publiczny adres IP, przy użyciu interfejsu wiersza polecenia, dodaj do polecenia **vmss create** parametr **--public-ip-per-vm**. 

Aby utworzyć zestaw skalowania przy użyciu szablonu platformy Azure, upewnij się, że interfejs API zasobu Microsoft.Compute/virtualMachineScaleSets ma wersję co najmniej **2017-03-30**, i dodaj właściwość JSON **publicIpAddressConfiguration** do sekcji ipConfigurations zestawu skalowania. Na przykład:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

Przykład szablonu: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Badanie publicznych adresów IP maszyn wirtualnych w zestawie skalowania
Aby uzyskać listę publicznych adresów IP przypisanych do maszyn wirtualnych w zestawie skalowania przy użyciu interfejsu wiersza polecenia, użyj polecenia **az vmss list-instance-public-ips**.

Aby uzyskać listę publicznych adresów IP zestawu skalowania przy użyciu programu PowerShell, użyj polecenia _Get-AzPublicIpAddress_. Na przykład:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Publiczne adresy IP można także badać, odwołując się bezpośrednio do identyfikatora zasobu konfiguracji publicznych adresów IP. Na przykład:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Możesz też wyświetlić publiczne adresy IP przypisane do maszyn wirtualnych w zestawie skalowania przez wysłanie zapytania do witryny [Azure Resource Explorer](https://resources.azure.com) lub interfejsu API REST platformy Azure w wersji co najmniej **2017-03-30**.

Aby wysłać zapytanie do witryny [Azure Resource Explorer](https://resources.azure.com):

1. Otwórz witrynę [Azure Resource Explorer](https://resources.azure.com) w przeglądarce internetowej.
1. Rozwiń pozycję *subskrypcje* po lewej stronie, klikając znak *+* obok niej. Jeśli w obszarze *subskrypcje* znajduje się tylko jeden element, może to oznaczać, że subskrypcje są już rozwinięte.
1. Rozwiń subskrypcję.
1. Rozwiń grupę zasobów.
1. Rozwiń pozycję *dostawcy*.
1. Rozwiń pozycję *Microsoft. COMPUTE*.
1. Rozwiń pozycję *virtualMachineScaleSets*.
1. Rozwiń zestaw skalowania.
1. Kliknij pozycję *publicipaddresses*.

Aby wysłać zapytanie do interfejsu API REST platformy Azure:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Przykładowe dane wyjściowe z witryny [Azure Resource Explorer](https://resources.azure.com) i interfejsu API REST platformy Azure:

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Wiele adresów IP dla każdej karty sieciowej
Z każdą kartą sieciową dołączoną do maszyny wirtualnej w zestawie skalowania może być skojarzona co najmniej jedna konfiguracja adresu IP. Każdej konfiguracji jest przypisany jeden prywatny adres IP. Każda konfiguracja może mieć również skojarzony jeden zasób publicznego adresu IP. Aby zrozumieć, jak wiele adresów IP można przypisać do karty sieciowej i jak wielu publicznych adresów IP można używać w subskrypcji platformy Azure, zapoznaj się z [informacjami o limitach na platformie Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Wiele kart sieciowych dla każdej maszyny wirtualnej
Zależnie od rozmiaru maszyny wirtualnej każda z nich może mieć do 8 kart sieciowych. Maksymalna liczba kart sieciowych na maszynę jest podana w [artykule poświęconym rozmiarom maszyn wirtualnych](../virtual-machines/sizes.md). Wszystkie karty sieciowe podłączone do wystąpienia maszyny wirtualnej muszą łączyć się z tą samą siecią wirtualną. Karty sieciowe mogą łączyć się z różnymi podsieciami, ale wszystkie podsieci muszą należeć do tej samej sieci wirtualnej.

Poniższej przedstawiono przykład profilu sieciowego zestawu skalowania z wieloma wpisami kart sieciowych oraz wieloma publicznymi adresami IP związanymi z poszczególnymi maszynami wirtualnymi:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Sieciowa grupa zabezpieczeń i grupa zabezpieczeń aplikacji dla zestawu skalowania
[Sieciowa grupa zabezpieczeń](../virtual-network/network-security-groups-overview.md) umożliwia filtrowanie ruchu przychodzącego do zasobów platformy Azure i wychodzącego z nich w sieci wirtualnej platformy Azure za pomocą reguł zabezpieczeń. [Grupy zabezpieczeń aplikacji](../virtual-network/network-security-groups-overview.md#application-security-groups) umożliwiają obsługę zabezpieczeń sieci zasobów platformy Azure i pogrupowanie ich jako rozszerzenia struktury aplikacji.

Grupy zabezpieczeń sieci można stosować bezpośrednio do zestawu skalowania przez dodanie odwołania w sekcji konfiguracji interfejsu sieciowego we właściwościach maszyn wirtualnych zestawu skalowania.

Grupy zabezpieczeń aplikacji mogą być również określane bezpośrednio w zestawie skalowania przez dodanie odwołania w sekcji konfiguracji interfejsu sieciowego we właściwościach maszyn wirtualnych zestawu skalowania.

Na przykład:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Aby sprawdzić, czy sieciowa grupa zabezpieczeń jest skojarzona z zestawem skalowania, użyj polecenia `az vmss show`. W poniższym przykładzie użyto opcji `--query`, aby odfiltrować wyniki i pokazać tylko odpowiednią sekcję danych wyjściowych.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Aby sprawdzić, czy grupa zabezpieczeń aplikacji jest skojarzona z zestawem skalowania, użyj polecenia `az vmss show`. W poniższym przykładzie użyto opcji `--query`, aby odfiltrować wyniki i pokazać tylko odpowiednią sekcję danych wyjściowych.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat sieci wirtualnych platformy Azure znajduje się w artykule [Azure virtual networks overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych platformy Azure).
