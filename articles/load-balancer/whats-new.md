---
title: Co nowego w Azure Load Balancer
description: Dowiedz się, co nowego w Azure Load Balancer, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 8b44dc230dbee1b29b9889a1b81e35ebe25f6b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078692"
---
# <a name="whats-new-in-azure-load-balancer"></a>Co nowego w Azure Load Balancer?

Azure Load Balancer jest regularnie aktualizowana. Bądź na bieżąco z najnowszymi powiadomieniami. Ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje (jeśli dotyczy)

Możesz również znaleźć najnowsze aktualizacje Azure Load Balancer i subskrybować kanał informacyjny RSS [tutaj](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Najnowsze wersje

| Typ |Nazwa |Opis  |Data dodania  |
| ------ |---------|---------|---------|
| Cechy | Obsługa zarządzania puli zaplecza opartego na protokole IP (wersja zapoznawcza) | Azure Load Balancer obsługuje dodawanie i usuwanie zasobów z puli zaplecza za pośrednictwem adresów IPv4 lub IPv6. Dzięki temu można łatwo zarządzać kontenerami, maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych skojarzonymi z Load Balancer. Zezwala również na zastrzeżone adresy IP w ramach puli zaplecza przed utworzeniem skojarzonych zasobów. Więcej informacji można znaleźć [tutaj](backend-pool-management.md)|Lipiec 2020 r. |
| Cechy| Azure Load Balancer szczegółowych informacji przy użyciu Azure Monitor | W ramach Azure Monitor dla sieci klienci mają teraz mapy topologiczny dla wszystkich Load Balancer konfiguracji i pulpitów nawigacyjnych kondycji dla standardowych modułów równoważenia obciążenia wstępnie skonfigurowanych przy użyciu metryk w Azure Portal. [Rozpocznij pracę i Dowiedz się więcej](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Czerwiec 2020 r. |
| Walidacja | Dodawanie weryfikacji dla portów HA | Dodano weryfikację, aby upewnić się, że reguły portów HA i reguły portów inne niż HA są konfigurowane tylko wtedy, gdy jest włączony swobodny adres IP. Wcześniej ta konfiguracja przejdzie przez program, ale nie będzie działała zgodnie z oczekiwaniami. Nie wprowadzono zmian w funkcjonalności. Więcej informacji można znaleźć [tutaj](load-balancer-ha-ports-overview.md#limitations)| Czerwiec 2020 r. |
| Cechy| Obsługa protokołu IPv6 dla Azure Load Balancer (ogólnie dostępna) | Adresy IPv6 mogą być używane jako fronton dla usług równoważenia obciążenia platformy Azure. Dowiedz się, jak [utworzyć podwójną aplikację stosu](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Kwiecień 2020 r.|
| Cechy| Ruch przychodzący TCP po upływie limitu czasu bezczynności (ogólnie dostępny)| Użyj resetowania TCP, aby utworzyć bardziej przewidywalne zachowanie aplikacji. [Dowiedz się więcej](load-balancer-tcp-reset.md)| Luty 2020 r. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Load Balancer, zobacz [co to jest Azure Load Balancer?](load-balancer-overview.md) i [często zadawane pytania](load-balancer-faqs.md).
