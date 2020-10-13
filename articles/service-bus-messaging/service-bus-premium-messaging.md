---
title: Azure Service Bus warstwach Premium i Standard
description: W tym artykule opisano warstwy Standard i Premium Azure Service Bus. Porównuje te warstwy i zapewnia różnice techniczne.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 31c53a1375078cd5d185945cba55a6e5a6dd5ffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90966793"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Warstwy Premium i Standardowa komunikatów usługi Service Bus

Komunikaty usługi Service Bus, w tym jednostki, takie jak kolejki i tematy, stanowią połączenie możliwości obsługi komunikatów dla przedsiębiorstw oraz zaawansowanej semantyki publikowania/subskrybowania w skali chmury. Obsługa komunikatów usługi Service Bus pełni rolę szkieletu komunikacyjnego dla wielu zaawansowanych rozwiązań w chmurze.

Warstwa *Premium* Service Bus komunikatów rozwiązuje typowe żądania klientów dotyczące skali, wydajności i dostępności dla aplikacji o krytycznym znaczeniu. Warstwa Premium jest zalecana do użytku w scenariuszach produkcyjnych. Mimo że zestawy funkcji są niemal identyczne, te dwie warstwy komunikatów usługi Service Bus są przeznaczone do różnych zastosowań.

W poniższej tabeli wyróżniono pewne ogólne różnice.

