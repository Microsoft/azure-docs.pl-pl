---
title: Konfigurowanie portów wysokiej dostępności dla Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak używać portów o wysokiej dostępności do równoważenia obciążenia ruchu wewnętrznego na wszystkich portach
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: a71a01629f334534d8eb26847a8f3400efbbeafe
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807838"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurowanie portów wysokiej dostępności dla wewnętrznego modułu równoważenia obciążenia

Ten artykuł zawiera przykładowe wdrożenie portów wysokiej dostępności dla wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfiguracji specyficznych dla wirtualnych urządzeń sieciowych (urządzeń WUS), zobacz odpowiednie witryny sieci Web dostawcy.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługa Load Balancer w warstwie Standardowa. Aby uzyskać więcej informacji na temat podstawowych Load Balancer, zobacz [Load Balancer Overview](load-balancer-overview.md).

Ilustracja przedstawia następującą konfigurację przykładu wdrożenia opisanego w tym artykule:

- Urządzeń WUS są wdrażane w puli zaplecza wewnętrznego modułu równoważenia obciążenia za konfiguracją portów o wysokiej dostępności. 
- Trasa zdefiniowana przez użytkownika (UDR) zastosowana w podsieci DMZ kieruje cały ruch do urządzeń WUS, naciskając następny przeskok jako wirtualny adres IP wewnętrznego modułu równoważenia obciążenia. 
- Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do jednego z aktywnych urządzeń WUS zgodnie z algorytmem modułu równoważenia obciążenia.
- URZĄDZENIE WUS przetwarza ruch i przekazuje go do oryginalnego miejsca docelowego w podsieci zaplecza.
- Ścieżka zwracana może przyjmować tę samą trasę, jeśli w podsieci zaplecza jest skonfigurowana odpowiednia UDR. 

![Przykładowe wdrożenie portów o wysokiej dostępności](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurowanie portów wysokiej dostępności

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skonfigurować porty wysokiej dostępności, skonfiguruj wewnętrzny moduł równoważenia obciążenia za pomocą urządzeń WUS w puli zaplecza. Skonfigurowanie odpowiedniej konfiguracji sondowania kondycji modułu równoważenia obciążenia w celu wykrycia urządzenie WUS kondycji i reguły modułu równoważenia obciążenia z portami o wysokiej dostępności. Ogólna konfiguracja związana z usługą równoważenia obciążenia została omówiona w temacie [wprowadzenie](load-balancer-get-started-ilb-arm-portal.md). W tym artykule przedstawiono konfigurację portów o wysokiej dostępności.

Konfiguracja zasadniczo wymaga ustawienia portu frontonu i wartości portu zaplecza na **0**. Ustaw wartość protokołu na **wszystkie**. W tym artykule opisano sposób konfigurowania portów wysokiej dostępności przy użyciu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Skonfiguruj regułę modułu równoważenia obciążenia dla portów o wysokiej dostępności przy użyciu Azure Portal

Aby skonfigurować porty o wysokiej dostępności przy użyciu Azure Portal, zaznacz pole wyboru **porty ha** . W przypadku wybrania tej konfiguracji pokrewny port i protokół są wypełniane automatycznie. 

![Konfiguracja portów wysokiej dostępności za pośrednictwem Azure Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurowanie reguły równoważenia obciążenia portów o wysokiej dostępności za pośrednictwem szablonu Menedżer zasobów

Porty o wysokiej dostępności można skonfigurować przy użyciu wersji interfejsu API 2017-08-01 dla elementu Microsoft. Network/loadBalancers w zasobie Load Balancer. Poniższy fragment kodu JSON ilustruje zmiany w konfiguracji modułu równoważenia obciążenia dla portów o wysokiej dostępności za pośrednictwem interfejsu API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurowanie reguły modułu równoważenia obciążenia dla portów o wysokiej dostępności przy użyciu programu PowerShell

Użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia dla portów o wysokiej dostępności podczas tworzenia wewnętrznego modułu równoważenia obciążenia za pomocą programu PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurowanie reguły modułu równoważenia obciążenia dla portów o wysokiej dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

W kroku 4 [Tworzenie zestawu wewnętrznego modułu równoważenia obciążenia](load-balancer-get-started-ilb-arm-cli.md)Użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia dla portów o wysokiej dostępności:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [portach o wysokiej dostępności](load-balancer-ha-ports-overview.md).