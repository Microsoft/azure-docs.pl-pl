---
title: Konfigurowanie limitu czasu bezczynności resetowania protokołu TCP modułu równoważenia obciążenia na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować Azure Load Balancer limit czasu bezczynności protokołu TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649868"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Skonfiguruj limit czasu bezczynności protokołu TCP dla Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Azure Load Balancer ma ustawienie limitu czasu bezczynności wynoszące 4 minuty do 120 minut. Wartość domyślna to 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą w chmurze. Dowiedz się więcej o [limicie czasu bezczynności protokołu TCP](load-balancer-tcp-reset.md).

W poniższych sekcjach opisano, jak zmienić ustawienia limitu czasu bezczynności dla zasobów publicznego adresu IP i modułu równoważenia obciążenia.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Konfigurowanie limitu czasu bezczynności protokołu TCP dla publicznego adresu IP

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślny limit czasu wynosi 4 minuty. Akceptowalny zakres limitu czasu wynosi od 4 do 120 minut.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Ustaw limit czasu bezczynności protokołu TCP dla reguł

Aby ustawić limit czasu bezczynności dla modułu równoważenia obciążenia, wartość "IdleTimeoutInMinutes" jest ustawiana dla reguły ze zrównoważonym obciążeniem. Na przykład:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Następne kroki

[Przegląd wewnętrznego modułu równoważenia obciążenia](load-balancer-internal-overview.md)

[Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-load-balancer-standard-public-powershell.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
