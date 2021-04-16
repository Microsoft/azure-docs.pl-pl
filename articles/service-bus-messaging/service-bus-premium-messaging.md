---
title: Azure Service Bus warstwy Premium i Standardowa
description: W tym artykule opisano warstwy Standardowa i Premium Azure Service Bus. Porównuje te warstwy i zapewnia różnice techniczne.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389489"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Warstwy Premium i Standardowa komunikatów usługi Service Bus

Komunikaty usługi Service Bus, w tym jednostki, takie jak kolejki i tematy, stanowią połączenie możliwości obsługi komunikatów dla przedsiębiorstw oraz zaawansowanej semantyki publikowania/subskrybowania w skali chmury. Obsługa komunikatów usługi Service Bus pełni rolę szkieletu komunikacyjnego dla wielu zaawansowanych rozwiązań w chmurze.

Warstwa *Premium* usługi Service Bus Messaging odpowiada na typowe żądania klientów dotyczące skali, wydajności i dostępności dla aplikacji o znaczeniu krytycznym. Warstwa Premium jest zalecana do użytku w scenariuszach produkcyjnych. Mimo że zestawy funkcji są niemal identyczne, te dwie warstwy komunikatów usługi Service Bus są przeznaczone do różnych zastosowań.

W poniższej tabeli wyróżniono pewne ogólne różnice.

| Premium | Standardowa (Standard) |
| --- | --- |
| Wysoka przepływność |Zmienna przepływność |
| Przewidywalna wydajność |Zmienne opóźnienie |
| Stałe ceny |Zmienne ceny i płatność zgodnie z rzeczywistym użyciem |
| Możliwość skalowania obciążenia |Nie dotyczy |
| Rozmiar komunikatu do 1 MB. Ten limit może zostać podwyższony w przyszłości. Aby uzyskać najnowsze ważne aktualizacje usługi, zobacz [messaging on Azure blog (Obsługa komunikatów na platformie Azure).](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog) |Rozmiar komunikatu do 256 KB |

**Warstwa Premium komunikatów usługi Service Bus** zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany jednostką *obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2, 4, 8 lub 16 jednostek obsługi komunikatów dla każdej z Service Bus Premium. Pojedyncze obciążenie lub jednostka może obejmować wiele jednostek obsługi komunikatów, a liczbę jednostek obsługi komunikatów można zmienić w każdej chwili. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus.

Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania. Dzięki komunikatom w warstwie Premium maksymalna szybkość działania jest znacznie wyższa niż w przypadku warstwy Standardowa.

> [!NOTE]
> Limit rozmiaru partii dla komunikatów Premium wynosi 1 MB.

## <a name="premium-messaging-technical-differences"></a>Różnice techniczne dotyczące komunikatów w warstwie Premium

W poniższych sekcjach omówiono niektóre różnice między komunikatami w warstwie Premium i Standardowa.

### <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Partycjonowane kolejki i tematy nie są obsługiwane w przypadku komunikatów Premium. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partitioned queues and topics](service-bus-partitioning.md) (Partycjonowane kolejki i tematy).

### <a name="express-entities"></a>Jednostki ekspresowe

Ponieważ komunikaty Premium działają w izolowanym środowisku uruchomieniowym, jednostki ekspresowe nie są obsługiwane w przestrzeniach nazw Premium. Jednostka ekspresowa tymczasowo przechowuje komunikat w pamięci przed jego zapisem w magazynie trwałym. Jeśli masz kod uruchomiony w obszarze obsługi komunikatów w warstwie Standardowa i chcesz go zmienić na warstwę Premium, upewnij się, że funkcja jednostki ekspresowej jest wyłączona.

## <a name="premium-messaging-resource-usage"></a>Użycie zasobów komunikatów Premium
Ogólnie rzecz biorąc, każda operacja na jednostce może spowodować użycie procesora CPU i pamięci. Oto niektóre z tych operacji: 

- Operacje zarządzania, takie jak CRUD (tworzenie, pobieranie, aktualizowanie i usuwanie) w kolejkach, tematach i subskrypcjach.
- Operacje środowiska uruchomieniowego (wysyłanie i odbieranie komunikatów)
- Monitorowanie operacji i alertów

