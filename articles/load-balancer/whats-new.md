---
title: Co nowego w Azure Load Balancer
description: Dowiedz się, co nowego w Azure Load Balancer, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148051"
---
# <a name="whats-new-in-azure-load-balancer"></a>Co nowego w Azure Load Balancer?

Azure Load Balancer jest regularnie aktualizowana. Bądź na bieżąco z najnowszymi powiadomieniami. Ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje (jeśli dotyczy)

Możesz również znaleźć najnowsze aktualizacje Azure Load Balancer i subskrybować kanał informacyjny RSS [tutaj](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="new-features"></a>Nowe funkcje

|Cecha  |Opis  |Data dodania  |
|---------|---------|---------|
| Obsługa zarządzania puli zaplecza opartego na protokole IP (wersja zapoznawcza) | Azure Load Balancer obsługuje dodawanie i usuwanie zasobów z puli zaplecza za pośrednictwem adresów IPv4 lub IPv6. Dzięki temu można łatwo zarządzać kontenerami, maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych skojarzonymi z Load Balancer. Zezwala również na zastrzeżone adresy IP w ramach puli zaplecza przed utworzeniem skojarzonych zasobów. | Lipiec 2020 r. |
| Azure Load Balancer szczegółowych informacji przy użyciu Azure Monitor | W ramach Azure Monitor dla sieci klienci mają teraz mapy topologiczny dla wszystkich Load Balancer konfiguracji i pulpitów nawigacyjnych kondycji dla standardowych modułów równoważenia obciążenia wstępnie skonfigurowanych przy użyciu metryk w Azure Portal. [Rozpocznij pracę i Dowiedz się więcej](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Czerwiec 2020 r. |
| Obsługa protokołu IPv6 dla Azure Load Balancer (ogólnie dostępna) | Adresy IPv6 mogą być używane jako fronton dla usług równoważenia obciążenia platformy Azure. Dowiedz się, jak [utworzyć podwójną aplikację stosu](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Kwiecień 2020 r.|
| Ruch przychodzący TCP po upływie limitu czasu bezczynności (ogólnie dostępny)| Użyj resetowania TCP, aby utworzyć bardziej przewidywalne zachowanie aplikacji. [Dowiedz się więcej](load-balancer-tcp-reset.md)| Luty 2020 r. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat Azure Load Balancer, zobacz [co to jest Azure Load Balancer?](load-balancer-overview.md) i [często zadawane pytania](load-balancer-faqs.md).