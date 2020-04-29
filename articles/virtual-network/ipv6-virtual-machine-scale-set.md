---
title: Wdrażanie zestawów skalowania maszyn wirtualnych przy użyciu protokołu IPv6 na platformie Azure
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania zestawów skalowania maszyn wirtualnych przy użyciu protokołu IPv6 w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 6a751fa193c8dd530707f790af0292d536a6f47d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420456"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Wdrażanie zestawów skalowania maszyn wirtualnych przy użyciu protokołu IPv6 na platformie Azure

W tym artykule pokazano, jak wdrożyć zestaw skalowania maszyn wirtualnych z podwójnym stosem (IPv4 + IPv6) z podwójnym zewnętrznym modułem równoważenia obciążenia w sieci wirtualnej platformy Azure. Proces tworzenia zestawu skalowania maszyn wirtualnych z obsługą protokołu IPv6 jest niemal identyczny z procesem tworzenia poszczególnych maszyn wirtualnych opisanych w [tym miejscu](ipv6-configure-standard-load-balancer-template-json.md). Zacznij od kroków, które są podobne do tych opisanych dla poszczególnych maszyn wirtualnych:
1.    Tworzenie publicznych adresów IP IPv4 i IPv6.
2.    Utwórz moduł równoważenia obciążenia podwójnego stosu.  
3.    Utwórz reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).  

Jedyną czynnością, która różni się od poszczególnych maszyn wirtualnych, jest utworzenie konfiguracji interfejsu sieciowego (NIC) używającej zasobu zestawu skalowania maszyn wirtualnych: networkProfile/networkInterfaceConfigurations. Struktura JSON jest podobna do tego obiektu Microsoft. Network/networkInterfaces używanego w przypadku poszczególnych maszyn wirtualnych z dodaniem ustawienia karty sieciowej i elementu IpConfiguration protokołu IPv4 jako podstawowego interfejsu przy użyciu atrybutu **"Primary": true** , jak pokazano w następującym przykładzie:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Przykładowy kod JSON szablonu zestawu skalowania maszyn wirtualnych

Aby wdrożyć zestaw skalowania maszyn wirtualnych z podwójnym stosem (IPv4 + IPv6) z podwójnym szablonem Load Balancer zewnętrzna i widok sieci wirtualnej w [tym miejscu](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o obsłudze protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla platformy azure Virtual Network?](ipv6-overview.md).
