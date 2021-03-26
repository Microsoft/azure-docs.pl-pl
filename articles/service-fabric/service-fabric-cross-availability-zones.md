---
title: Wdróż klaster w Strefy dostępności
description: Dowiedz się, jak utworzyć klaster usługi Azure Service Fabric w Strefy dostępności.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: a49fd6f97a9130fa0369d2a36cdc38e59613afc1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544387"
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

Diagram przedstawiający diagram architektury strefy dostępności Service Fabric platformy Azure ![ , który pokazuje architekturę strefy dostępności Service Fabric platformy Azure.][sf-architecture]

Przykładowa lista węzłów, która przedstawia formaty FD/UD w strefach obejmujących zestaw skalowania maszyn wirtualnych

 ![Przykładowa lista węzłów, która przedstawia formaty FD/UD w strefach obejmujących zestaw skalowania maszyn wirtualnych.][sf-multi-az-nodes]

**Dystrybucja replik usług między strefami**: gdy usługa jest wdrażana w elementów NodeType, które obejmują strefy obejmujące obszary, repliki są umieszczane w celu zapewnienia, że są one umieszczone w oddzielnych strefach. Jest to wymagane, ponieważ domena błędów w węzłach obecnych w każdej z tych elementów NodeType są skonfigurowane przy użyciu informacji o strefie (tj. FD = FD:/strefie 1/1 itp..). Na przykład: dla 5 replik lub wystąpień usługi dystrybucja będzie 2-2-1, a środowisko uruchomieniowe podejmie próbę zapewnienia równości dystrybucji w AZs.

**Konfiguracja repliki usługi użytkownika**: stanowe usługi użytkownika wdrożone w strefie dostępności elementów NodeType powinny być skonfigurowane przy użyciu tej konfiguracji: liczba replik z elementem Target = 9, minimum = 5. Ta konfiguracja pomoże działać przez usługę nawet wtedy, gdy jedna strefa ulegnie awarii, ponieważ 6 replik będzie nadal znajdować się w pozostałych dwóch strefach. Uaktualnienie aplikacji w tym scenariuszu spowoduje również przejście przez program.

**ReliabilityLevel klastra**: definiuje liczbę węzłów inicjatora w klastrze, a także rozmiar repliki usług systemowych. Jako że konfiguracja strefy o wysokiej dostępności obejmuje większą liczbę węzłów, które są rozmieszczone w różnych strefach w celu włączenia odporności strefy, wyższa wartość niezawodności zapewni węzłom więcej węzłów inicjatora i repliki usługi systemowej, które są równomiernie dystrybuowane między strefami, dzięki czemu w przypadku awarii strefy klaster i usługi systemowe pozostaną niezmienione. "ReliabilityLevel = Platinum" zapewnia, że 9 węzłów inicjatora rozciąga się między strefami w klastrze z 3 nasionami w każdej strefie, dlatego jest to zalecane w przypadku konfiguracji strefy Cross Availability.

**Scenariusz w strefie**: gdy strefa ulegnie awarii, wszystkie węzły w tej strefie będą wyświetlane w dół. Repliki usług w tych węzłach również zostaną wyłączone. Ze względu na to, że w innych strefach znajdują się repliki, usługa nadal jest w dalszym ciągu odpowiadać na strefy, które działają. Usługi będą wyświetlane w stanie ostrzegawczym, ponieważ docelowa liczba replik nie została jeszcze osiągnięta i ponieważ liczba maszyn wirtualnych jest nadal większa niż minimalna wielkość repliki docelowej. Następnie Service Fabric moduł równoważenia obciążenia przekaże repliki w strefach roboczych, aby odpowiadały skonfigurowanej liczbie docelowych replik. W tym momencie usługi będą wyświetlane w dobrej kondycji. Gdy strefa, w której wystąpiła kopia zapasowa, połączy się z równoważeniem obciążenia, będzie ponownie rozłożyć wszystkie repliki usługi równomiernie we wszystkich strefach.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Przeglądania Włącz wiele stref dostępności w jednym zestawie skalowania maszyn wirtualnych

Wspomniane wcześniej rozwiązanie używa jednego nodeType na AZ. Poniższe rozwiązanie umożliwi użytkownikom wdrożenie 3 AZ w tym samym nodeType.

