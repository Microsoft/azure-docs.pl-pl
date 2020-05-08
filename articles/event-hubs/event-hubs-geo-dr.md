---
title: Replikacja geograficzna — odzyskiwanie po awarii — Event Hubs platformy Azure | Microsoft Docs
description: Jak używać regionów geograficznych do przełączania do trybu failover i odzyskiwania po awarii na platformie Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2c42637dda9d1a413c0521ea2d7565a63ca58e81
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858290"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs — odzyskiwanie geograficzne 

W przypadku awarii całego regionu platformy Azure lub centrów danych (jeśli nie są używane [strefy dostępności](../availability-zones/az-overview.md) ) nie ma możliwości zapewnienia przestoju w celu przeprowadzenia działania w innym regionie lub w centrum. W związku z tym *odzyskiwanie geograficzne* i *replikacja geograficzna* są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwanie geograficzne po awarii, jak i replikację geograficzną, na poziomie przestrzeni nazw. 

> [!NOTE]
> Funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowej i dedykowanej jednostki SKU](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Awarie i katastrofy

Ważne jest, aby zauważyć rozróżnienie między "awariami" i "katastrofami". *Awaria* to tymczasowa niedostępność usługi Azure Event Hubs i może mieć wpływ na niektóre składniki usługi, takie jak magazyn komunikatów, a nawet całe centrum danych. Jednak po rozwiązaniu problemu Event Hubs znów będzie dostępny. Zazwyczaj awaria nie powoduje utraty komunikatów ani innych danych. Przykładem takiego przestoju może być awaria w centrum danych. Niektóre przestoje są tylko krótkimi stratami połączenia z powodu problemów przejściowych lub sieciowych. 

*Awaria* jest definiowana jako trwała lub w długim czasie utrata Event Hubs klastra, regionu platformy Azure lub centrum danych. Region lub centrum danych może lub nie staje się dostępne ponownie lub mogą być wyłączone w godzinach lub dniach. Przykładami takich awarii są pożary, powodziowe lub ziemie. Awaria, która stała się trwała, może spowodować utratę niektórych komunikatów, zdarzeń lub innych danych. Jednak w większości przypadków nie powinno się odzyskać utraty danych, a komunikaty mogą być odzyskiwane po utworzeniu kopii zapasowej centrum danych.

Funkcja odzyskiwania po awarii geograficznej platformy Azure Event Hubs to rozwiązanie do odzyskiwania po awarii. Koncepcje i przepływ pracy opisane w tym artykule mają zastosowanie do scenariuszy awaryjnych, a nie przejściowych lub tymczasowych przestojów. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i postanowienia

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych przestrzeniach nazw odzyskiwania po awarii. 

Funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nie musisz wprowadzać żadnych zmian parametrów połączenia, ponieważ połączenie jest nawiązywane za pośrednictwem aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zawiera pojedyncze stałe parametry połączenia w pełni kwalifikowanej nazwy domeny (FQDN). Aplikacje używają tego ciągu połączenia z aliasem do łączenia się z przestrzenią nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw*: przestrzenie nazw, które odpowiadają aliasu. Podstawowa przestrzeń nazw jest "aktywna" i odbiera komunikaty (może to być istniejąca lub Nowa przestrzeń nazw). Pomocnicza przestrzeń nazw jest "pasywna" i nie odbiera komunikatów. Metadane między obydwoma są zsynchronizowane, dzięki czemu oba mogą bezproblemowo akceptować komunikaty bez konieczności zmiany kodu aplikacji ani parametrów połączenia. Aby upewnić się, że tylko aktywna przestrzeń nazw odbiera komunikaty, należy użyć aliasu. 

-  *Metadane*: jednostki, takie jak centra zdarzeń i grupy konsumentów; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostki i ich ustawienia są replikowane automatycznie. Komunikaty i zdarzenia nie są replikowane. 

-  *Tryb failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="supported-namespace-pairs"></a>Obsługiwane pary przestrzeni nazw
Obsługiwane są następujące kombinacje podstawowych i pomocniczych przestrzeni nazw:  

| Podstawowa przestrzeń nazw | Pomocnicza przestrzeń nazw | Obsługiwane | 
| ----------------- | -------------------- | ---------- |
| Standardowa | Standardowa | Tak | 
| Standardowa | Dedykowane | Tak | 
| Dedykowane | Dedykowane | Tak | 
| Dedykowane | Standardowa | Nie | 

> [!NOTE]
> Nie można sparować przestrzeni nazw, które znajdują się w tym samym dedykowanym klastrze. Można sparować przestrzenie nazw, które znajdują się w osobnych klastrach. 

## <a name="setup-and-failover-flow"></a>Konfiguracja i przepływ pracy awaryjnej

Poniższa sekcja zawiera omówienie procesu pracy w trybie failover i wyjaśnia, jak skonfigurować początkową pracę w trybie failover. 

![1][]

### <a name="setup"></a>Konfigurowanie

Najpierw należy utworzyć lub użyć istniejącej głównej przestrzeni nazw oraz nowej pomocniczej przestrzeni nazw, a następnie sparować te dwa. Ta parowanie zapewnia alias, którego można użyć do nawiązania połączenia. Ponieważ używasz aliasu, nie musisz zmieniać parametrów połączenia. Do parowania trybu failover można dodawać tylko nowe przestrzenie nazw. Na koniec należy dodać monitorowanie w celu wykrycia, czy jest konieczne przełączenie w tryb failover. W większości przypadków usługa jest jedną częścią dużego ekosystemu, dzięki czemu automatyczne przełączanie w tryb failover jest mało prawdopodobne, tak jak bardzo często należy przeprowadzić tryb failover w synchronizacji z pozostałym podsystemem lub infrastrukturą.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza Rozważmy rozwiązanie punktu sprzedaży (POS), które emituje komunikaty lub zdarzenia. Event Hubs przekazuje te zdarzenia do niektórych mapowań lub ponownego formatowania, a następnie przekazuje zmapowane dane do innego systemu w celu dalszej obróbki. W tym momencie wszystkie te systemy mogą być hostowane w tym samym regionie świadczenia usługi Azure. Decyzja o tym, kiedy i co część przełączenia w tryb failover zależy od przepływu danych w infrastrukturze. 

Możesz zautomatyzować tryb failover z systemami monitorowania lub z wbudowanymi rozwiązaniami do monitorowania. Jednak taka Automatyzacja wymaga dodatkowego planowania i pracy, która jest poza zakresem tego artykułu.

### <a name="failover-flow"></a>Przepływ pracy awaryjnej

Po zainicjowaniu trybu failover wymagane są dwa kroki:

1. W przypadku wystąpienia innej awarii można ponownie przełączyć się do trybu failover. W związku z tym Skonfiguruj inną pasywną przestrzeń nazw i zaktualizuj parowanie. 

2. Po ponownym udostępnieniu wiadomości ściągających z wcześniejszej podstawowej przestrzeni nazw. Następnie należy użyć tej przestrzeni nazw do zwykłych komunikatów poza konfiguracją odzyskiwania geograficznego lub usunąć starą podstawową przestrzeń nazw.

> [!NOTE]
> Obsługiwane są tylko semantyki z przekazywaniem niepowodzeniem. W tym scenariuszu nastąpi przełączenie w tryb failover, a następnie ponowne Parowanie z nową przestrzenią nazw. Powrót po awarii nie jest obsługiwany; na przykład w klastrze SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełniono błąd; na przykład podczas początkowej konfiguracji sparowano nieprawidłowe regiony, można przerwać parowanie dwóch przestrzeni nazw w dowolnym momencie. Jeśli chcesz używać par przestrzeni nazw jako zwykłych przestrzeni nazw, Usuń alias.

## <a name="samples"></a>Samples

[Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) pokazuje, jak skonfigurować i zainicjować tryb failover. Ten przykład ilustruje następujące koncepcje:

- Ustawienia wymagane w Azure Active Directory do użycia Azure Resource Manager z Event Hubs. 
- Kroki wymagane do wykonania przykładowego kodu. 
- Wyślij i Odbierz z bieżącej podstawowej przestrzeni nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę na następujące kwestie, które należy wziąć pod uwagę w tej wersji:

1. Zgodnie z projektem, Event Hubs odzyskiwanie geograficznego systemu nie replikuje danych i w związku z tym nie można ponownie użyć starej wartości przesunięcia głównego centrum zdarzeń w pomocniczym centrum zdarzeń. Zalecamy ponowne uruchomienie odbiornika zdarzeń z jedną z następujących czynności:

- *EventPosition. FromStart ()* — Jeśli chcesz odczytywać wszystkie dane z pomocniczego centrum zdarzeń.
- *EventPosition. FromEnd ()* — Jeśli chcesz odczytywać wszystkie nowe dane z czasu połączenia z pomocniczym centrum zdarzeń.
- *EventPosition. FromEnqueuedTime (DateTime)* — Jeśli chcesz odczytywać wszystkie dane odebrane w pomocniczym centrum zdarzeń, rozpoczynając od danego dnia i godziny.

2. W planowaniu trybu failover należy również wziąć pod uwagę współczynnik czasu. Jeśli na przykład utracisz łączność dłużej niż od 15 do 20 minut, możesz zdecydować się na zainicjowanie trybu failover. 
 
3. Fakt, że żadne dane nie są replikowane, oznacza, że aktualnie aktywne sesje nie są replikowane. Ponadto może nie zadziałała funkcja wykrywania duplikatów i zaplanowanych komunikatów. Nowe sesje, zaplanowane wiadomości i nowe duplikaty będą działały. 

4. Przełączenie w tryb failover złożonej infrastruktury rozproszonej powinno być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz. 

5. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Standardowa jednostka SKU Event Hubs obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizację izolowaną od błędów w regionie świadczenia usługi Azure. 

> [!NOTE]
> Strefy dostępności pomoc techniczna dla usługi Azure Event Hubs Standard jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-region.md) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko dla nowych przestrzeni nazw, korzystając z Azure Portal. Event Hubs nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po włączeniu jej w przestrzeni nazw.

![3][]

## <a name="next-steps"></a>Następne kroki

* [Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) zawiera prosty przepływ pracy, który tworzy parowanie geograficzne i inicjuje tryb failover w przypadku scenariusza odzyskiwania po awarii.
* [Dokumentacja interfejsu API REST](/rest/api/eventhub/) zawiera opis interfejsów API służących do wykonywania konfiguracji odzyskiwania po awarii geograficznej.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
