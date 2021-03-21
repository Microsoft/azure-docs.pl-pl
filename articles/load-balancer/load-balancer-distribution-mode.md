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
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739920"
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
* **Adres IP klienta (koligacja źródłowego adresu IP — dwie kolekcje)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
* Adres **IP i protokół klienta (koligacja źródłowego adresu IP z trzema krotką)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie wybierz pozycję **Zapisz**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Zmień trwałość sesji w regule modułu równoważenia obciążenia." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Za pomocą programu PowerShell Zmień ustawienia dystrybucji modułu równoważenia obciążenia na istniejącą regułę równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Ustaw wartość `LoadDistribution` elementu dla typu wymaganego do równoważenia obciążenia. 

* Określ **SourceIP** dla dwóch krotek (źródłowy adres IP i docelowy adres IP). 

* Określ **SourceIPProtocol** dla funkcji równoważenia obciążenia dla trzech krotek (źródłowy adres IP, docelowy adres IP i typ protokołu). 

* Określ **wartość domyślną** dla zachowania domyślnego z pięcioma kolekcjami równoważenia obciążenia.

# <a name="cli"></a>[**Interfejs wiersza polecenia**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Użyj interfejsu wiersza polecenia platformy Azure, aby zmienić ustawienia dystrybucji modułu równoważenia obciążenia na istniejącą regułę równoważenia obciążenia.  Następujące polecenie aktualizuje tryb dystrybucji:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Ustaw wartość `--load-distribution` dla wymaganego typu równoważenia obciążenia.

* Określ **SourceIP** dla dwóch krotek (źródłowy adres IP i docelowy adres IP). 

* Określ **SourceIPProtocol** dla funkcji równoważenia obciążenia dla trzech krotek (źródłowy adres IP, docelowy adres IP i typ protokołu). 

* Określ **wartość domyślną** dla zachowania domyślnego z pięcioma kolekcjami równoważenia obciążenia.

Aby uzyskać więcej informacji na temat polecenia użytego w tym artykule, zobacz [AZ Network lb Rule Update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Load Balancer](load-balancer-overview.md)
* [Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-load-balancer-standard-public-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)