**Ponieważ ta funkcja jest obecnie dostępna w wersji zapoznawczej, nie jest obecnie obsługiwana w scenariuszach produkcyjnych.**

Pełny przykładowy szablon jest dostępny [tutaj](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure).

![Architektura strefy dostępności usługi Azure Service Fabric][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Konfigurowanie stref na zestawie skalowania maszyn wirtualnych
Aby włączyć strefy na zestawie skalowania maszyn wirtualnych, należy uwzględnić następujące trzy wartości w zasobie zestawu skalowania maszyn wirtualnych.

* Pierwsza wartość to właściwość **Zones** , która określa strefy dostępności obecną w zestawie skalowania maszyn wirtualnych.
* Druga wartość to właściwość "singlePlacementGroup", która musi mieć wartość true. **Zestaw skalowania, który został przełączony w 3 AZs, może skalować do 300 maszyn wirtualnych nawet przy użyciu "singlePlacementGroup = true".**
* Trzecia wartość to "zoneBalance", która zapewnia ścisłe równoważenie strefy. Powinna to być wartość "true". Gwarantuje to, że dystrybucje maszyn wirtualnych między strefami nie są niezrównoważone, upewniając się, że po przekroczeniu jednej z tych stref pozostałe dwie strefy mają wystarczającą liczbę maszyn wirtualnych, aby upewnić się, że klaster utrzymuje nieprzerwane działanie. Klaster z niezrównoważoną dystrybucją maszyn wirtualnych może nie przetrwać scenariusza strefy w dół, ponieważ ta strefa może mieć większość maszyn wirtualnych. Niezrównoważona Dystrybucja maszyn wirtualnych między strefami również prowadzi do problemów związanych z umieszczaniem usługi, & aktualizacje infrastruktury są zablokowane. Przeczytaj o [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* Zastąpień FaultDomain i UpgradeDomain nie są wymagane do skonfigurowania.

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
> * **Klastry Service Fabric powinny mieć co najmniej jedną główną nodeType. DurabilityLevel podstawowego elementów NodeType powinna być większa niż Silver.**
> * AZ Łączenie zestawu skalowania maszyn wirtualnych powinno być skonfigurowane z co najmniej 3 strefami dostępności niezależnie od durabilityLevel.
> * AZ łączenia zestawu skalowania maszyn wirtualnych z trwałością Silver (lub wyższą) powinna mieć co najmniej 15 maszyn wirtualnych.
> * AZ łączenia zestawu skalowania maszyn wirtualnych z trwałością Bronze powinno mieć co najmniej 6 maszyn wirtualnych.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Włączanie obsługi wielu stref w Service Fabric nodeType
Aby zapewnić obsługę wielu stref dostępności, należy włączyć Service Fabric nodeType.

* Pierwsza wartość to **multipleAvailabilityZones** , która powinna być ustawiona na wartość true dla NodeType.
* Druga wartość to **sfZonalUpgradeMode** i jest opcjonalna. Nie można zmodyfikować tej właściwości, jeśli element NodeType o wielu elementach AZ jest już obecny w klastrze.
  Właściwość kontroluje logiczne grupowanie maszyn wirtualnych w domenach uaktualnienia.
  **Jeśli wartość jest równa "Parallel":** Maszyny wirtualne znajdujące się w NodeType zostaną pogrupowane w celu zignorowania informacji o strefie w 5. Spowoduje to, że wszystkie strefy zostaną uaktualnione w tym samym czasie. Ten tryb wdrożenia jest szybszy w przypadku uaktualnień, ale nie jest zalecany, ponieważ przechodzi do wytycznych SDP, co oznacza, że aktualizacje powinny być stosowane tylko do jednej strefy w danym momencie.
  **Jeśli wartość jest pominięta lub ustawiona na "hierarchiczny":** Maszyny wirtualne zostaną pogrupowane w celu odzwierciedlenia rozkładu strefowego do 15. Każda z 3 stref będzie miała 5. Pozwala to zagwarantować, że aktualizacje zostaną nałożone na strefę, przechodząc do następnej strefy dopiero po zakończeniu 5.3. w pierwszej strefie, wolno powoli na 15.
  Ta właściwość definiuje tylko zachowanie uaktualnienia dla aplikacji servicefabric i uaktualnień kodu. Uaktualnienia podstawowego zestawu skalowania maszyn wirtualnych będą nadal równoległe we wszystkich AZ.
  Ta właściwość nie ma żadnego wpływu na dystrybucję UD dla typów węzłów, dla których nie włączono wielu stref.
* Trzecia wartość to **vmssZonalUpgradeMode = Parallel**. Jest to właściwość *obowiązkowa* , która ma zostać skonfigurowana w klastrze, jeśli zostanie dodany NodeType z wieloma AZs. Ta właściwość definiuje tryb uaktualniania dla aktualizacji zestawu skalowania maszyn wirtualnych, które będą wykonywane równolegle we wszystkich AZ jednocześnie.
  Teraz dla tej właściwości można ustawić tylko wartość Parallel.
* ApiVersion zasobów klastra Service Fabric powinien mieć wartość "2020-12-01 — wersja zapoznawcza" lub nowszą.
* Wersja kodu klastra powinna mieć wartość "7.2.445" lub wyższą.

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
> * Publiczny adres IP i zasoby Load Balancer powinny używać standardowej jednostki SKU, jak opisano wcześniej w artykule.
> * Właściwość "multipleAvailabilityZones" w nodeType może być zdefiniowana tylko w czasie tworzenia nodeType i nie można jej później modyfikować. W związku z tym nie można skonfigurować istniejącej elementów NodeType z tą właściwością.
> * Gdy wartość "sfZonalUpgradeMode" jest pomijana lub ustawiona na "hierarchiczny", wdrożenia klastra i aplikacji będą wolniejsze, ponieważ w klastrze znajdują się więcej domen uaktualnienia. Ważne jest, aby poprawnie dostosować limity czasu zasad uaktualniania, aby uwzględnić czas uaktualnienia dla 15 domen uaktualnienia. Należy zaktualizować zasady uaktualniania dla aplikacji i klastra, aby upewnić się, że wdrożenie nie przekracza limitów czasu wdrożenia Serbice zasobów platformy Azure 12hours. Oznacza to, że wdrożenie nie powinno trwać więcej niż 12hours dla 15UDs nie może trwać więcej niż 40 min/UD.
> * Ustaw klaster **reliabilityLevel = Platinum** , aby upewnić się, że klaster przeżyje ten scenariusz w dół.

>[!NOTE]
> W przypadku najlepszych rozwiązań zalecamy sfZonalUpgradeMode ustawienie wartości hierarchicznych lub pomijanie. Wdrożenie będzie podążać za strefową dystrybucją maszyn wirtualnych, które mają wpływ na mniejszą ilość replik i/lub wystąpień, dzięki czemu są one bezpieczniejsze.
> Użyj sfZonalUpgradeMode ustawionej na Parallel, jeśli szybkość wdrażania jest priorytetem lub tylko bezstanowe obciążenie jest uruchamiane na typie węzła z wieloma. Spowoduje to, że UD zostanie wykonane równolegle we wszystkich AZ.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migracja do typu węzła z wieloma Strefy dostępności
W przypadku wszystkich scenariuszy migracji należy dodać nowe nodeType, które będą obsługiwały wiele stref dostępności. Nie można migrować istniejącej nodeType do obsługi wielu stref.
W [tym artykule przedstawiono](./service-fabric-scale-up-primary-node-type.md) szczegółowe instrukcje dotyczące dodawania nowych NodeType oraz dodawania innych zasobów wymaganych do nowych NodeType, takich jak zasoby IP i LB. Ten sam artykuł zawiera również opis wycofywania istniejących nodeType po dodaniu nodeType z wieloma strefami dostępności do klastra.

* Migracja z nodeType, który korzysta z podstawowych zasobów LB i IP: jest to już opisane w [tym miejscu](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) dla rozwiązania z jednym typem węzła na AZ. 
    Dla nowego typu węzła jedyną różnicą jest to, że tylko 1 zestaw skalowania maszyn wirtualnych i 1 NodeType dla wszystkich AZ zamiast 1 każdego na AZ.
* Migracja z nodeType, która używa standardowych zasobów jednostki SKU LB i IP z sieciowej grupy zabezpieczeń: Postępuj zgodnie z tą samą procedurą, jak opisano powyżej, z wyjątkiem tego, że nie ma potrzeby dodawania nowych zasobów LB, IP i sieciowej grupy zabezpieczeń. te same zasoby mogą być ponownie używane w nowym nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png