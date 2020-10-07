---
title: Wdróż klaster w Strefy dostępności
description: Dowiedz się, jak utworzyć klaster usługi Azure Service Fabric w Strefy dostępności.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 56f7224d93293a0a26d09692996d2c4a4ace344b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803742"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Wdróż klaster Service Fabric platformy Azure w Strefy dostępności
Strefy dostępności na platformie Azure to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefa dostępności jest unikatową lokalizacją fizyczną z niezależną mocą, chłodzeniem i siecią w regionie świadczenia usługi Azure.

Service Fabric obsługuje klastry, które rozciągają się między Strefy dostępności przez wdrożenie typów węzłów, które są przypięte do określonych stref. Zapewni to wysoką dostępność aplikacji. Strefy dostępności platformy Azure są dostępne tylko w wybranych regionach. Aby uzyskać więcej informacji, zobacz [strefy dostępności platformy Azure przegląd](../availability-zones/az-overview.md).

Dostępne są przykładowe szablony: [Service Fabric Szablon strefy dostępności krzyżowej](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Zalecana topologia dla podstawowego typu węzła klastrów Service Fabric platformy Azure, obejmująca między Strefy dostępności
Klaster Service Fabric rozproszony przez Strefy dostępności zapewnia wysoką dostępność stanu klastra. Aby objąć klaster Service Fabric między strefami, należy utworzyć podstawowy typ węzła w każdej strefie dostępności obsługiwanej przez ten region. Spowoduje to równomierne dystrybuowanie węzłów inicjatora w każdym z podstawowych typów węzłów.

Zalecana topologia dla typu węzła podstawowego wymaga opisanych poniżej zasobów:

* Poziom niezawodności klastra ma ustawioną wartość Platinum.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinien być mapowany do własnego zestawu skalowania maszyn wirtualnych znajdującego się w różnych strefach.
    * Każdy zestaw skalowania maszyn wirtualnych powinien mieć co najmniej pięć węzłów (trwałość srebrna).
* Pojedynczy zasób publicznego adresu IP używający standardowej jednostki SKU.
* Pojedynczy zasób Load Balancer przy użyciu standardowej jednostki SKU.
* SIECIOWEJ grupy zabezpieczeń przywoływany przez podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.

>[!NOTE]
> Właściwość pojedynczej grupy położenia zestawu skalowania maszyn wirtualnych musi być ustawiona na wartość true, ponieważ Service Fabric nie obsługuje pojedynczego zestawu skalowania maszyn wirtualnych obejmującego strefy.

 ![Architektura strefy dostępności usługi Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny adres IP i zasób Load Balancer
Aby włączyć właściwość Zones w zasobie zestawu skalowania maszyn wirtualnych, moduł równoważenia obciążenia i zasób IP, do których odwołuje się ten zestaw skalowania maszyn wirtualnych, muszą używać *standardowej* jednostki SKU. Utworzenie modułu równoważenia obciążenia lub zasobu IP bez właściwości SKU spowoduje utworzenie podstawowej jednostki SKU, która nie obsługuje Strefy dostępności. Moduł równoważenia obciążenia standardowej jednostki SKU domyślnie blokuje cały ruch z zewnątrz. Aby zezwolić na ruch zewnętrzny, należy wdrożyć sieciowej grupy zabezpieczeń w podsieci.

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
```

```json
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
> Nie można wykonać zmiany w miejscu jednostki SKU dla zasobów publicznego adresu IP i modułu równoważenia obciążenia. W przypadku migrowania z istniejących zasobów z podstawową jednostką SKU zapoznaj się z sekcją migracja tego artykułu.

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


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Włączanie stref na zestawie skalowania maszyn wirtualnych
Aby włączyć strefę, w zestawie skalowania maszyn wirtualnych należy uwzględnić następujące trzy wartości w zasobie zestawu skalowania maszyn wirtualnych.

* Pierwsza wartość to właściwość **Zones** , która określa strefę dostępności, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych.
* Druga wartość to właściwość "singlePlacementGroup", która musi mieć wartość true.
* Trzecia wartość to właściwość "faultDomainOverride" w rozszerzeniu zestawu skalowania maszyn wirtualnych Service Fabric. Wartość tej właściwości powinna zawierać tylko strefę, w której zostanie umieszczony ten zestaw skalowania maszyn wirtualnych. Przykład: "faultDomainOverride": "AZ1" wszystkie zasoby zestawu skalowania maszyn wirtualnych muszą być umieszczone w tym samym regionie, ponieważ klastry usługi Azure Service Fabric nie mają obsługi między regionami.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
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
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Włączanie wielu typów węzłów głównych w Service Fabric zasobów klastra
Aby ustawić co najmniej jeden typ węzła jako podstawowy w zasobie klastra, ustaw właściwość "isprimary" na wartość "true". W przypadku wdrażania klastra Service Fabric w Strefy dostępności należy mieć trzy typy węzłów w różnych strefach.

```json
{
    "reliabilityLevel": "Platinum",
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
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrowanie do korzystania z Strefy dostępności z klastra przy użyciu podstawowej jednostki SKU Load Balancer i podstawowego adresu IP jednostki SKU
Aby przeprowadzić migrację klastra, który używa Load Balancer i adresu IP z podstawową jednostką SKU, należy najpierw utworzyć zupełnie nowy Load Balancer i zasób IP przy użyciu standardowej jednostki SKU. Nie można zaktualizować tych zasobów w miejscu.

Nowy LB i adres IP powinny być przywoływane w nowych typach węzłów strefy dostępności, których chcesz użyć. W powyższym przykładzie dodano trzy nowe zasoby zestawu skalowania maszyn wirtualnych w strefach 1, 2 i 3. Te zestawy skalowania maszyn wirtualnych odwołują się do nowo utworzonych LB i IP i są oznaczone jako typy węzłów głównych w zasobie klastra Service Fabric.

Aby rozpocząć, musisz dodać nowe zasoby do istniejącego szablonu Menedżer zasobów. Te zasoby obejmują:
* Zasób publicznego adresu IP używający standardowej jednostki SKU.
* Zasób Load Balancer przy użyciu standardowej jednostki SKU.
* SIECIOWEJ grupy zabezpieczeń przywoływany przez podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinien być mapowany do własnego zestawu skalowania maszyn wirtualnych znajdującego się w różnych strefach.
    * Każdy zestaw skalowania maszyn wirtualnych powinien mieć co najmniej pięć węzłów (trwałość srebrna).

Przykładem tych zasobów można znaleźć w przykładowym [szablonie](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po zakończeniu wdrażania zasobów można rozpocząć wyłączanie węzłów w podstawowym typie węzła z oryginalnego klastra. Po wyłączeniu węzłów usługi systemowe zostaną zmigrowane do nowego typu węzła podstawowego, który został wdrożony w powyższym kroku.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Po wyłączeniu węzłów usługi systemowe będą działały na podstawowym typie węzła, który jest rozłożony między strefy. Następnie można usunąć wyłączone węzły z klastra. Po usunięciu węzłów można usunąć oryginalny adres IP, Load Balancer i zasoby zestawu skalowania maszyn wirtualnych.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Następnie należy usunąć odwołania do tych zasobów z szablonu Menedżer zasobów, który został wdrożony.

Ostatni krok obejmuje zaktualizowanie nazwy DNS i publicznego adresu IP.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