| Premium | Standardowa |
| --- | --- |
| Wysoka przepływność |Zmienna przepływność |
| Przewidywalna wydajność |Zmienne opóźnienie |
| Stałe ceny |Zmienne ceny i płatność zgodnie z rzeczywistym użyciem |
| Możliwość skalowania obciążenia |Nie dotyczy |
| Rozmiar komunikatu do 1 MB. Ten limit może być zgłaszany w przyszłości. Najnowsze ważne aktualizacje usługi znajdują się w temacie [Obsługa komunikatów w blogu platformy Azure](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Rozmiar komunikatu do 256 KB |

**Warstwa Premium komunikatów usługi Service Bus** zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany *jednostką obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Dla każdej przestrzeni nazw Premium Service Bus można zakupić 1, 2, 4 lub 8 jednostek obsługi komunikatów. Pojedyncze obciążenie lub jednostka mogą obejmować wiele jednostek obsługi komunikatów, a liczba jednostek obsługi komunikatów w programie może zostać zmieniona. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus.

Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania. Service Bus w warstwie Premium kompilacje w aparacie magazynu wprowadzonym w [usłudze Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Dzięki komunikatom w warstwie Premium maksymalna szybkość działania jest znacznie wyższa niż w przypadku warstwy Standardowa.

## <a name="premium-messaging-technical-differences"></a>Różnice techniczne dotyczące komunikatów w warstwie Premium

W poniższych sekcjach omówiono niektóre różnice między komunikatami w warstwie Premium i Standardowa.

### <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Partycjonowane kolejki i tematy nie są obsługiwane w przypadku komunikatów w warstwie Premium. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partitioned queues and topics](service-bus-partitioning.md) (Partycjonowane kolejki i tematy).

### <a name="express-entities"></a>Jednostki ekspresowe

Ponieważ obsługa komunikatów w warstwie Premium działa w izolowanym środowisku uruchomieniowym, jednostki ekspresowe nie są obsługiwane w przestrzeniach nazw w warstwie Premium. Aby uzyskać więcej informacji o funkcji jednostek ekspresowych, zobacz opis właściwości [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Jeśli masz kod uruchomiony w ramach obsługi komunikatów w warstwie Standardowa i chcesz przenieść go do warstwy Premium, upewnij się, że właściwość [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) jest ustawiona na wartość **false** (wartość domyślna).

## <a name="premium-messaging-resource-usage"></a>Użycie zasobów obsługi komunikatów w warstwie Premium
Ogólnie rzecz biorąc, każda operacja w jednostce może spowodować użycie procesora CPU i pamięci. Oto niektóre z tych operacji: 

- Operacje zarządzania, takie jak CRUD (tworzenie, pobieranie, aktualizowanie i usuwanie) operacji w kolejkach, tematach i subskrypcjach.
- Operacje środowiska uruchomieniowego (wysyłanie i odbieranie komunikatów)
- Operacje monitorowania i alerty

Dodatkowe użycie procesora i pamięci nie jest tańsze. W przypadku warstwy obsługi komunikatów Premium dostępna jest pojedyncza cena dla jednostki wiadomości.

Użycie procesora CPU i pamięci jest śledzone i wyświetlane z następujących powodów: 

- Zapewnianie przejrzystości systemu
- Zapoznaj się z pojemnością zakupionych zasobów.
- Planowanie pojemności, które pomaga zdecydować się na skalowanie w górę/w dół.

## <a name="messaging-unit---how-many-are-needed"></a>Jednostka obsługi komunikatów — ile jest potrzebnych?

W przypadku inicjowania obsługi przestrzeni nazw Premium Azure Service Bus należy określić liczbę jednostek obsługi komunikatów przydzieloną. Te jednostki obsługi komunikatów to dedykowane zasoby, które są przydzielane do przestrzeni nazw.

Liczba jednostek obsługi komunikatów przypisywanych do przestrzeni nazw Premium Service Bus może być **dynamicznie dostosowywana** do współczynnika zmian (zwiększa lub zmniejsza) w obciążeniach.

Istnieje kilka czynników, które należy wziąć pod uwagę podczas decydowania o liczbie jednostek obsługi komunikatów dla architektury:

- Zacznij od ***1 lub 2 jednostek obsługi komunikatów*** przypisywanych do przestrzeni nazw.
- Zbadaj metryki użycia procesora CPU w ramach [metryk użycia zasobów](service-bus-metrics-azure-monitor.md#resource-usage-metrics) dla przestrzeni nazw.
    - Jeśli użycie procesora CPU jest ***poniżej 20%***, można ***skalować*** liczbę jednostek obsługi komunikatów przypisywanych do przestrzeni nazw.
    - Jeśli użycie procesora CPU jest ***powyżej 70%***, aplikacja będzie korzystać z ***skalowania w górę*** liczby jednostek obsługi komunikatów przypisywanych do przestrzeni nazw.

Aby dowiedzieć się, jak skonfigurować Service Bus przestrzeń nazw w celu automatycznego skalowania (zwiększać lub zmniejszać liczbę jednostek obsługi komunikatów), zobacz [Automatyczne aktualizowanie jednostek obsługi komunikatów](automate-update-messaging-units.md).

> [!NOTE]
> **Skalowanie** zasobów przyznanych do przestrzeni nazw może być zastępują lub ponownie aktywne.
>
>  * **Zastępujący**: Jeśli oczekiwane jest dodatkowe obciążenie (ze względu na sezonowości lub trendy), można przydzielić więcej jednostek obsługi komunikatów do przestrzeni nazw przed trafieniem obciążeń.
>
>  * **Reaktywny**: Jeśli dodatkowe obciążenia są identyfikowane przez analizowanie metryk użycia zasobów, dodatkowe zasoby mogą być przydzielone do przestrzeni nazw w celu uwzględnienia rosnącego zapotrzebowania.
>
> Liczniki rozliczeń dla Service Bus są co godzinę. W przypadku skalowania w górę opłaty są napłacone tylko za dodatkowe zasoby w godzinach, w których były używane.
>

## <a name="get-started-with-premium-messaging"></a>Wprowadzenie do obsługi komunikatów Premium

Rozpoczęcie pracy z obsługą komunikatów Premium jest proste, a proces jest podobny, jak w przypadku standardowej obsługi komunikatów. Rozpocznij od [utworzenia przestrzeni nazw](service-bus-create-namespace-portal.md) w witrynie [Azure Portal](https://portal.azure.com). W obszarze **Warstwa cenowa** wybierz pozycję **Premium**. Kliknij przycisk **Wyświetl pełne szczegóły ceny**, aby uzyskać więcej informacji o poszczególnych warstwach.

![create-premium-namespace][create-premium-namespace]

Możesz również tworzyć [przestrzenie nazw Premium za pomocą szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz poniższe linki:

- [Automatycznie Aktualizuj jednostki obsługi komunikatów](automate-update-messaging-units.md).
- [Introducing Azure Service Bus Premium messaging](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — wpis w blogu
- [Introducing Azure Service Bus Premium messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Wprowadzenie do komunikatów usługi Service Bus w warstwie Premium) — Channel9

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
