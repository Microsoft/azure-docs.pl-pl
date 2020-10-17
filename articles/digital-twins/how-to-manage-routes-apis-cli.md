---
title: Zarządzanie punktami końcowymi i trasami (interfejsy API i interfejs wiersza polecenia)
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować punkty końcowe i trasy zdarzeń dla danych Digital bliźniaczych reprezentacji systemu Azure oraz zarządzać nimi.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 10/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 14edc97115735f8b6763171a07b5f739fc745e9f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151246"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Zarządzanie punktami końcowymi i trasami w usłudze Azure Digital bliźniaczych reprezentacji (interfejsy API i interfejs wiersza polecenia)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

W usłudze Azure Digital bliźniaczych reprezentacji można kierować [powiadomienia o zdarzeniach](how-to-interpret-event-data.md) do usług podrzędnych lub podłączonych zasobów obliczeniowych. W tym celu należy najpierw skonfigurować **punkty końcowe** , które mogą odbierać zdarzenia. Następnie można utworzyć  [**trasy zdarzeń**](concepts-route-events.md) , które określają, które zdarzenia generowane przez usługę Azure Digital bliźniaczych reprezentacji są dostarczane do których punktów końcowych.

Punkty końcowe i trasy mogą być zarządzane za pomocą [interfejsów API EventRoutes](how-to-use-apis-sdks.md), [zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Ten artykuł przeprowadzi Cię przez proces tworzenia punktów końcowych i tras za pomocą tych mechanizmów.

Mogą być również zarządzane przez [Azure Portal](https://portal.azure.com). Aby uzyskać wersję tego artykułu, która używa portalu, zobacz [*How to: Manage Endpoints and Routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć **konto platformy Azure** (możesz skonfigurować je bezpłatnie w [tym miejscu](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* W subskrypcji platformy Azure będzie potrzebne **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . Jeśli nie masz już wystąpienia, możesz je utworzyć, wykonując kroki opisane w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md). Skorzystaj z następujących wartości z Instalatora, które są przydatne w dalszej części tego artykułu:
    - Nazwa wystąpienia
    - Grupa zasobów
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Tworzenie punktu końcowego dla usługi Azure Digital bliźniaczych reprezentacji

Są to obsługiwane typy punktów końcowych, które można utworzyć dla danego wystąpienia:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Aby uzyskać więcej informacji na temat różnych typów punktów końcowych, zobacz [*Wybieranie między usługami Azure Messaging*](../event-grid/compare-messaging-services.md).

Aby można było połączyć punkt końcowy z usługą Azure Digital bliźniaczych reprezentacji, należy już użyć tematu usługi Event Grid, centrum zdarzeń lub Service Bus, które są używane dla punktu końcowego. 

### <a name="create-an-event-grid-endpoint"></a>Tworzenie punktu końcowego Event Grid

Poniższy przykład pokazuje, jak utworzyć punkt końcowy typu usługi Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz użyć [Azure Cloud Shell](https://shell.azure.com)lub [zainstalować interfejs wiersza polecenia lokalnie](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

Najpierw utwórz temat z siatką zdarzeń. Możesz użyć poniższego polecenia lub wyświetlić kroki bardziej szczegółowo, odwiedzając [sekcję *Tworzenie niestandardowego tematu w temacie* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) Event Grid *zdarzenia niestandardowe* — Szybki Start.

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Aby uzyskać listę nazw regionów platformy Azure, które mogą być przekazywane do poleceń w interfejsie wiersza polecenia platformy Azure, uruchom następujące polecenie:
> ```azurecli
> az account list-locations -o table
> ```

Po utworzeniu tematu można połączyć go z usługą Azure Digital bliźniaczych reprezentacji, korzystając z następującego [polecenia interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md):

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Teraz temat usługi Event Grid jest dostępny jako punkt końcowy wewnątrz Digital bliźniaczych reprezentacji systemu Azure, pod nazwą określoną za pomocą `--endpoint-name` argumentu. Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia**, którą utworzysz [w dalszej części tego artykułu](#event-routes-with-apis-and-the-c-sdk) przy użyciu interfejsu API usługi Digital bliźniaczych reprezentacji.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Utwórz punkt końcowy Event Hubs lub Service Bus

Proces tworzenia Event Hubs lub Service Bus punktów końcowych jest podobny do procesu Event Grid pokazanego powyżej.

Najpierw utwórz zasoby, które będą używane jako punkt końcowy. Oto co jest wymagane:
* Service Bus: _Service Bus przestrzeni nazw_, _temat Service Bus_, _reguła autoryzacji_
* Event Hubs: _Event Hubs przestrzeń nazw_, _centrum zdarzeń_, _reguła autoryzacji_

Następnie użyj następujących poleceń, aby utworzyć punkty końcowe w usłudze Azure Digital bliźniaczych reprezentacji: 

* Dodaj punkt końcowy tematu Service Bus (wymaga wstępnie utworzonego zasobu Service Bus)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Dodaj punkt końcowy Event Hubs (wymaga wstępnie utworzonego zasobu Event Hubs)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Tworzenie punktu końcowego z utraconymi wiadomościami

Gdy punkt końcowy nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**.

Aby można było utworzyć punkt końcowy z włączoną obsługą utraconych wiadomości, należy użyć [interfejsów API ARM](https://docs.microsoft.com/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) do utworzenia punktu końcowego. 

Przed ustawieniem lokalizacji utraconych wiadomości musisz mieć konto magazynu z kontenerem. Podajesz adres URL dla tego kontenera podczas tworzenia punktu końcowego. Utracona wartość jest podawana jako adres URL kontenera z tokenem SAS. Ten token wymaga tylko `write` uprawnienia do kontenera docelowego na koncie magazynu. W pełni sformułowany adres URL będzie miał postać: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Aby dowiedzieć się więcej o tokenach SAS, zobacz: [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

Aby dowiedzieć się więcej o utraconych wiadomościach, zobacz [pojęcia: trasy zdarzeń](./concepts-route-events.md#dead-letter-events)

#### <a name="configuring-the-endpoint"></a>Konfigurowanie punktu końcowego

Podczas tworzenia punktu końcowego Dodaj `deadLetterSecret` do `properties` obiektu w treści żądania, który zawiera adres URL kontenera i token sygnatury dostępu współdzielonego dla konta magazynu.

```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji: [Endpoints-DigitalTwinsEndpoint metodę createorupdate](https://docs.microsoft.com/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate).

### <a name="message-storage-schema"></a>Schemat magazynu komunikatów

Komunikaty o utraconych wiadomościach będą przechowywane w następującym formacie na koncie magazynu:

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

Wiadomości utracone będą zgodne ze schematem oryginalnego zdarzenia, które było przeznaczone do dostarczenia do oryginalnego punktu końcowego.

Poniżej znajduje się przykład wiadomości utraconej dla [powiadomienia o tworzeniu sznurka](./how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Trasy zdarzeń (za pomocą interfejsów API i zestawu C# SDK)

Aby faktycznie wysyłać dane z usługi Azure Digital bliźniaczych reprezentacji do punktu końcowego, należy zdefiniować **trasę zdarzeń**. **Interfejsy API** usługi Azure Digital bliźniaczych reprezentacji EventRoutes umożliwiają deweloperom tworzenie przepływów zdarzeń w całym systemie i w ramach usług podrzędnych. Przeczytaj więcej na temat tras zdarzeń w temacie [*pojęcia: Routing Digital bliźniaczych reprezentacji Events*](concepts-route-events.md).

W przykładach w tej sekcji użyto [zestawu SDK platformy .NET (C#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core).

**Wymaganie wstępne**: należy utworzyć punkty końcowe zgodnie z wcześniejszym opisem w tym artykule przed rozpoczęciem tworzenia trasy. Po zakończeniu konfigurowania punktów końcowych można utworzyć trasę zdarzenia.

>[!NOTE]
>Jeśli ostatnio wdrożono punkty końcowe, sprawdź, czy są one gotowe do wdrożenia, **przed** podjęciem próby użycia ich dla nowej trasy zdarzeń. Jeśli wdrożenie trasy nie powiedzie się, ponieważ punkty końcowe nie są gotowe, odczekaj kilka minut i spróbuj ponownie.
>
> W przypadku wykonywania skryptów w tym przepływie warto uwzględnić to przez utworzenie w ciągu 2-3 minut czasu oczekiwania na zakończenie wdrażania usługi punktu końcowego przed przejściem do konfiguracji trasy.

### <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń

Trasy zdarzeń są definiowane przy użyciu [interfejsów API płaszczyzny danych](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Definicja trasy może zawierać następujące elementy:
* Nazwa trasy, która ma być używana
* Nazwa punktu końcowego, który ma być używany
* Filtr określający, które zdarzenia są wysyłane do punktu końcowego. 

Jeśli nie ma nazwy trasy, żadne komunikaty nie są kierowane poza usługę Azure Digital bliźniaczych reprezentacji. Jeśli istnieje Nazwa trasy i filtr jest `true` , wszystkie komunikaty są kierowane do punktu końcowego. Jeśli istnieje Nazwa trasy i zostanie dodany inny filtr, komunikaty będą filtrowane zgodnie z filtrem.

Jedna trasa powinna zezwalać na wybranie wielu powiadomień i typów zdarzeń. 

`CreateEventRoute` to wywołanie zestawu SDK, które służy do dodawania trasy zdarzenia. Oto przykład użycia:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

### <a name="event-route-sample-code"></a>Przykładowy kod trasy zdarzenia

Poniższy przykład kodu przedstawia sposób tworzenia, wyświetlania i usuwania trasy zdarzeń:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrowanie zdarzeń

Bez filtrowania punkty końcowe otrzymują wiele zdarzeń z usługi Azure Digital bliźniaczych reprezentacji:
* Dane telemetryczne wywoływane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) przy użyciu interfejsu API usługi Digital bliźniaczych reprezentacji platformy Azure
* Powiadomienia o zmianie właściwości przędzy, generowane dla zmian właściwości dla dowolnej przędzy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji
* Zdarzenia cyklu życiowego, wywoływane podczas tworzenia lub usuwania bliźniaczych reprezentacji lub relacji
* Zdarzenia zmiany modelu, wywoływane po dodaniu lub usunięciu [modeli](concepts-models.md) skonfigurowanych w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji

Można ograniczyć wysyłane zdarzenia, dodając **Filtr** dla punktu końcowego do trasy zdarzenia.

Aby dodać filtr, można użyć żądania PUT do *protokołu https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* z następującą treścią:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Poniżej przedstawiono obsługiwane filtry tras. Użyj szczegółów w kolumnie *Filtruj schemat tekstu* , aby zastąpić `<filter-text>` symbol zastępczy w powyższej treści żądania.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Zarządzanie punktami końcowymi i trasami przy użyciu interfejsu wiersza polecenia

Punkty końcowe i trasy można także zarządzać za pomocą interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji. Aby uzyskać więcej informacji o korzystaniu z interfejsu wiersza polecenia i dostępnych poleceń, zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji CLI*](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Następne kroki

Przeczytaj o różnych typach komunikatów zdarzeń, które można odbierać:
* [*Instrukcje: interpretowanie danych zdarzenia*](how-to-interpret-event-data.md)