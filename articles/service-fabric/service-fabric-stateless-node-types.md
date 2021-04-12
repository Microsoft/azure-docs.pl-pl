---
title: Wdróż bezstanowe typy węzłów w klastrze Service Fabric
description: Dowiedz się, jak tworzyć i wdrażać bezstanowe typy węzłów w klastrze Service Fabric platformy Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 74680f7b56ad98851e2839b53c1f9e92b6c6c23a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030016"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Wdrażanie klastra Service Fabric platformy Azure z autonomicznymi typami węzłów
Service Fabric typy węzłów są związane z założeniem, że w pewnym momencie usługi stanowe mogą być umieszczane w węzłach. Bezstanowe typy węzłów obniżają to założenie dla typu węzła, w związku z tym w taki sposób, aby typ węzła korzystał z innych funkcji, takich jak szybsze operacje skalowania w poziomie, obsługa automatycznych uaktualnień systemu operacyjnego w przypadku trwałości i skalowanie do ponad 100 węzłów w jednym zestawie skalowania maszyn wirtualnych.

* Typów węzła podstawowego nie można skonfigurować jako bezstanowego
* Typy węzłów bezstanowych są obsługiwane tylko z poziomu trwałości Bronze
* Typy węzłów bezstanowych są obsługiwane tylko w środowisku uruchomieniowym Service Fabric w wersji 7.1.409 lub nowszej.


