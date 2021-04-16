---
title: Konfigurowanie Azure Load Balancer dystrybucji
titleSuffix: Azure Load Balancer
description: W tym artykule rozpoczynasz konfigurowanie trybu dystrybucji dla Azure Load Balancer do obsługi koligacji źródłowego adresu IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483643"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurowanie trybu dystrybucji dla Azure Load Balancer

Azure Load Balancer obsługuje dwa tryby dystrybucji do dystrybucji ruchu do aplikacji:

* Oparte na skrótach
* Koligacja źródłowego adresu IP

Z tego artykułu dowiesz się, jak skonfigurować tryb dystrybucji dla Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Konfigurowanie trybu dystrybucji

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Konfigurację trybu dystrybucji można zmienić, modyfikując regułę równoważenia obciążenia w portalu.

1. Zaloguj się do Azure Portal zasobów i znajdź grupę zasobów zawierającą równoważenie obciążenia, które chcesz zmienić, klikając pozycję **Grupy zasobów.**
2. Na ekranie przeglądu równoważenia obciążenia wybierz pozycję **Reguły równoważenia** obciążenia w obszarze **Ustawienia**.
3. Na ekranie reguły równoważenia obciążenia wybierz regułę równoważenia obciążenia, dla których chcesz zmienić tryb dystrybucji.
4. W ramach reguły tryb dystrybucji jest zmieniany przez zmianę **pola** listy rozwijanej Trwałość sesji. 

Dostępne są następujące opcje: 

* **Brak (oparty na skrótach)** — określa, że kolejne żądania od tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.
* **Adres IP klienta (dwuplejowa koligacja** źródłowego adresu IP) — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
* **Adres IP klienta i** protokół (trzy krotka źródłowego koligacji IP) — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołu będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie wybierz pozycję **Zapisz.**

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Zmiana trwałości sesji dla reguły równoważenia obciążenia." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj programu PowerShell, aby zmienić ustawienia dystrybucji usługi równoważenia obciążenia dla istniejącej reguły równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Ustaw wartość elementu `LoadDistribution` dla typu wymaganego równoważenia obciążenia. 

* Określ **wartość SourceIP** dla równoważenia obciążenia dla dwóch krotek (źródłowy adres IP i docelowy adres IP). 

* Określ **sourceIPProtocol** dla trzech krotki (źródłowy adres IP, docelowy adres IP i typ protokołu) równoważenia obciążenia. 

* Określ **domyślne** zachowanie równoważenia obciążenia krotki z pięcioma krotami.

# <a name="cli"></a>[**Interfejs wiersza polecenia**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Użyj interfejsu wiersza polecenia platformy Azure, aby zmienić ustawienia dystrybucji usługi równoważenia obciążenia dla istniejącej reguły równoważenia obciążenia.  Następujące polecenie aktualizuje tryb dystrybucji:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Ustaw wartość `--load-distribution` dla typu wymaganego równoważenia obciążenia.

* Określ **wartość SourceIP** dla równoważenia obciążenia dla krotki dwuliniowej (źródłowy adres IP i docelowy adres IP). 

* Określ **sourceIPProtocol** dla trzech krotki (źródłowy adres IP, docelowy adres IP i typ protokołu) równoważenia obciążenia. 

* Określ **domyślne** zachowanie równoważenia obciążenia krotki z pięcioma krotami.

Aby uzyskać więcej informacji na temat polecenia używanego w tym artykule, zobacz [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Load Balancer](load-balancer-overview.md)
* [Wprowadzenie do konfigurowania internetowego usługi równoważenia obciążenia](quickstart-load-balancer-standard-public-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
