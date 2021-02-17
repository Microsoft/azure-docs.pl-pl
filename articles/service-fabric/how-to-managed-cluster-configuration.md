---
title: Skonfiguruj klaster zarządzany Service Fabric (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować klaster zarządzany Service Fabric na potrzeby automatycznych uaktualnień systemu operacyjnego, reguł sieciowej grupy zabezpieczeń i innych.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642499"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opcje konfiguracji klastra zarządzanego (wersja zapoznawcza) Service Fabric

Oprócz wybrania [Service Fabric zarządzanej jednostki SKU klastra](overview-managed-cluster.md#service-fabric-managed-cluster-skus) podczas tworzenia klastra istnieją różne sposoby ich konfiguracji. W bieżącej wersji zapoznawczej można:

* Dodawanie [rozszerzenia zestawu skalowania maszyn wirtualnych](how-to-managed-cluster-vmss-extension.md) do typu węzła
* Włączanie [automatycznych uaktualnień systemu operacyjnego](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) dla węzłów
* Włączanie [szyfrowania dysków systemu operacyjnego i danych](how-to-enable-managed-cluster-disk-encryption.md) w węzłach
* Informacje o [konfiguracjach sieciowych](how-to-managed-cluster-configuration.md#networking-configurations)
* Skonfiguruj [zarządzaną tożsamość](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) w typach węzłów

## <a name="networking-configurations"></a>Konfiguracje sieci

Service Fabric zarządzane klastry są tworzone z domyślną konfiguracją sieci. Ta konfiguracja obejmuje obowiązkowe reguły dla najważniejszych funkcji klastra oraz kilka opcjonalnych reguł, które są przeznaczone do ułatwienia konfiguracji klienta.

Poza domyślną konfiguracją sieci można modyfikować reguły sieciowe w celu spełnienia potrzeb danego scenariusza. 

### <a name="nsg-rules-guidance"></a>Wskazówki dotyczące reguł sieciowej grupy zabezpieczeń

* Service Fabric zarządzanych klastrów rezerwuje priorytet reguły sieciowej grupy zabezpieczeń w zakresie od 0 do 999 w celu uzyskania istotnych funkcji. Nie można utworzyć niestandardowych reguł sieciowej grupy zabezpieczeń z wartością priorytetu mniejszą niż 1000. 
* Service Fabric klastry zarządzane rezerwują zakres priorytetów 3001 do 4000 podczas tworzenia opcjonalnych reguł sieciowej grupy zabezpieczeń. Te reguły są dodawane automatycznie, aby konfiguracje były szybkie i łatwe. Te reguły można przesłonić, dodając niestandardowe reguły sieciowej grupy zabezpieczeń w zakresie priorytetu 1000 do 3000. 
* Niestandardowe reguły sieciowej grupy zabezpieczeń powinny mieć priorytet w zakresie od 1000 do 3000. 


### <a name="apply-nsg-rules"></a>Zastosuj reguły sieciowej grupy zabezpieczeń

W przypadku klasycznych (niezarządzanych) klastrów Service Fabric należy zadeklarować oddzielny zasób *Microsoft. Network/networkSecurityGroups* i zarządzać nim w celu [zastosowania reguł sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) do klastra](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Service Fabric zarządzane klastry umożliwiają przypisanie reguł sieciowej grupy zabezpieczeń bezpośrednio w ramach zasobu klastra szablonu wdrożenia.

Aby przypisać reguły sieciowej grupy zabezpieczeń, użyj właściwości [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) zasobu *Microsoft. servicefabric/managedclusters* (wersja `2021-01-01-preview` lub nowsza). Na przykład:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>Porty RDP 

Service Fabric klastry zarządzane domyślnie nie zezwalają na dostęp do portów RDP. Porty RDP można otworzyć do Internetu, ustawiając następującą właściwość w Service Fabric zarządzanym zasobem klastra. 

```json
"allowRDPAccess": true 
```

Gdy właściwość allowRDPAccess ma wartość true, następująca reguła sieciowej grupy zabezpieczeń zostanie dodana do wdrożenia klastra.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>Porty ClientConnection i HttpGatewayConnection 

**Reguła sieciowej grupy zabezpieczeń: SFMC_AllowServiceFabricGatewayToSFRP** Zostanie dodana domyślna reguła sieciowej grupy zabezpieczeń, aby umożliwić dostawcy zasobów Service Fabric dostęp do clientConnectionPort i httpGatewayConnectionPort klastra. Ta reguła umożliwia dostęp do portów za pomocą tagu usługi "servicefabric".

>[!NOTE]
>Ta reguła jest zawsze dodawana i nie można jej zastąpić. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**Reguła sieciowej grupy zabezpieczeń: SFMC_AllowServiceFabricGatewayPorts** Jest to opcjonalna reguła sieciowej grupy zabezpieczeń zezwalająca na dostęp do clientConnectionPort i httpGatewayPort z Internetu. Ta reguła umożliwia klientom dostęp do usługi SFX, nawiązywanie połączenia z klastrem przy użyciu programu PowerShell oraz Service Fabric korzystanie z punktów końcowych interfejsu API klastra z zewnątrz. 

>[!NOTE]
>Ta reguła nie zostanie dodana, jeśli istnieje reguła niestandardowa mająca taki sam dostęp, kierunek i wartości protokołów dla tego samego portu. Tę regułę można zastąpić przy użyciu niestandardowych reguł sieciowej grupy zabezpieczeń. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Porty usługi równoważenia obciążenia 

Service Fabric zarządzanych klastrów Utwórz regułę sieciowej grupy zabezpieczeń w domyślnym zakresie priorytetu dla wszystkich portów LB skonfigurowanych w sekcji "loadBalancingRules" w obszarze właściwości ManagedCluster. Ta reguła umożliwia otwarcie portów LB dla ruchu przychodzącego z Internetu.  

>[!NOTE]
>Ta reguła jest dodawana w opcjonalnym zakresie priorytetów i można ją przesłonić, dodając niestandardowe reguły sieciowej grupy zabezpieczeń. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Sondy modułu równoważenia obciążenia

Service Fabric klastry zarządzane automatycznie tworzą sondy modułu równoważenia obciążenia dla portów bramy sieci szkieletowej, a także wszystkie porty skonfigurowane w sekcji "loadBalancingRules" właściwości zarządzanych klastrów.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Włącz automatyczne uaktualnianie obrazu systemu operacyjnego

Można włączyć automatyczne uaktualnienia obrazu systemu operacyjnego do maszyn wirtualnych, na których są uruchomione zarządzane węzły klastra. Mimo że zasoby zestawu skalowania maszyn wirtualnych są zarządzane w Twoim imieniu przy użyciu Service Fabric zarządzanych klastrów, wybór umożliwia włączenie automatycznych uaktualnień obrazów systemu operacyjnego dla węzłów klastra. Podobnie jak w przypadku [klasycznych klastrów Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) , zarządzane węzły klastra nie są domyślnie uaktualniane, aby zapobiec niezamierzonym zakłóceniom w klastrze.

Aby włączyć automatyczne uaktualnienia systemu operacyjnego:

* Użyj `2021-01-01-preview` wersji (lub nowszej) *Microsoft. servicefabric/Managedclusters* i *Microsoft. servicefabric/managedclusters/elementów NodeType* Resources
* Ustaw właściwość klastra `enableAutoOSUpgrade` na *wartość true*
* Ustaw właściwość zasobu elementów NodeType klastra na wartość `vmImageVersion` *Najnowsza*

Na przykład:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Po włączeniu Service Fabric rozpocznie wykonywanie zapytań i śledzenie wersji obrazu systemu operacyjnego w zarządzanym klastrze. Jeśli dostępna jest nowa wersja systemu operacyjnego, typy węzłów klastra (zestawy skalowania maszyn wirtualnych) zostaną uaktualnione pojedynczo. Uaktualnienia środowiska uruchomieniowego Service Fabric są wykonywane dopiero po potwierdzeniu braku uaktualnienia obrazu systemu operacyjnego węzła klastra.

Jeśli uaktualnienie nie powiedzie się, Service Fabric ponowi próbę po upływie 24 godzin. Podobnie jak w przypadku uaktualnień Service Fabric klasyczny (niezarządzany), aplikacje w złej kondycji lub węzły mogą blokować uaktualnienie obrazu systemu operacyjnego.

Aby uzyskać więcej informacji na temat uaktualnień obrazów, zobacz [automatyczne uaktualnienia obrazu systemu operacyjnego za pomocą zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Następne kroki

[Link do przykładowego szablonu]

[Omówienie zarządzanego klastra]
