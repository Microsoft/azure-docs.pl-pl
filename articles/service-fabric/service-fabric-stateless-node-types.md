---
title: Wdrażanie typów węzłów bez stanowych w klastrze Service Fabric stanowym
description: Dowiedz się, jak tworzyć i wdrażać bez stanowe typy węzłów w klastrze Service Fabric Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 68c617b6e9345910bfd913e61e227a8e6c401bbc
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576044"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Wdrażanie klastra usługi Azure Service Fabric z typami węzłów tylko do stanu
Service Fabric typów węzłów są z założeniami, że w pewnym momencie usługi stanowe mogą być umieszczane w węzłach. Typy węzłów bez stanowych złagodniają to założenie dla typu węzła, dzięki czemu typ węzła może korzystać z innych funkcji, takich jak szybsze operacje skalowania na zewnątrz, obsługa automatycznych uaktualnień systemu operacyjnego na brązowej trwałości i skalowanie na zewnątrz do ponad 100 węzłów w jednym zestawie skalowania maszyn wirtualnych.

* Podstawowych typów węzłów nie można skonfigurować jako bez stanowych
* Typy węzłów bez stanowych są obsługiwane tylko w przypadku poziomów trwałości brązu
* Typy węzłów bez stanowych są obsługiwane tylko w Service Fabric Runtime w wersji 7.1.409 lub nowszej.


