---
title: Wdrażanie klastra w wielu Strefy dostępności
description: Dowiedz się, jak utworzyć klaster usługi Azure Service Fabric w Strefy dostępności.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727766"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Wdrażanie klastra usługi Azure Service Fabric w wielu Strefy dostępności
Strefy dostępności platformie Azure to oferta wysokiej dostępności, która chroni aplikacje i dane przed awariami centrum danych. Strefa dostępności to unikatowa lokalizacja fizyczna z niezależnym zasilaniem, chłodzeniem i siecią w regionie świadczenia usługi Azure.

Service Fabric obsługuje klastry obejmujące wiele Strefy dostępności przez wdrażanie typów węzłów przypiętych do określonych stref. Zapewni to wysoką dostępność aplikacji. Strefy dostępności platformy Azure są dostępne tylko w wybranych regionach. Aby uzyskać więcej informacji, [zobacz Strefy dostępności platformy Azure Omówienie.](../availability-zones/az-overview.md)

Dostępne są przykładowe szablony: [Service Fabric między strefami dostępności](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Zalecana topologia dla podstawowego typu węzła klastrów usługi Azure Service Fabric obejmujących wiele Strefy dostępności
Klaster Service Fabric rozproszony między Strefy dostępności zapewnia wysoką dostępność stanu klastra. Aby obejmować klaster Service Fabric w różnych strefach, należy utworzyć typ węzła podstawowego w każdej strefie dostępności obsługiwanej przez region. Spowoduje to równomierne rozłożenie węzłów in seed na każdy z typów węzłów podstawowych.

Zalecana topologia dla podstawowego typu węzła wymaga zasobów opisanych poniżej:

* Poziom niezawodności klastra jest ustawiony na Wartość.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinien być mapowany do własnego zestawu skalowania maszyn wirtualnych znajdującego się w różnych strefach.
    * Każdy zestaw skalowania maszyn wirtualnych powinien mieć co najmniej pięć węzłów (trwałość na poziomie Silver).
* Pojedynczy zasób publicznego adresu IP używający standardowej wersji SKU.
* Pojedynczy zasób Load Balancer przy użyciu standardowej wersji SKU.
* Sieciowa siećowa, do której odwołuje się podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.

>[!NOTE]
> Właściwość pojedynczej grupy umieszczania zestawu skalowania maszyn wirtualnych musi mieć wartość true.

Diagram przedstawiający diagram architektury Service Fabric platformy Azure, który przedstawia architekturę ![ Service Fabric strefie dostępności platformy Azure.][sf-architecture]

Przykładowa lista węzłów przedstawiająca formaty FD/UD w zestawie skalowania maszyn wirtualnych obejmującym strefy

 ![Przykładowa lista węzłów przedstawiająca formaty FD/UD w zestawie skalowania maszyn wirtualnych obejmującym strefy.][sf-multi-az-nodes]

**Rozkład replik usługi między** strefami: gdy usługa jest wdrażana w typach nodeType, które są obejmujące strefy, repliki są umieszczane w celu zapewnienia ich umieszczenia w oddzielnych strefach. Jest to zapewniane, ponieważ domeny błędów w węzłach obecnych w każdym z tych typów węzłów są konfigurowane przy użyciu informacji o strefie (tj. FD = fd:/zone1/1 itp.). Na przykład: w przypadku 5 replik lub wystąpień usługi dystrybucja będzie mieć wartości 2–2–1, a środowisko uruchomieniowe spróbuje zapewnić równy rozkład między środowiskami AZ.

**Konfiguracja repliki usługi użytkownika:** stanowe usługi użytkownika wdrożone w strefie między dostępnościami nodeTypes powinny być skonfigurowane z tą konfiguracją: liczba replik z celem = 9, min = 5. Ta konfiguracja pomoże usłudze działać nawet wtedy, gdy jedna strefa nie działa, ponieważ 6 replik będzie nadal działać w dwóch pozostałych strefach. Uaktualnienie aplikacji w takim scenariuszu również zostanie przejedne.

**Niezawodność klastraLevel:** definiuje liczbę węzłów iniekcjatora w klastrze, a także rozmiar repliki usług systemowych. Ponieważ konfiguracja strefy dostępności ma większą liczbę węzłów, które są rozmieszczone w różnych strefach w celu zapewnienia odporności strefy, wyższa wartość niezawodności zapewni, że będzie obecnych więcej węzłów iniekcjatora i replik usługi systemowej, które są równomiernie rozłożone między strefy, dzięki czemu w przypadku awarii strefy klaster i usługi systemowe pozostaną niezmienione. "ReliabilityLevel = Status" zapewni, że w klastrach będzie 9 węzłów iniekcjonowych rozrzucanych w klastrach z 3 iniekcją w każdej strefie, dlatego jest to zalecane dla konfiguracji strefy między dostępnościami.

**Scenariusz z nieuejmą** strefą: gdy strefa nie działa, wszystkie węzły w tej strefie będą wyświetlane jako nie działa. Repliki usług w tych węzłach również nie będą. Ze względu na to, że w innych strefach istnieją repliki, usługa nadal odpowiada z replikami podstawowymi, które przejdą do stref, które działają. Usługi będą wyświetlane w stanie ostrzeżenia, ponieważ docelowa liczba replik nie została jeszcze osiągnięta, a ponieważ liczba maszyn wirtualnych jest nadal większa niż minimalny rozmiar repliki docelowej. Następnie Service Fabric równoważenia obciążenia będzie konfigurować repliki w strefach roboczych w celu dopasowania ich do skonfigurowanej liczby replik docelowych. W tym momencie usługi będą wyglądać na w dobrej kondycji. Gdy strefa, która nie została ponownie wywrócą, równoważenie obciążenia ponownie równomiernie rozłozy wszystkie repliki usługi we wszystkich strefach.

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny adres IP i Load Balancer zasobów
Aby włączyć właściwość zones w zasobie zestawu skalowania maszyn wirtualnych, zarówno zasób równoważenia obciążenia, jak i zasób adresu IP, do których odwołuje się ten zestaw skalowania maszyn wirtualnych, muszą używać standardowej *wersji* SKU. Utworzenie usługi równoważenia obciążenia lub zasobu adresu IP bez właściwości SKU spowoduje utworzenie podstawowej wersji SKU, która nie obsługuje Strefy dostępności. Standardowa sku równoważenia obciążenia domyślnie zablokuje cały ruch z zewnątrz. Aby zezwolić na ruch zewnętrzny, w podsieci musi zostać wdrożona sieciowagrupa sieciowa.

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
> Nie można zmienić w miejscu typu SKU w zasobach publicznego adresu IP i usługi równoważenia obciążenia. Jeśli migrujesz z istniejących zasobów z podstawową sku, zobacz sekcję migracji w tym artykule.

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
usługa Load Balancer w warstwie Standardowa i standardowy publiczny adres IP wprowadzają nowe możliwości i różne zachowania łączności wychodzącej w porównaniu z użyciem podstawowych jednostki SKU. Jeśli chcesz mieć łączność wychodzącą podczas pracy z jednostkami SKU w standardowych, musisz jawnie zdefiniować ją za pomocą publicznych adresów IP w standardowych lub standardowych publicznych Load Balancer. Aby uzyskać więcej informacji, zobacz [Połączenia wychodzące i](../load-balancer/load-balancer-outbound-connections.md) [usługa Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Szablon standardowy odwołuje się do sieciowej grupy zabezpieczeń, która domyślnie zezwala na cały ruch wychodzący. Ruch przychodzący jest ograniczony do portów, które są wymagane do Service Fabric zarządzania. Reguły sieciowej organizacji sieciowej mogą być modyfikowane w celu spełnienia wymagań.

>[!NOTE]
> Każdy Service Fabric sieciowy, który korzysta z usługi SLB standardowej sku, musi mieć pewność, że każdy typ węzła ma regułę zezwalania na ruch wychodzący na porcie 443. Jest to niezbędne do ukończenia konfiguracji klastra, a każde wdrożenie bez takiej reguły zakończy się niepowodzeniem.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Włączanie stref w zestawie skalowania maszyn wirtualnych
Aby włączyć strefę, w zestawie skalowania maszyn wirtualnych należy uwzględnić następujące trzy wartości w zasobie zestawu skalowania maszyn wirtualnych.

* Pierwszą wartością jest **właściwość zones,** która określa strefę dostępności, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych.
* Druga wartość to właściwość "singlePlacementGroup", która musi być ustawiona na wartość true.
* Trzecia wartość to właściwość "faultDomainOverride" w rozszerzeniu Service Fabric zestawu skalowania maszyn wirtualnych. Wartość tej właściwości powinna zawierać tylko strefę, w której zostanie umieszczony ten zestaw skalowania maszyn wirtualnych. Przykład: "faultDomainOverride": "az1" Wszystkie zasoby zestawu skalowania maszyn wirtualnych muszą być umieszczone w tym samym regionie, ponieważ klastry usługi Azure Service Fabric nie obsługują regionów.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Włączanie wielu podstawowych typów węzłów w zasobie Service Fabric klastra
Aby ustawić co najmniej jeden typ węzła jako podstawowy w zasobie klastra, ustaw właściwość "isPrimary" na wartość "true". Podczas wdrażania klastra Service Fabric klastrze w Strefy dostępności powinny być trzy typy węzłów w różnych strefach.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrowanie do Strefy dostępności z klastra przy użyciu podstawowego adresu Load Balancer i podstawowego adresu IP SKU
Aby przeprowadzić migrację klastra korzystającego z adresu Load Balancer i adresu IP z podstawową sku, należy najpierw utworzyć zupełnie nowy zasób adresu Load Balancer ip przy użyciu standardowej wersji SKU. Nie można zaktualizować tych zasobów w miejscu.

Nowe typy węzłów stref dostępności, których chcesz użyć, powinny być przywołyne w nowych typach węzłów między strefami dostępności. W powyższym przykładzie dodano trzy nowe zasoby zestawu skalowania maszyn wirtualnych w strefach 1,2 i 3. Te zestawy skalowania maszyn wirtualnych odwołują się do nowo utworzonego usługi LB i adresu IP i są oznaczone jako typy węzłów podstawowych w zasobie Service Fabric klastra.

Aby rozpocząć, musisz dodać nowe zasoby do istniejącego szablonu Resource Manager szablonu. Te zasoby obejmują:
* Zasób publicznego adresu IP używający standardowej wersji SKU.
* Zasób Load Balancer przy użyciu standardowej wersji SKU.
* Sieciowa grupa danych, do której odwołuje się podsieć, w której wdrażasz zestawy skalowania maszyn wirtualnych.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinien być mapowany do własnego zestawu skalowania maszyn wirtualnych znajdującego się w różnych strefach.
    * Każdy zestaw skalowania maszyn wirtualnych powinien mieć co najmniej pięć węzłów (trwałość na poziomie Silver).

Przykład tych zasobów można znaleźć w [przykładowym szablonie](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po zakończeniu wdrażania zasobów można rozpocząć wyłączanie węzłów typu węzła podstawowego z oryginalnego klastra. Po wyłączeniu węzłów usługi systemowe zostaną zmigrowane do nowego typu węzła podstawowego, który został wdrożony w powyższym kroku.

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

Po wyłączeniu wszystkich węzłów usługi systemowe będą działać na podstawowym typie węzła, który jest rozłożony na strefy. Następnie można usunąć wyłączone węzły z klastra. Po usunięciu węzłów można usunąć oryginalny adres IP, Load Balancer i zasoby zestawu skalowania maszyn wirtualnych.

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

Następnie należy usunąć odwołania do tych zasobów z Resource Manager szablonu, który został wdrożony.

Ostatnim krokiem będzie zaktualizowanie nazwy DNS i publicznego adresu IP.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Wersja zapoznawcza) Włączanie wielu stref dostępności w jednym zestawie skalowania maszyn wirtualnych

We wcześniej wymienionym rozwiązaniu jest używany jeden typ węzła na az. Następujące rozwiązanie umożliwi użytkownikom wdrażanie 3 az w tym samym nodeType.

**Ponieważ ta funkcja jest obecnie w wersji zapoznawczej, nie jest obecnie obsługiwana w scenariuszach produkcyjnych.**

Pełny przykładowy szablon znajduje się [tutaj.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Architektura Service Fabric strefy dostępności platformy Azure][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Konfigurowanie stref w zestawie skalowania maszyn wirtualnych
Aby włączyć strefy w zestawie skalowania maszyn wirtualnych, należy uwzględnić następujące trzy wartości w zasobie zestawu skalowania maszyn wirtualnych.

* Pierwszą wartością jest **właściwość zones,** która określa Strefy dostępności w zestawie skalowania maszyn wirtualnych.
* Druga wartość to właściwość "singlePlacementGroup", która musi być ustawiona na wartość true. **Zestaw skalowania rozmieszczony w trzech azach może skalować w górę do 300 maszyn wirtualnych nawet przy użyciu wartości "singlePlacementGroup = true".**
* Trzecia wartość to "zoneBalance", która zapewnia ścisłe równoważenie strefy. Powinno to być "true". Dzięki temu rozkłady maszyn wirtualnych w różnych strefach nie są niezrównoważyć, co gwarantuje, że gdy jedna ze stref nie działa, pozostałe dwie strefy mają wystarczającą ilość maszyn wirtualnych, aby zapewnić nieprzerwane działanie klastra. Klaster z niezrównoważytą dystrybucją maszyn wirtualnych może nie przetrwać scenariusza z niesprawną strefą, ponieważ ta strefa może mieć większość maszyn wirtualnych. Niezrównoważyć dystrybucji maszyn wirtualnych między strefami spowoduje również problemy związane z umieszczaniem usług & aktualizacji infrastruktury zablokowane.. Przeczytaj o [strefie Równoważenie](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* Przesłonięcia FaultDomain i UpgradeDomain nie są wymagane do skonfigurowania.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric powinny mieć co najmniej jeden podstawowy typ węzła. Poziom trwałości podstawowych typów węzłów powinien mieć poziom Silver lub większą.**
> * Zestaw skalowania maszyn wirtualnych obejmujący wiele stref dostępności powinien być skonfigurowany z co najmniej 3 strefami dostępności niezależnie od durabilityLevel.
> * Zestaw skalowania maszyn wirtualnych o trwałości Silver (lub większej) powinien mieć co najmniej 15 maszyn wirtualnych.
> * Zestaw skalowania maszyn wirtualnych o trwałości Brązowa powinien mieć co najmniej 6 maszyn wirtualnych.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Włączanie obsługi wielu stref w Service Fabric nodeType
Dla Service Fabric nodeType musi być włączona obsługa wielu stref dostępności.

* Pierwsza wartość to **multipleAvailabilityZones,** która powinna mieć wartość true dla właściwości nodeType.
* Druga wartość to **sfZonalUpgradeMode** i jest opcjonalna. Nie można zmodyfikować tej właściwości, jeśli w klastrze znajduje się już typ węzła z wieloma węzłami AZ.
  Właściwość kontroluje logiczne grupowanie maszyn wirtualnych w domenach uaktualnienia.
  **Jeśli wartość jest ustawiona na wartość "Parallel":** Maszyny wirtualne o typie nodetype zostaną zgrupowane w uid, ignorując informacje o strefie w 5 uJ. Spowoduje to, że ud0 we wszystkich strefach do uaktualnienia w tym samym czasie. Ten tryb wdrażania jest szybszy w przypadku uaktualnień, ale nie jest zalecany, ponieważ jest on stosowany zgodnie z wytycznymi protokołu SDP, zgodnie z którymi aktualizacje powinny być stosowane tylko w jednej strefie na raz.
  **Jeśli wartość zostanie pominięta lub ustawiona na wartość "Hierarchiczna":** Maszyny wirtualne zostaną pogrupowane w celu odzwierciedlenia rozkładu strefowego w maksymalnie 15 UD. Każda z trzech stref będzie mieć 5 identyfikatorów UD. Gwarantuje to, że aktualizacje są zmieniane na strefy, przechodząc do następnej strefy dopiero po ukończeniu 5 identyfikatorów UD w pierwszej strefie, powoli w 15 ujściach (3 strefy, 5 identyfikatorów UD), co jest bezpieczniejsze z perspektywy klastra i aplikacji użytkownika.
  Ta właściwość definiuje tylko zachowanie uaktualniania aplikacji ServiceFabric i uaktualnień kodu. Uaktualnienia bazowego zestawu skalowania maszyn wirtualnych będą nadal równoległe we wszystkich serwerach az.
  Ta właściwość nie będzie mieć żadnego wpływu na dystrybucję UD dla typów węzłów, które nie mają włączonej wielu stref.
* Trzecia wartość to **vmssZonalUpgradeMode = Parallel**. Jest to właściwość *obowiązkowa* do skonfigurowania w klastrze, jeśli dodano element nodeType z wieloma węzłami AZ. Ta właściwość definiuje tryb uaktualniania aktualizacji zestawu skalowania maszyn wirtualnych, co będzie odbywać się równolegle we wszystkich serwerach az jednocześnie.
  W tej chwili tę właściwość można ustawić tylko na wartość równoległą.
* Wartość Service Fabric apiVersion zasobu klastra powinna mieć wartość "2020-12-01-preview" lub wyższą.
* Wersja kodu klastra powinna mieć wartość "7.2.445" lub nowszą.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Publiczny adres IP i Load Balancer powinny używać standardowej wersji SKU, jak opisano wcześniej w tym artykule.
> * Właściwość "multipleAvailabilityZones" w węźle nodeType można zdefiniować tylko podczas tworzenia typu nodeType i nie można jej później zmodyfikować. W związku z tym za pomocą tej właściwości nie można skonfigurować istniejących typów nodeType.
> * Gdy wartość "sfZonalUpgradeMode" zostanie pominięta lub ustawiona na wartość "Hierarchical", wdrożenia klastra i aplikacji będą wolniejsze, ponieważ w klastrze będzie więcej domen uaktualnienia. Ważne jest, aby prawidłowo dostosować limity czasu zasad uaktualniania, aby uwzględnić czas trwania uaktualnienia dla 15 domen uaktualnienia. Zasady uaktualniania dla aplikacji i klastra powinny zostać zaktualizowane, aby upewnić się, że wdrożenie nie przekracza limitów czasu wdrożenia usługi Azure Resource Przekroczenie limitu czasu 12 godzin. Oznacza to, że wdrożenie nie powinno trwać dłużej niż 12 godzin dla 15 identyfikatorów JDS, tj. Nie powinien trwać dłużej niż 40 minut/UD.
> * Ustaw klaster **reliabilityLevel = Status, aby** upewnić się, że klaster przeżyje scenariusz z jedną strefą.

>[!NOTE]
> Aby uzyskać najlepsze rozwiązanie, zalecamy ustawienie trybu sfZonalUpgradeMode na Hierarchiczne lub pominięcie go. Wdrożenie będzie zgodne z strefową dystrybucją maszyn wirtualnych, która wpływa na mniejszą liczbę replik i/lub wystąpień, co czyni je bezpieczniejszymi.
> Użyj właściwości sfZonalUpgradeMode ustawionej na wartość Parallel, jeśli szybkość wdrażania ma priorytet lub tylko bez stanowe obciążenie jest uruchamiane na typie węzła z wieloma węzłami AZ. Spowoduje to, że śledzenie ud będzie odbywać się równolegle we wszystkich az.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migracja do typu węzła z wieloma Strefy dostępności
We wszystkich scenariuszach migracji należy dodać nowy typ węzła, który będzie miał obsługiwanych wiele stref dostępności. Nie można migrować istniejącego typu nodeType w celu obsługi wielu stref.
Ten artykuł [zawiera](./service-fabric-scale-up-primary-node-type.md) szczegółowe instrukcje dodawania nowego typu węzła oraz innych zasobów wymaganych dla nowego typu węzła, takich jak zasoby adresu IP i usługi LB. W tym samym artykule opisano również teraz wycofanie istniejącego typu nodeType po dodaniu do klastra typu nodeType z wieloma strefami dostępności.

* Migracja z typu węzła korzystającego z podstawowych zasobów [](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) usługi LB i adresu IP: Jest to już opisane w tym miejscu dla rozwiązania z jednym typem węzła na az. 
    W przypadku nowego typu węzła jedyną różnicą jest to, że istnieje tylko 1 zestaw skalowania maszyn wirtualnych i 1 typ węzła dla wszystkich węzłów az zamiast 1 dla każdego az.
* Migracja z typu nodeType używającego standardowego zestawu SKU LB i zasobów IP z sieciową migrację: wykonaj tę samą procedurę, jak opisano powyżej, z wyjątkiem, że nie ma potrzeby dodawania nowych zasobów lb, IP i NSG, a te same zasoby mogą być ponownie używane w nowym typie nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