Dodatkowe użycie procesora CPU i pamięci nie jest jednak dodatkowo wycenione. W przypadku warstwy Premium obsługi komunikatów istnieje pojedyncza cena za jednostkę wiadomości.

Użycie procesora CPU i pamięci jest śledzone i wyświetlane z następujących powodów: 

- Zapewnianie przezroczystości wewnętrznych części systemu
- Dowiedz się, jaka jest pojemność zakupionych zasobów.
- Planowanie pojemności, które pomaga zdecydować się na skalowanie w górę/w dół.

## <a name="messaging-unit---how-many-are-needed"></a>Jednostka obsługi komunikatów — ile z nich jest potrzebnych?

Podczas aprowizowania Azure Service Bus Premium należy określić liczbę przydzielonych jednostek obsługi komunikatów. Te jednostki obsługi komunikatów są dedykowanymi zasobami przydzielonymi do przestrzeni nazw.

Liczbę jednostek obsługi komunikatów przydzielonych do przestrzeni  nazw Service Bus Premium można dynamicznie dostosować, aby wychować zmiany (zwiększyć lub zmniejszyć) w obciążeniach.

Podejmując decyzję o liczbie jednostek obsługi komunikatów dla architektury, należy wziąć pod uwagę kilka czynników:

- Rozpocznij od ***1 lub 2 jednostek*** obsługi komunikatów przydzielonych do przestrzeni nazw.
- Zbadaj metryki użycia procesora CPU w [obszarze Metryki użycia zasobów](service-bus-metrics-azure-monitor.md#resource-usage-metrics) dla przestrzeni nazw.
    - Jeśli użycie procesora CPU jest * poniżej **20%** _, możliwe jest skalowanie w dół *_*_* liczby jednostek obsługi komunikatów przydzielonych do przestrzeni nazw.
    - Jeśli użycie procesora CPU wynosi ***powyżej 70%** _, aplikacja będzie korzystać ze *_skalowania_* w górę * liczby jednostek obsługi komunikatów przydzielonych do przestrzeni nazw.

Aby dowiedzieć się, jak skonfigurować przestrzeń nazw Service Bus do automatycznego skalowania (zwiększania lub zmniejszenia liczby jednostek obsługi komunikatów), zobacz [Automatyczne aktualizowanie jednostek obsługi komunikatów.](automate-update-messaging-units.md)

> [!NOTE]
> **Skalowanie** zasobów przydzielonych do przestrzeni nazw może być wywłaszczym lub reaktywnym.
>
>  * **Wywłaszcz:** jeśli oczekiwane jest dodatkowe obciążenie (ze względu na sezonowość lub trendy), możesz przydzielić więcej jednostek obsługi komunikatów do przestrzeni nazw przed trafieniem obciążeń.
>
>  * **Reaktywne:** Jeśli dodatkowe obciążenia są identyfikowane przez badanie metryk użycia zasobów, można przydzielić dodatkowe zasoby do przestrzeni nazw w celu uwzględnienia rosnącego zapotrzebowania.
>
> Mierniki rozliczeń dla Service Bus są godzinowe. W przypadku skalowania w górę płacisz tylko za dodatkowe zasoby tylko za te godziny, które były używane.
>

## <a name="get-started-with-premium-messaging"></a>Wprowadzenie do obsługi komunikatów Premium

Rozpoczęcie pracy z obsługą komunikatów Premium jest proste, a proces jest podobny, jak w przypadku standardowej obsługi komunikatów. Rozpocznij od [utworzenia przestrzeni nazw](service-bus-create-namespace-portal.md) w witrynie [Azure Portal](https://portal.azure.com). W obszarze **Warstwa cenowa** wybierz pozycję **Premium**. Kliknij przycisk **Wyświetl pełne szczegóły ceny**, aby uzyskać więcej informacji o poszczególnych warstwach.

![create-premium-namespace][create-premium-namespace]

Możesz również tworzyć [przestrzenie nazw Premium za pomocą szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz poniższe linki:

- [Automatycznie aktualizuj jednostki obsługi komunikatów.](automate-update-messaging-units.md)
- [Introducing Azure Service Bus Premium messaging](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — wpis w blogu
- [Introducing Azure Service Bus Premium messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — Channel9

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