Dostępne są przykładowe szablony: [szablon Service Fabric typów węzłów](https://github.com/Azure-Samples/service-fabric-cluster-templates) bez stanowych

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Włączanie bez stanowych typów węzłów w Service Fabric klastra
Aby ustawić co najmniej jeden typ węzła jako bezstanowy w zasobie klastra, ustaw właściwość **isStateless** na **wartość true.** Podczas wdrażania klastra Service Fabric z typami węzłów bez stanowych pamiętaj, aby mieć co najmniej jeden typ węzła podstawowego w zasobie klastra.

* Wersja Service Fabric apiVersion zasobu klastra powinna mieć wartość "2020-12-01-preview" lub wyższą.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych dla typów węzłów bez stanu
Aby włączyć bez stanowe typy węzłów, należy skonfigurować bazowy zasób zestawu skalowania maszyn wirtualnych w następujący sposób:

* Wartość właściwości  **singlePlacementGroup,** która powinna mieć wartość **false,** jeśli wymagane jest skalowanie do ponad 100 maszyn wirtualnych.
* Dla trybu **upgradeMode zestawu skalowania** należy ustawić wartość **Rolling**.
* Tryb uaktualnienia stopniowego wymaga skonfigurowanego rozszerzenia kondycji aplikacji lub sond kondycji. Skonfiguruj sondę kondycji z domyślną konfiguracją dla typów węzłów bez stanowych zgodnie z sugestią poniżej. Po wdrożeniu aplikacji w typie węzła można zmienić porty rozszerzenia Sonda kondycji/Kondycja w celu monitorowania kondycji aplikacji.

>[!NOTE]
> Podczas korzystania z autoskalowania z typami węzłów bez stanu po operacji skalowania w dół stan węzła nie jest automatycznie czyszczony. Aby wyczyścić węzeł NodeState węzłów w dół podczas autoskalowania, zaleca się użycie Service Fabric autoskalowania. [](https://github.com/Azure/service-fabric-autoscale-helper)

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Konfigurowanie typów węzłów bez stanu z wieloma Strefy dostępności
Aby skonfigurować typ węzła bez stanowego obejmujący wiele stref dostępności, postępuj zgodnie z dokumentacją w tym miejscu [wraz](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)z kilkoma zmianami w następujący sposób:

* Ustaw **wartość singlePlacementGroup:**  **false,**  jeśli należy włączyć wiele grup umieszczania.
* Ustaw  **tryb upgradeMode:** **stopniowe**   i dodawanie rozszerzenia kondycji aplikacji/sond kondycji, jak wspomniano powyżej.
* Ustaw **wartość elementu platformFaultDomainCount:** **5** dla zestawu skalowania maszyn wirtualnych.

>[!NOTE]
> Niezależnie od zestawu skalowania maszyn wirtualnych VMSSZonalUpgradeMode skonfigurowanego w klastrze aktualizacje zestawu skalowania maszyn wirtualnych są zawsze sekwencyjne po jednej strefie dostępności w tym samym czasie dla bez stanowego typu węzła, który obejmuje wiele stref, ponieważ używa trybu uaktualniania stopniowego.

Aby uzyskać informacje, zobacz [szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) do konfigurowania typów węzłów bez stanowych z wieloma Strefy dostępności

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny adres IP i Load Balancer zasobów
Aby umożliwić skalowanie do ponad 100 maszyn wirtualnych w zasobie zestawu skalowania maszyn wirtualnych, zarówno zasób równoważenia obciążenia, jak i zasób adresu IP, do których odwołuje się ten zestaw skalowania maszyn wirtualnych, muszą używać standardowej *wersji* SKU. Utworzenie usługi równoważenia obciążenia lub zasobu adresu IP bez właściwości SKU spowoduje utworzenie podstawowej wersji SKU, która nie obsługuje skalowania do ponad 100 maszyn wirtualnych. Standardowa sku równoważenia obciążenia domyślnie będzie blokować cały ruch z zewnątrz; Aby zezwolić na ruch zewnętrzny, w podsieci musi zostać wdrożona sieciowagrupa sieciowa.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Nie można zmienić w miejscu typu SKU w zasobach publicznego adresu IP i usługi równoważenia obciążenia. 

### <a name="virtual-machine-scale-set-nat-rules"></a>Reguły NAT zestawu skalowania maszyn wirtualnych
Reguły NAT dla ruchu przychodzącego usługi równoważenia obciążenia powinny być zgodne z pulami NAT z zestawu skalowania maszyn wirtualnych. Każdy zestaw skalowania maszyn wirtualnych musi mieć unikatową pulę nat dla ruchu przychodzącego.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardowa SKU Load Balancer reguł ruchu wychodzącego
usługa Load Balancer w warstwie Standardowa i standardowy publiczny adres IP wprowadzają nowe możliwości i różne zachowania łączności wychodzącej w porównaniu do korzystania z podstawowych jednostki SKU. Jeśli chcesz mieć łączność wychodzącą podczas pracy z jednostkami SKU w standardowych, musisz jawnie zdefiniować ją za pomocą publicznych adresów IP w standardowych lub publicznych Load Balancer. Aby uzyskać więcej informacji, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md) i [Usługa Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Szablon standardowy odwołuje się do sieciowej grupy zabezpieczeń, która domyślnie zezwala na cały ruch wychodzący. Ruch przychodzący jest ograniczony do portów, które są wymagane do Service Fabric zarządzania. Reguły sieciowej organizacji sieciowej można zmodyfikować w celu spełnienia wymagań.

>[!NOTE]
> Każdy Service Fabric sieciowy, który korzysta z usługi SLB standardowej sku, musi mieć pewność, że każdy typ węzła ma regułę zezwalania na ruch wychodzący na porcie 443. Jest to niezbędne do ukończenia konfiguracji klastra, a każde wdrożenie bez takiej reguły zakończy się niepowodzeniem.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrowanie do używania typów węzłów bez stanu w klastrze
We wszystkich scenariuszach migracji należy dodać nowy typ węzła tylko do stanu. Nie można migrować istniejącego typu węzła jako bez stanowego.

Aby przeprowadzić migrację klastra korzystającego z adresu Load Balancer i adresu IP z podstawową sku, należy najpierw utworzyć zupełnie nowy zasób adresu Load Balancer ip przy użyciu standardowej wersji SKU. Nie można zaktualizować tych zasobów w miejscu.

Nowe usługi LB i IP powinny być przywołyne w nowych typach węzłów bez stanowych, których chcesz użyć. W powyższym przykładzie dodano nowe zasoby zestawu skalowania maszyn wirtualnych, które będą używane dla typów węzłów bez stanowych. Te zestawy skalowania maszyn wirtualnych odwołują się do nowo utworzonej usługi LB i adresu IP i są oznaczone jako bez stanowe typy węzłów w zasobie Service Fabric klastra.

Aby rozpocząć, musisz dodać nowe zasoby do istniejącego szablonu Resource Manager szablonu. Te zasoby obejmują:
* Zasób publicznego adresu IP korzystający z standardowej wersji SKU.
* Zasób Load Balancer przy użyciu standardowej wersji SKU.
* Sieciowa siećowa, do której odwołuje się podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.

Po zakończeniu wdrażania zasobów możesz zacząć wyłączać węzły typu węzła, który chcesz usunąć z oryginalnego klastra.

## <a name="next-steps"></a>Następne kroki 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Typy węzłów i zestawy skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md)

