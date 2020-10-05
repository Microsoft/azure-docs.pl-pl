---
title: Co nowego? Informacje o wersji — Azure Event Grid
description: Dowiedz się, co nowego w Azure Event Grid, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 1edfa3e2bc4c8adae113b2215b7fb0483fba4c02
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87172788"
---
# <a name="whats-new-in-azure-event-grid"></a>Co nowego w Azure Event Grid?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: do czytnika kanału informacyjnego kanału informacyjnego `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Azure Event Grid w bieżąco otrzymuje ulepszenia. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Dodano obsługę do nowego interfejsu API usługi ogólnie dostępnej (GA) w wersji 2020-06-01.
- Nowe funkcje, które stały się GA:
    - [Mapowania danych wejściowych](input-mappings.md)
    - [Niestandardowy schemat wejściowy](input-mappings.md)
    - [Schemat usługi Event v10 w chmurze](cloud-event-schema.md)
    - [Service Bus tematu jako miejsce docelowe](handler-service-bus.md)
    - [Funkcja platformy Azure jako lokalizacja docelowa](handler-functions.md)
    - [Partia elementów webhook](./edge/delivery-output-batching.md)
    - [Zabezpieczanie elementu webhook (obsługa Azure Active Directory)](secure-webhook-delivery.md)
    - [Filtrowanie adresów IP](configure-firewall.md)
    - [Obsługa usługi link prywatny](configure-private-endpoints.md)
    - [Schemat dostarczania zdarzeń](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2 — wersja zapoznawcza (2020-05)
- Ta wersja zawiera dodatkowe poprawki błędów w celu zwiększenia jakości.
- W wersji 5.3.1-Preview ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview, która obejmuje następujące nowe funkcje: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](partner-topics-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Obsługa usługi link prywatny](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1 — wersja zapoznawcza (2020-04)
- Ta wersja zawiera różne poprawki błędów w celu zwiększenia jakości.
- Jako wersja 5.3.0-Preview ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview, która obejmuje następujące nowe funkcje: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](partner-topics-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Obsługa usługi link prywatny](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0 — wersja zapoznawcza (2020-03)
- Wprowadzamy nowe funkcje, które zostały już dodane do wersji 5.2.0-Preview. 
- Jako wersja 5.2.0-Preview, ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview.
- Dodaje obsługę do następujących nowych funkcji: 
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)
    - [Tematy partnerów](partner-topics-overview.md)
    - [Tematy systemowe jako śledzone zasoby w Azure Portal](system-topics.md)
    - [Dostarczanie zdarzeń przy użyciu tożsamości usługi zarządzanej](managed-service-identity.md) 
    - [Obsługa usługi link prywatny](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0 — wersja zapoznawcza (2020-01)
- Ta wersja odpowiada wersji interfejsu API 2020-04-01-Preview.
- Dodaje obsługę do następujących nowych funkcji:
    - [Obsługa filtrowania adresów IP podczas publikowania zdarzeń w domenach i tematach](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Ta wersja odpowiada `2019-06-01` wersji interfejsu API.
- Dodaje obsługę do następujących nowych funkcji:
    * [Domeny](event-domains.md)
    * Podział na strony i filtr wyszukiwania dla operacji na liście zasobów. Aby zapoznać się z przykładem, zobacz [Tematy — lista według subskrypcji](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Service Bus Kolejka jako lokalizacja docelowa](handler-service-bus.md)
    * [Filtrowanie zaawansowane](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0 — wersja zapoznawcza (2019-03)
- Ta wersja odpowiada wersji interfejsu API 2019-02-01-Preview.
- Dodaje obsługę do następujących nowych funkcji:
    * Podział na strony i filtr wyszukiwania dla operacji na liście zasobów. Aby zapoznać się z przykładem, zobacz [Tematy — lista według subskrypcji](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Ręczne tworzenie/usuwanie tematów domen](how-to-event-domains.md)
    * [Service Bus Kolejka jako lokalizacja docelowa](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Ta wersja odpowiada `2019-01-01` stabilnej wersji interfejsu API.
- Obsługuje ona ogólne udostępnienie następujących funkcji związanych z subskrypcjami zdarzeń:
    * [Miejsce docelowe wiadomości utraconych](manage-event-delivery.md)
    * [Kolejka usługi Azure Storage jako lokalizacja docelowa](handler-storage-queues.md)
    * [Azure Relay — połączenie hybrydowe jako miejsce docelowe](handler-relay-hybrid-connections.md)
    * [Sprawdzanie poprawności uzgadniania ręcznego](webhook-event-delivery.md)
    * [Obsługa zasad ponawiania](delivery-and-retry.md)
- Do funkcji, które są nadal dostępne w wersji zapoznawczej (takich jak [domeny Event Grid](event-domains.md) lub [Obsługa filtrów zaawansowanych](event-filtering.md#advanced-filtering) , nadal można uzyskać dostęp za pomocą wersji 3.0.1-Preview zestawu SDK ".

## <a name="301-preview-2018-10"></a>3.0.1 — wersja zapoznawcza (2018-10)
- Zależność od [10.0.3 wersji pakietu NuGet Newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0 — wersja zapoznawcza (2018-10)
- Ta wersja jest zestawem SDK dla nowych funkcji wprowadzonych w wersji interfejsu API 2018-09-15-Preview. — Ta wersja obejmuje obsługę programu:
    - [Tematy dotyczące domen i domen](event-domains.md)
    - Wprowadzenie [do daty wygaśnięcia subskrypcji zdarzeń](concepts.md#event-subscription-expiration)
    - Wprowadzenie [zaawansowanego filtrowania](event-filtering.md#advanced-filtering) dla subskrypcji zdarzeń
    - Stabilna wersja zestawu SDK, która określa `2018-01-01` wersję interfejsu API, nadal istnieje jako wersja 1.3.0

## <a name="next-steps"></a>Następne kroki
