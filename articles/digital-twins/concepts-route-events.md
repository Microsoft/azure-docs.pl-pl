---
title: Trasy zdarzeń
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak kierować zdarzenia w ramach usługi Azure Digital bliźniaczych reprezentacji i innych usług platformy Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4ba931555b8e6fa146b68ff2db0bee9c774d1a1b
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613033"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Kierowanie zdarzeń w ramach i na zewnątrz usługi Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji korzysta z **tras zdarzeń** do wysyłania danych do klientów spoza usługi. 

W trakcie okresu zapoznawczego istnieją dwa główne przypadki wysyłania danych usługi Azure Digital bliźniaczych reprezentacji:
* Wysyłanie danych z jednej sznurka na cyfrowym wykresie bliźniaczych reprezentacji Azure do innego. Na przykład, gdy zmieniana jest właściwość jednocyfrowego przędzy, możesz chcieć odpowiednio powiadomić i zaktualizować inne cyfrowe sznurki.
* Wysyłanie danych do usług danych podrzędnych w celu dodatkowego przechowywania lub przetwarzania (nazywanego również ruchem wychodzącym *danych*). Na przykład
  - Szpital może chcieć wysłać dane zdarzenia usługi Azure Digital bliźniaczych reprezentacji do [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md), aby zarejestrować dane szeregów czasowych zdarzeń związanych z handwashing dla analizy zbiorczej.
  - Firma, która już korzysta z [Azure Maps](../azure-maps/about-azure-maps.md) może chcieć używać usługi Azure Digital bliźniaczych reprezentacji do ulepszania swoich rozwiązań. Mogą szybko włączyć mapę platformy Azure po skonfigurowaniu usługi Azure Digital bliźniaczych reprezentacji, przełączyć jednostki mapy platformy Azure do usługi Azure Digital bliźniaczych reprezentacji jako [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) na grafie bliźniaczym lub uruchamiać zaawansowane zapytania wykorzystujące swoje Azure Maps i dane cyfrowego bliźniaczych reprezentacji platformy Azure.

Trasy zdarzeń są używane dla obu tych scenariuszy.

## <a name="about-event-routes"></a>Informacje o trasach zdarzeń

Trasa zdarzenia umożliwia wysyłanie danych zdarzeń z cyfrowego bliźniaczych reprezentacji w usłudze Azure Digital bliźniaczych reprezentacji do niestandardowych punktów końcowych w Twoich subskrypcjach. Trzy usługi platformy Azure są obecnie obsługiwane dla punktów końcowych: [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)i [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Każda z tych usług platformy Azure może być połączona z innymi usługami i działa jako środek pośredniczący, wysyłając dane do końcowych miejsc docelowych, takich jak TSI lub Azure Maps dla dowolnego wymaganego przetwarzania.

Na poniższym diagramie przedstawiono przepływ danych zdarzeń za pomocą większego rozwiązania IoT za pomocą bliźniaczych reprezentacji cyfrowej platformy Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Usługa Azure Digital bliźniaczych reprezentacji — dane routingu za pomocą punktów końcowych do kilku usług podrzędnych" border="false":::

Typowymi elementami podrzędnymi dla tras zdarzeń są zasoby, takie jak TSI, Azure Maps, magazyn i rozwiązania do analizy.

### <a name="event-routes-for-internal-digital-twin-events"></a>Trasy zdarzeń dla wewnętrznych cyfrowych dróg wieloosiowych

W bieżącej wersji zapoznawczej trasy zdarzeń są również używane do obsługi zdarzeń w grafie bliźniaczym i wysyłania danych z cyfrowych sznurów do wieloosiowych sznurów elektronicznych. W tym celu należy połączyć trasy zdarzeń za pomocą Event Grid do zasobów obliczeniowych, takich jak [Azure Functions](../azure-functions/functions-overview.md). Te funkcje definiują sposób, w jaki bliźniaczych reprezentacji powinna odbierać i odpowiadać na zdarzenia. 

Gdy zasób obliczeniowy chce zmodyfikować wykres bliźniaczy na podstawie zdarzenia otrzymanego za pośrednictwem trasy zdarzenia, pomocne jest, aby wiedzieć, które sznury mają być modyfikowane przed czasem. 

Alternatywnie komunikat o zdarzeniu zawiera również identyfikator sieci źródłowej, która wysłała komunikat, więc zasób obliczeniowy może użyć zapytań lub przekierować relacje w celu znalezienia przędzy docelowej dla żądanej operacji. 

Zasób obliczeniowy wymaga również niezależnego ustanowienia uprawnień zabezpieczeń i dostępu.

Aby zapoznać się z procesem konfigurowania funkcji platformy Azure w celu przetwarzania zdarzeń cyfrowych sznurów, zobacz [How to: set up a Azure Function for processing Data](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego

Aby zdefiniować trasę zdarzeń, deweloperzy najpierw muszą definiować punkty końcowe. **Punkt końcowy** jest miejscem docelowym poza usługą Azure Digital bliźniaczych reprezentacji, która obsługuje połączenie trasy. Obsługiwane miejsca docelowe w bieżącej wersji zapoznawczej to:
* Event Grid tematy niestandardowe
* Centrum zdarzeń
* Service Bus

Punkty końcowe są konfigurowane przy użyciu interfejsów API płaszczyzny kontroli (obsługiwane przez [interfejs wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md)lub za pośrednictwem Azure Portal. Definicja punktu końcowego daje:
* Identyfikator punktu końcowego (lub przyjazna nazwa)
* Typ punktu końcowego (Event Grid, centrum zdarzeń lub Service Bus)
* Podstawowe parametry połączenia i pomocnicze parametry połączenia do uwierzytelniania 
* Ścieżka tematu punktu końcowego, taka jak *Your-topic.westus2.eventgrid.Azure.NET*

Interfejsy API punktu końcowego dostępne w płaszczyźnie sterującej są następujące:
* Utwórz punkt końcowy
* Pobierz listę punktów końcowych
* Pobierz punkt końcowy według identyfikatora (identyfikator punktu końcowego: pass)
* Usuń punkt końcowy według identyfikatora (identyfikator punktu końcowego: pass)

## <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń
 
Trasy zdarzeń są tworzone w aplikacji klienckiej przy użyciu następującego wywołania [zestawu SDK platformy .NET (C#)](how-to-use-apis-sdks.md) : 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-ID>"));
```

* `endpoint-ID`Identyfikuje punkt końcowy, taki jak centrum zdarzeń, Event Grid lub Service Bus. Te punkty końcowe muszą zostać utworzone w subskrypcji i dołączone do usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API płaszczyzny kontroli przed wykonaniem tego wywołania.

Obiekt trasy zdarzenia przeszedł do `EventRoutes.Add` również przyjmuje parametr [ **filtru** ](./how-to-manage-routes.md#filter-events), który może służyć do ograniczania typów zdarzeń, które są zgodne z tą trasą.

Trasy można także tworzyć za pomocą [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Typy komunikatów o zdarzeniach

Różne typy zdarzeń w IoT Hub i Azure Digital bliźniaczych reprezentacji tworzą różne typy komunikatów powiadomień, zgodnie z poniższym opisem.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz jak skonfigurować trasę zdarzeń i zarządzać nią:
* [Instrukcje: Zarządzanie punktami końcowymi i trasami](how-to-manage-routes.md)

Lub zapoznaj się z tematem jak używać Azure Functions do kierowania zdarzeń w usłudze Azure Digital bliźniaczych reprezentacji:
* [Porada: Konfigurowanie funkcji platformy Azure do przetwarzania danych](how-to-create-azure-function.md)