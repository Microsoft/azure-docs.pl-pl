---
title: Konfigurowanie trybu dystrybucji Azure Load Balancer
titleSuffix: Azure Load Balancer
description: W tym artykule Rozpocznij Konfigurowanie trybu dystrybucji dla Azure Load Balancer w celu obsługi koligacji źródłowego adresu IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 7f2525b89f03e8bc1a2c3166b46c40b4dbb6ff17
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99562004"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Skonfiguruj tryb dystrybucji dla Azure Load Balancer

Azure Load Balancer obsługuje dwa tryby dystrybucji do dystrybucji ruchu do aplikacji:

* Oparte na skrótach
* Koligacja źródłowego adresu IP

W tym artykule dowiesz się, jak skonfigurować tryb dystrybucji dla Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Konfigurowanie trybu dystrybucji

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Konfigurację trybu dystrybucji można zmienić, modyfikując regułę równoważenia obciążenia w portalu.

1. Zaloguj się do Azure Portal i Znajdź grupę zasobów zawierającą moduł równoważenia obciążenia, który chcesz zmienić, klikając pozycję **grupy zasobów**.
2. Na ekranie przegląd usługi równoważenia obciążenia wybierz pozycję **reguły równoważenia obciążenia** w obszarze **Ustawienia**.
3. Na ekranie reguły równoważenia obciążenia wybierz regułę równoważenia obciążenia, w której chcesz zmienić tryb dystrybucji.
4. W ramach zasady tryb dystrybucji jest zmieniany, zmieniając pole listy rozwijanej **trwałość sesji** . 

Dostępne są następujące opcje: 

* **Brak (oparte na skrócie)** — określa, że kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.
* **Adres IP klienta (koligacja źródłowego adresu IP 2-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
* **IP i protokół klienta (koligacja źródłowego adresu IP 3-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie wybierz pozycję **Zapisz**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Zmień trwałość sesji w regule modułu równoważenia obciążenia." border="true":::


# <a name="powershell"></a>[**Program PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Za pomocą programu PowerShell Zmień ustawienia dystrybucji modułu równoważenia obciążenia na istniejącą regułę równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Ustaw wartość `LoadDistribution` elementu dla wymaganej ilości usługi równoważenia obciążenia. 

Określ **sourceIP** dla dwóch krotek (źródłowy adres IP i docelowy adres IP). 

Określ **sourceIPProtocol** dla funkcji równoważenia obciążenia dla trzech krotek (źródłowy adres IP, docelowy adres IP i typ protokołu). 

Określ **wartość domyślną** dla zachowania domyślnego z pięcioma kolekcjami równoważenia obciążenia.

---

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Load Balancer](load-balancer-overview.md)
* [Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-load-balancer-standard-public-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)