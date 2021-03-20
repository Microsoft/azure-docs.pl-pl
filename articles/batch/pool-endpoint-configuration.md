---
title: Konfigurowanie punktów końcowych węzła w puli Azure Batch
description: Jak skonfigurować lub wyłączyć dostęp do portów SSH lub RDP w węzłach obliczeniowych w puli Azure Batch.
ms.topic: how-to
ms.date: 02/13/2018
ms.openlocfilehash: 4e7df7da539be75ef1befdff4b4e1fe5244c1702
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109310"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli Azure Batch

Domyślnie zadanie wsadowe umożliwia [użytkownikowi węzła](/rest/api/batchservice/computenode/adduser) z łącznością sieciową łączenie się zewnętrznie z węzłem obliczeniowym w puli usługi Batch. Na przykład użytkownik może nawiązać połączenie za pomocą Pulpit zdalny (RDP) na porcie 3389 do węzła obliczeniowego w puli systemu Windows. Analogicznie, domyślnie użytkownik może łączyć się za pomocą Secure Shell (SSH) na porcie 22 z węzłem obliczeniowym w puli systemu Linux. 

W danym środowisku może być konieczne ograniczenie lub wyłączenie tych domyślnych ustawień dostępu zewnętrznego. Te ustawienia można zmodyfikować przy użyciu interfejsów API usługi Batch, aby ustawić właściwość [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) . 

## <a name="about-the-pool-endpoint-configuration"></a>Informacje o konfiguracji punktu końcowego puli
Konfiguracja punktu końcowego składa się z co najmniej jednej [puli translacji adresów sieciowych (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) portów frontonu. (Nie należy mylić puli NAT z pulą wsadową węzłów obliczeniowych). Należy skonfigurować każdą pulę NAT, aby zastąpić domyślne ustawienia połączenia w węzłach obliczeniowych puli. 

Każda konfiguracja puli NAT zawiera co najmniej jedną [regułę sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Każda reguła sieciowej grupy zabezpieczeń umożliwia lub odrzuca określony ruch sieciowy do punktu końcowego. Można wybrać Zezwalanie na ruch, ruch identyfikowany przez [tag usługi](../virtual-network/network-security-groups-overview.md#service-tags) (na przykład "Internet") lub blokowanie ruchu z określonych adresów IP lub podsieci.

### <a name="considerations"></a>Zagadnienia do rozważenia
* Konfiguracja punktu końcowego puli jest częścią [konfiguracji sieci](/rest/api/batchservice/pool/add#networkconfiguration)puli. Konfiguracja sieci może opcjonalnie obejmować ustawienia umożliwiające dołączenie puli do [sieci wirtualnej platformy Azure](batch-virtual-network.md). W przypadku skonfigurowania puli w sieci wirtualnej można utworzyć reguły sieciowej grupy zabezpieczeń, które używają ustawień adresu w sieci wirtualnej.
* Podczas konfigurowania puli NAT można skonfigurować wiele reguł sieciowej grupy zabezpieczeń. Reguły są sprawdzane w kolejności priorytetów. Gdy reguła ma zastosowanie, żadne inne reguły nie są sprawdzane pod kątem dopasowania.


## <a name="example-deny-all-rdp-traffic"></a>Przykład: Odmów całego ruchu RDP

Poniższy fragment kodu w języku C# pokazuje, jak skonfigurować punkt końcowy protokołu RDP w węzłach obliczeniowych w puli systemu Windows, aby odmówić całego ruchu sieciowego. Punkt końcowy używa puli frontonu portów w zakresie *60000 – 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Przykład: Odrzuć cały ruch SSH z Internetu

Poniższy fragment kodu w języku Python pokazuje, jak skonfigurować punkt końcowy SSH w węzłach obliczeniowych w puli systemu Linux, aby odmówić całego ruchu internetowego. Punkt końcowy używa puli frontonu portów w zakresie *4000 – 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Przykład: Zezwalaj na ruch RDP z określonego adresu IP

Poniższy fragment kodu w języku C# przedstawia sposób konfigurowania punktu końcowego protokołu RDP w węzłach obliczeniowych w puli systemu Windows w celu zezwalania na dostęp do protokołu RDP tylko z adresu IP *198.51.100.7*. Druga reguła sieciowej grupy zabezpieczeń odmówi ruch, który nie jest zgodny z adresem IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Przykład: Zezwalaj na ruch SSH z określonej podsieci

Poniższy fragment kodu w języku Python przedstawia sposób konfigurowania punktu końcowego SSH w węzłach obliczeniowych w puli systemu Linux w celu zezwalania na dostęp tylko z podsieci *192.168.1.0/24*. Druga reguła sieciowej grupy zabezpieczeń odmówi ruch, który nie jest zgodny z podsiecią.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń na platformie Azure, zobacz [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).