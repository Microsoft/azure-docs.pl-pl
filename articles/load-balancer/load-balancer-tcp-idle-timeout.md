---
title: Konfigurowanie limitu czasu bezczynności protokołu TCP modułu równoważenia obciążenia na platformie Azure
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
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 374ec9daf6255a0a05ed9b2f03cc01b90785493c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628183"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Skonfiguruj ustawienia limitu czasu bezczynności protokołu TCP dla Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="tcp-idle-timeout"></a>Limit czasu bezczynności protokołu TCP
Azure Load Balancer ma ustawienie limitu czasu bezczynności wynoszące 4 minuty do 30 minut. Wartość domyślna to 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą w chmurze.

Gdy połączenie zostanie zamknięte, aplikacja kliencka może otrzymać następujący komunikat o błędzie: "Połączenie podstawowe zostało zamknięte: połączenie, które było oczekiwać aktywności, zostało zamknięte przez serwer".

Typowym zastosowaniem jest utrzymywanie aktywności protokołu TCP. Ta metoda utrzymuje, że połączenie jest aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady dla platformy .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Gdy włączona jest funkcja Keep-Alive, pakiety są wysyłane w trakcie okresów braku aktywności w ramach połączenia. Pakiety Keep-Alive zapewniają, że wartość limitu czasu bezczynności nie zostanie osiągnięta, a połączenie jest utrzymywane przez długi czas.

Ustawienie działa tylko dla połączeń przychodzących. Aby uniknąć utraty połączenia, skonfiguruj wartość "Keep-Alive TCP" z interwałem mniejszym niż ustawienie limitu czasu bezczynności lub Zwiększ limit czasu bezczynności. Aby można było obsługiwać te scenariusze, dodano obsługę limitu czasu bezczynności, który można skonfigurować.

Utrzymywanie aktywności TCP działa w scenariuszach, w których czas pracy baterii nie jest ograniczeniem. Nie jest to zalecane w przypadku aplikacji mobilnych. Korzystanie z funkcji utrzymywania połączenia TCP w aplikacji mobilnej umożliwia szybsze opróżnianie baterii urządzenia.

![Limit czasu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

W poniższych sekcjach opisano, jak zmienić ustawienia limitu czasu bezczynności dla zasobów publicznego adresu IP i modułu równoważenia obciążenia.

>[!NOTE]
> Limit czasu bezczynności protokołu TCP nie ma wpływu na reguły równoważenia obciążenia w protokole UDP.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Skonfiguruj limit czasu protokołu TCP dla publicznego adresu IP na poziomie wystąpienia na 15 minut

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślny limit czasu wynosi 4 minuty. Akceptowalny zakres limitu czasu wynosi od 4 do 120 minut.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Ustaw limit czasu TCP dla reguły ze zrównoważonym obciążeniem na 15 minut

Aby ustawić limit czasu bezczynności dla modułu równoważenia obciążenia, wartość "IdleTimeoutInMinutes" jest ustawiana dla reguły ze zrównoważonym obciążeniem. Na przykład:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Następne kroki

[Przegląd wewnętrznego modułu równoważenia obciążenia](load-balancer-internal-overview.md)

[Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-load-balancer-standard-public-powershell.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
