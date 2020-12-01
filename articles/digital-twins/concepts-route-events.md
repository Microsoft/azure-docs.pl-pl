---
title: Trasy zdarzeń
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak kierować zdarzenia w ramach usługi Azure Digital bliźniaczych reprezentacji i innych usług platformy Azure.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6559de2b94879ac8643f4945fd5adcf4fc7e1045
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350827"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Kierowanie zdarzeń w ramach i na zewnątrz usługi Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji korzysta z **tras zdarzeń** do wysyłania danych do klientów spoza usługi. 

Istnieją dwa główne przypadki wysyłania danych usługi Azure Digital bliźniaczych reprezentacji:
* Wysyłanie danych z jednej sznurka na cyfrowym wykresie bliźniaczych reprezentacji Azure do innego. Na przykład, gdy zmieniana jest właściwość jednocyfrowego przędzy, możesz chcieć odpowiednio powiadomić i zaktualizować inne cyfrowe sznurki.
* Wysyłanie danych do usług danych podrzędnych w celu dodatkowego przechowywania lub przetwarzania (nazywanego również ruchem wychodzącym *danych*). Na przykład
  - Szpital może chcieć wysłać dane zdarzenia usługi Azure Digital bliźniaczych reprezentacji do [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md), aby zarejestrować dane szeregów czasowych zdarzeń związanych z handwashing dla analizy zbiorczej.
  - Firma, która już korzysta z [Azure Maps](../azure-maps/about-azure-maps.md) może chcieć używać usługi Azure Digital bliźniaczych reprezentacji do ulepszania swoich rozwiązań. Mogą szybko włączyć mapę platformy Azure po skonfigurowaniu usługi Azure Digital bliźniaczych reprezentacji, przełączyć jednostki mapy platformy Azure do usługi Azure Digital bliźniaczych reprezentacji jako [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) na grafie bliźniaczym lub uruchamiać zaawansowane zapytania wykorzystujące swoje Azure Maps i dane cyfrowego bliźniaczych reprezentacji platformy Azure.

Trasy zdarzeń są używane dla obu tych scenariuszy.

## <a name="about-event-routes"></a>Informacje o trasach zdarzeń

