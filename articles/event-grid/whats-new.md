---
title: Co nowego Informacje o wersji — Azure Event Grid
description: Dowiedz się, co nowego w Azure Event Grid, takich jak najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: f135b25f28002c037dd24fa0cb3bb7476a06309f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389591"
---
# <a name="whats-new-in-azure-event-grid"></a>Co nowego w programie Azure Event Grid?

>Otrzymuj powiadomienia o tym, kiedy chcieć ponownie na tę stronę w celu uzyskania aktualizacji, kopiując i wklejając ten adres URL: do czytnika źródła danych `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` ![ RSS ikona ](./media/whats-new/feed-icon-16x16.png) czytnika kanału informacyjnego.

Azure Event Grid na bieżąco otrzymuje ulepszenia. Aby być na bieżąco z najnowszymi zmianami, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

## <a name="610-preview-2020-10"></a>6.1.0-preview (2020–10)
- [Tożsamości zarządzane dla tematów systemowych](enable-identity-system-topics.md)
- [Niestandardowe właściwości dostarczania](delivery-properties.md)
- [Kolejka magazynu — czas wygaśnięcia komunikatu (TTL)](delivery-properties.md#configure-time-to-live-on-outgoing-events-to-azure-storage-queues)
- [Zaawansowane ulepszenia filtrowania](event-filtering.md#advanced-filtering)
    - Obsługa filtrowania danych tablicy w zdarzeniach przychodzących
    - Zezwalaj na filtrowanie atrybutów kontekstu rozszerzeń CloudEvents
    - Nowe operatory
        - StringNotContains
        - StringNotBeginsWith
        - StringNotEndsWith
        - NumberInRange
        - NumberNotInRange
        - Isnull
        - IsNotNull
- [Zezwalaj Event Grid na przekształcenia schematu cloudEvents 1.0 dla niestandardowych tematów i domen](cloudevents-schema.md#configure-event-grid-for-cloudevents)
        

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Dodanie obsługi do nowej ogólnie dostępnej wersji interfejsu API usługi 2020-06-01.
- Nowe funkcje, które stały się bardziej dostępne:
    - [Mapowania danych wejściowych](input-mappings.md)
    - [Niestandardowy schemat wejściowy](input-mappings.md)
    - [Schemat zdarzeń w chmurze w wersji 10](cloud-event-schema.md)
    - [Service Bus temat jako miejsce docelowe](handler-service-bus.md)
    - [Azure function as destination (Funkcja platformy Azure jako miejsce docelowe)](handler-functions.md)
    - [Przetwarzanie wsadowe w webhook](./edge/delivery-output-batching.md)
    - [Bezpieczny webhook (obsługa Azure Active Directory)](secure-webhook-delivery.md)
    - [Filtrowanie adresów IP](configure-firewall.md)
    - [Private Link service support (Obsługa usługi Private Link Service)](configure-private-endpoints.md)
    - [Schemat dostarczania zdarzeń](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Ta wersja zawiera dodatkowe poprawki błędów zwiększające jakość.
- Jako wersja 5.3.1-preview ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview, która zawiera następujące nowe funkcje: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](./partner-events-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Private Link service support (Obsługa usługi Private Link Service)](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Ta wersja zawiera różne poprawki błędów w celu poprawy jakości.
- Jako wersja 5.3.0-preview ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview, która zawiera następujące nowe funkcje: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](./partner-events-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Private Link service support (Obsługa usługi Private Link Service)](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- Wprowadzamy nowe funkcje, które zostały już dodane w wersji 5.2.0-preview. 
- Jako wersja 5.2.0-preview ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview.
- Dodaje obsługę następujących nowych funkcji: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](./partner-events-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Private Link service support (Obsługa usługi Private Link Service)](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview.
- Dodaje ona obsługę następujących nowych funkcji:
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Ta wersja odpowiada wersji `2019-06-01` interfejsu API.
- Dodaje obsługę następujących nowych funkcji:
    * [Domeny](event-domains.md)
    * Stronicowanie i filtrowanie wyszukiwania dla operacji listy zasobów. Aby uzyskać przykład, zobacz [Tematy — lista według subskrypcji.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Service Bus jako miejsce docelowe](handler-service-bus.md)
    * [Filtrowanie zaawansowane](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Ta wersja odpowiada wersji interfejsu API 2019-02-01-preview.
- Dodaje obsługę następujących nowych funkcji:
    * Stronicowanie i filtrowanie wyszukiwania dla operacji listy zasobów. Aby uzyskać przykład, zobacz [Tematy — lista według subskrypcji.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Ręczne tworzenie/usuwanie tematów dotyczących domeny](how-to-event-domains.md)
    * [Service Bus jako miejsce docelowe](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Ta wersja odpowiada stabilnej `2019-01-01` wersji interfejsu API.
- Obsługuje ona ogólną dostępność następujących funkcji związanych z subskrypcjami zdarzeń:
    * [Miejsce docelowe utraconych utraconych danych](manage-event-delivery.md)
    * [Kolejka usługi Azure Storage jako miejsce docelowe](handler-storage-queues.md)
    * [Azure Relay — połączenie hybrydowe jako miejsce docelowe](handler-relay-hybrid-connections.md)
    * [Ręczna walidacja uściśli](webhook-event-delivery.md)
    * [Obsługa zasad ponawiania prób](delivery-and-retry.md)
- Funkcje, które są nadal [](event-domains.md) w wersji zapoznawczej (takie jak domeny Event Grid lub zaawansowana obsługa filtrów, są nadal dostępne przy użyciu wersji 3.0.1 zestawu SDK w wersji zapoznawczej". [](event-filtering.md#advanced-filtering)

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Zależność od [wersji 10.0.3 pakietu NuGet firmy Newtonsoft.](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Ta wersja to zestaw SDK w wersji zapoznawczej dla nowych funkcji wprowadzonych w wersji 2018-09-15-preview interfejsu API. — Ta wersja obejmuje obsługę:
    - [Tematy dotyczące domeny i domeny](event-domains.md)
    - Wprowadzenie do [daty wygaśnięcia subskrypcji zdarzeń](concepts.md#event-subscription-expiration)
    - Wprowadzenie do [zaawansowanego filtrowania](event-filtering.md#advanced-filtering) subskrypcji zdarzeń
    - Stabilna wersja zestawu SDK przeznaczonego dla wersji interfejsu API nadal istnieje `2018-01-01` jako wersja 1.3.0

## <a name="next-steps"></a>Następne kroki