Dostępne są przykładowe szablony: [Service Fabric bezstanowego szablonu typów węzłów](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Włączanie bezstanowych typów węzłów w klastrze Service Fabric
Aby ustawić jeden lub więcej typów węzłów jako bezstanowe w zasobów klastra, ustaw właściwość **Isstate bez stanu** na **wartość true**. Podczas wdrażania klastra Service Fabric przy użyciu bezstanowych typów węzłów Pamiętaj, aby mieć co najmniej jeden typ węzła podstawowego w zasobie klastra.

* ApiVersion zasobów klastra Service Fabric powinien mieć wartość "2020-12-01 — wersja zapoznawcza" lub nowszą.

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

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych dla typów węzłów bezstanowych
Aby włączyć bezstanowe typy węzłów, należy skonfigurować źródłowy zasób zestawu skalowania maszyn wirtualnych w następujący sposób:

* Wartość właściwości  **singlePlacementGroup** , która powinna być ustawiona na **false** , jeśli wymagane jest skalowanie do ponad 100 maszyn wirtualnych.
* Wartość **upgrademode** zestawu skalowania powinna być równa " **roll**".
* Tryb uaktualniania stopniowego wymaga skonfigurowanych rozszerzeń kondycji aplikacji lub sond kondycji. Skonfiguruj sondę kondycji z domyślną konfiguracją dla typów węzłów bezstanowych zgodnie z sugerowaną poniżej. Po wdrożeniu aplikacji na typ węzła można zmienić porty sondy kondycji/rozszerzenia kondycji, aby monitorować kondycję aplikacji.

>[!NOTE]
> Podczas korzystania z funkcji automatycznego skalowania z bezstanową elementów NodeType po przeprowadzeniu skalowania w dół stan węzła nie jest automatycznie czyszczony. Aby można było oczyścić NodeState węzłów w dół podczas skalowania automatycznego, zaleca się użycie [pomocnika automatycznego skalowania Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper) .

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

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Konfigurowanie typów węzłów bezstanowych z wieloma Strefy dostępności
Aby skonfigurować bezstanowe łączenie NodeType w wielu strefach dostępności, zobacz dokumentację [poniżej](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set), a także kilka zmian w następujący sposób:

* Ustaw **singlePlacementGroup** :  **false**  , jeśli jest wymagane włączenie wielu grup umieszczania.
* Ustaw wartość  **upgrademode** : **wycofywanie**   i Dodawanie danych o rozszerzeniu/kondycji kondycji aplikacji, jak wspomniano powyżej.
* Ustaw **platformFaultDomainCount** : **5** dla zestawu skalowania maszyn wirtualnych.

>[!NOTE]
> Bez względu na VMSSZonalUpgradeMode skonfigurowany w klastrze, aktualizacje zestawu skalowania maszyn wirtualnych są zawsze wykonywane sekwencyjnie jedną strefę dostępności dla bezstanowego NodeType, który obejmuje wiele stref, ponieważ używa trybu uaktualnienia stopniowego.

Aby uzyskać informacje na ten temat, należy zapoznać się z [szablonem](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) dotyczącym konfigurowania typów węzłów bezstanowych z wieloma strefy dostępności

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny adres IP i zasób Load Balancer
Aby włączyć skalowanie do ponad 100 maszyn wirtualnych w ramach zasobu zestawu skalowania maszyn wirtualnych, moduł równoważenia obciążenia i zasób IP, do których odwołuje się ten zestaw skalowania maszyn wirtualnych, muszą używać *standardowej* jednostki SKU. Utworzenie modułu równoważenia obciążenia lub zasobu IP bez właściwości SKU spowoduje utworzenie podstawowej jednostki SKU, która nie obsługuje skalowania do ponad 100 maszyn wirtualnych. Moduł równoważenia obciążenia standardowej jednostki SKU domyślnie blokuje cały ruch z zewnątrz. Aby zezwolić na ruch zewnętrzny, należy wdrożyć sieciowej grupy zabezpieczeń w podsieci.

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
> Nie można wykonać zmiany w miejscu jednostki SKU dla zasobów publicznego adresu IP i modułu równoważenia obciążenia. 

### <a name="virtual-machine-scale-set-nat-rules"></a>Reguły NAT zestawu skalowania maszyn wirtualnych
Reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia powinny być zgodne z pulami NAT z zestawu skalowania maszyn wirtualnych. Każdy zestaw skalowania maszyn wirtualnych musi mieć unikatową pulę NAT dla ruchu przychodzącego.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardowa jednostka SKU Load Balancer reguły ruchu wychodzącego
Usługa Load Balancer w warstwie Standardowa i Standard publiczny adres IP wprowadzają nowe możliwości i inne zachowania łączności wychodzącej w porównaniu z użyciem podstawowych jednostek SKU. Jeśli chcesz mieć łączność wychodzącą podczas pracy z standardowymi jednostkami SKU, musisz jawnie zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowych Load Balancer publicznych. Aby uzyskać więcej informacji, zobacz [połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md) i [Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standardowy szablon odwołuje się do elementu sieciowej grupy zabezpieczeń, który domyślnie zezwala na cały ruch wychodzący. Ruch przychodzący jest ograniczony do portów, które są wymagane do Service Fabric operacji zarządzania. Reguły sieciowej grupy zabezpieczeń można modyfikować w celu spełnienia wymagań.

>[!NOTE]
> Każdy klaster Service Fabric korzystający ze standardowego modułu równoważenia jednostki SKU musi upewnić się, że każdy typ węzła ma regułę zezwalającą na ruch wychodzący na porcie 443. Jest to niezbędne do ukończenia instalacji klastra, a wszelkie wdrożenia bez takiej reguły zakończą się niepowodzeniem.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrowanie do korzystania z bezstanowych typów węzłów w klastrze
W przypadku wszystkich scenariuszy migracji należy dodać nowy typ węzła tylko bezstanowy. Nie można migrować istniejącego typu węzła do stanu bezstanowego.

Aby przeprowadzić migrację klastra, który używa Load Balancer i adresu IP z podstawową jednostką SKU, należy najpierw utworzyć zupełnie nowy Load Balancer i zasób IP przy użyciu standardowej jednostki SKU. Nie można zaktualizować tych zasobów w miejscu.

Nowy LB i adres IP powinny być przywoływane w nowych typach bezstanowych węzłów, których chcesz użyć. W powyższym przykładzie dodano nowe zasoby zestawu skalowania maszyn wirtualnych, które będą używane dla bezstanowych typów węzłów. Te zestawy skalowania maszyn wirtualnych odwołują się do nowo utworzonych LB i IP i są oznaczone jako bezstanowe typy węzłów w zasobie klastra Service Fabric.

Aby rozpocząć, musisz dodać nowe zasoby do istniejącego szablonu Menedżer zasobów. Te zasoby obejmują:
* Zasób publicznego adresu IP używający standardowej jednostki SKU.
* Zasób Load Balancer przy użyciu standardowej jednostki SKU.
* SIECIOWEJ grupy zabezpieczeń przywoływany przez podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.

Po zakończeniu wdrażania zasobów można rozpocząć wyłączanie węzłów w typie węzła, który ma zostać usunięty z oryginalnego klastra.

## <a name="next-steps"></a>Następne kroki 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Typy węzłów i zestawy skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md)

