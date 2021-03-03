---
title: Konfigurowanie ustawień sieciowych dla Service Fabric klastrów zarządzanych (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować klaster zarządzany Service Fabric, aby sieciowej grupy zabezpieczeń reguły, dostęp portów protokołu RDP, reguły równoważenia obciążenia i nie tylko.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746990"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Konfigurowanie ustawień sieciowych dla Service Fabric klastrów zarządzanych (wersja zapoznawcza)

Service Fabric zarządzane klastry są tworzone z domyślną konfiguracją sieci. Ta konfiguracja obejmuje obowiązkowe reguły dla najważniejszych funkcji klastra oraz kilka opcjonalnych reguł, które są przeznaczone do ułatwienia konfiguracji klienta.

Poza domyślną konfiguracją sieci można modyfikować reguły sieciowe w celu spełnienia potrzeb danego scenariusza.

## <a name="nsg-rules-guidance"></a>Wskazówki dotyczące reguł sieciowej grupy zabezpieczeń

Należy pamiętać o następujących kwestiach podczas tworzenia nowych reguł sieciowej grupy zabezpieczeń dla zarządzanego klastra.

* Service Fabric zarządzanych klastrów rezerwuje priorytet reguły sieciowej grupy zabezpieczeń w zakresie od 0 do 999 w celu uzyskania istotnych funkcji. Nie można utworzyć niestandardowych reguł sieciowej grupy zabezpieczeń z wartością priorytetu mniejszą niż 1000.
* Service Fabric klastry zarządzane rezerwują zakres priorytetów 3001 do 4000 podczas tworzenia opcjonalnych reguł sieciowej grupy zabezpieczeń. Te reguły są dodawane automatycznie, aby konfiguracje były szybkie i łatwe. Te reguły można przesłonić, dodając niestandardowe reguły sieciowej grupy zabezpieczeń w zakresie priorytetu 1000 do 3000.
* Niestandardowe reguły sieciowej grupy zabezpieczeń powinny mieć priorytet w zakresie od 1000 do 3000.

## <a name="apply-nsg-rules"></a>Zastosuj reguły sieciowej grupy zabezpieczeń

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

## <a name="rdp-ports"></a>Porty RDP

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

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Porty ClientConnection i HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Reguła sieciowej grupy zabezpieczeń: SFMC_AllowServiceFabricGatewayToSFRP

Zostanie dodana domyślna reguła sieciowej grupy zabezpieczeń, aby umożliwić dostawcy zasobów Service Fabric dostęp do clientConnectionPort i httpGatewayConnectionPort klastra. Ta reguła umożliwia dostęp do portów za pomocą tagu usługi "servicefabric".

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

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Reguła sieciowej grupy zabezpieczeń: SFMC_AllowServiceFabricGatewayPorts

Jest to opcjonalna reguła sieciowej grupy zabezpieczeń zezwalająca na dostęp do clientConnectionPort i httpGatewayPort z Internetu. Ta reguła umożliwia klientom dostęp do usługi SFX, nawiązywanie połączenia z klastrem przy użyciu programu PowerShell oraz Service Fabric korzystanie z punktów końcowych interfejsu API klastra z zewnątrz. 

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

## <a name="load-balancer-ports"></a>Porty usługi równoważenia obciążenia

Service Fabric zarządzanych klastrów Utwórz regułę sieciowej grupy zabezpieczeń w domyślnym zakresie priorytetów dla wszystkich portów usługi równoważenia obciążenia skonfigurowanych w sekcji "loadBalancingRules" w obszarze właściwości *ManagedCluster* . Ta zasada umożliwia otwarcie portów LB dla ruchu przychodzącego z Internetu.  

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

## <a name="load-balancer-probes"></a>Sondy modułu równoważenia obciążenia

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

## <a name="next-steps"></a>Następne kroki

[Service Fabric opcje konfiguracji zarządzanych klastrów](how-to-managed-cluster-configuration.md)

[Omówienie Service Fabric zarządzanych klastrów](overview-managed-cluster.md)