Trasa zdarzenia umożliwia wysyłanie danych zdarzeń z cyfrowego bliźniaczych reprezentacji w usłudze Azure Digital bliźniaczych reprezentacji do niestandardowych punktów końcowych w Twoich subskrypcjach. Trzy usługi platformy Azure są obecnie obsługiwane dla punktów końcowych: [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)i [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Każda z tych usług platformy Azure może być połączona z innymi usługami i działa jako środek pośredniczący, wysyłając dane do końcowych miejsc docelowych, takich jak TSI lub Azure Maps dla dowolnego wymaganego przetwarzania.

Na poniższym diagramie przedstawiono przepływ danych zdarzeń za pomocą większego rozwiązania IoT za pomocą bliźniaczych reprezentacji cyfrowej platformy Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Usługa Azure Digital bliźniaczych reprezentacji — dane routingu za pomocą punktów końcowych do kilku usług podrzędnych" border="false":::

Typowymi elementami podrzędnymi dla tras zdarzeń są zasoby, takie jak TSI, Azure Maps, magazyn i rozwiązania do analizy.

### <a name="event-routes-for-internal-digital-twin-events"></a>Trasy zdarzeń dla wewnętrznych cyfrowych dróg wieloosiowych

Trasy zdarzeń są również używane do obsługi zdarzeń w grafie bliźniaczym i wysyłania danych z cyfrowych sznurów do cyfrowych sznurów. W tym celu należy połączyć trasy zdarzeń za pomocą Event Grid do zasobów obliczeniowych, takich jak [Azure Functions](../azure-functions/functions-overview.md). Te funkcje definiują sposób, w jaki bliźniaczych reprezentacji powinna odbierać i odpowiadać na zdarzenia. 

Gdy zasób obliczeniowy chce zmodyfikować wykres bliźniaczy na podstawie zdarzenia otrzymanego za pośrednictwem trasy zdarzenia, pomocne jest, aby wiedzieć, które sznury mają być modyfikowane przed czasem. 

Alternatywnie komunikat o zdarzeniu zawiera również identyfikator sieci źródłowej, która wysłała komunikat, więc zasób obliczeniowy może użyć zapytań lub przekierować relacje w celu znalezienia przędzy docelowej dla żądanej operacji. 

Zasób obliczeniowy wymaga również niezależnego ustanowienia uprawnień zabezpieczeń i dostępu.

Aby zapoznać się z procesem konfigurowania funkcji platformy Azure w celu przetwarzania zdarzeń cyfrowych sznurów, zobacz [*How to: set up a Azure Function for processing Data*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego

Aby zdefiniować trasę zdarzeń, deweloperzy najpierw muszą definiować punkty końcowe. **Punkt końcowy** jest miejscem docelowym poza usługą Azure Digital bliźniaczych reprezentacji, która obsługuje połączenie trasy. Obsługiwane miejsca docelowe obejmują:
* Event Grid tematy niestandardowe
* Centrum zdarzeń
* Service Bus

Aby utworzyć punkt końcowy, można użyć [**interfejsów API**](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins), [**poleceń interfejsu wiersza polecenia**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)platformy Azure Digital bliźniaczych reprezentacji lub [**Azure Portal**](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins). 

Podczas definiowania punktu końcowego należy podać:
* Nazwa punktu końcowego
* Typ punktu końcowego (Event Grid, centrum zdarzeń lub Service Bus)
* Podstawowe parametry połączenia i pomocnicze parametry połączenia do uwierzytelniania 
* Ścieżka tematu punktu końcowego, taka jak *Your-topic.westus2.eventgrid.Azure.NET*

Interfejsy API punktu końcowego dostępne w płaszczyźnie sterującej są następujące:
* Utwórz punkt końcowy
* Pobierz listę punktów końcowych
* Pobierz punkt końcowy według nazwy
* Usuń punkt końcowy według nazwy

## <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń
 
Aby utworzyć trasę zdarzeń, można użyć [**interfejsów API**](how-to-manage-routes-apis-cli.md#create-an-event-route), [**poleceń interfejsu wiersza polecenia**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)platformy Azure Digital bliźniaczych reprezentacji lub [**Azure Portal**](how-to-manage-routes-portal.md#create-an-event-route). 

Oto przykład tworzenia trasy zdarzeń w aplikacji klienckiej przy użyciu `CreateOrReplaceEventRouteAsync` wywołania [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) : 

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("endpointName", eventFilter);
await client.CreateOrReplaceEventRouteAsync("routeName", er);
```

1. Najpierw `DigitalTwinsEventRoute` tworzony jest obiekt, a Konstruktor przyjmuje nazwę punktu końcowego. To `endpointName` pole identyfikuje punkt końcowy, taki jak centrum zdarzeń, Event Grid lub Service Bus. Te punkty końcowe muszą zostać utworzone w subskrypcji i dołączone do usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API płaszczyzny kontroli przed wykonaniem tego wywołania.

2. Obiekt trasy zdarzeń ma także pole [**filtru**](how-to-manage-routes-apis-cli.md#filter-events) , które może służyć do ograniczania typów zdarzeń, które są zgodne z tą trasą. Filtr `true` włącza trasę bez dodatkowych filtrowania (filtr `false` wyłącza trasę). 

3. Ten obiekt trasy zdarzeń jest następnie przesyłany do `CreateOrReplaceEventRouteAsync` , wraz z nazwą trasy.

> [!TIP]
> Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

Trasy można także tworzyć za pomocą [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

## <a name="dead-letter-events"></a>Zdarzenia utraconych wiadomości

Gdy punkt końcowy nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**. Po spełnieniu **jednego z następujących** warunków usługa Azure Digital bliźniaczych reprezentacji będzie martwa zdarzenie. 

* Zdarzenie nie jest dostarczane w okresie czasu wygaśnięcia
* Liczba prób dostarczenia zdarzenia przekroczyła limit.

Jeśli spełniony jest dowolny z warunków, zdarzenie zostanie porzucone lub utracone. Domyślnie **każdy punkt końcowy nie włącza** utraconych wiadomości. Aby je włączyć, należy określić konto magazynu do przechowywania niedostarczonych zdarzeń podczas tworzenia punktu końcowego. Następnie można ściągnąć zdarzenia z tego konta magazynu w celu rozpoznania dostaw.

Przed ustawieniem lokalizacji utraconych wiadomości musisz mieć konto magazynu z kontenerem. Podajesz adres URL dla tego kontenera podczas tworzenia punktu końcowego. Utracona wartość jest podawana jako adres URL kontenera z tokenem SAS. Ten token wymaga tylko `write` uprawnienia do kontenera docelowego na koncie magazynu. W pełni sformułowany adres URL będzie miał postać: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Aby dowiedzieć się więcej o tokenach SAS, zobacz: [ *udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)*](../storage/common/storage-sas-overview.md)

Aby dowiedzieć się, jak skonfigurować punkt końcowy przy użyciu utraconych wiadomości, zobacz [*How to: Manage Endpoints and Routes in Azure Digital bliźniaczych reprezentacji (interfejsy API i CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Typy komunikatów o zdarzeniach

Różne typy zdarzeń w IoT Hub i Azure Digital bliźniaczych reprezentacji tworzą różne typy komunikatów powiadomień, zgodnie z poniższym opisem.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz jak skonfigurować trasę zdarzeń i zarządzać nią:
* [*Instrukcje: Zarządzanie punktami końcowymi i trasami*](how-to-manage-routes-apis-cli.md)

Lub zapoznaj się z tematem jak używać Azure Functions do kierowania zdarzeń w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Porada: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md)