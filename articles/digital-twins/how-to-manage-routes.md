---
title: Zarządzanie punktami końcowymi i trasami
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować punkty końcowe i trasy zdarzeń dla danych Digital bliźniaczych reprezentacji systemu Azure oraz zarządzać nimi.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 923ae652872246916b2a4c5e8be95871983dbe95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559838"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Zarządzanie punktami końcowymi i trasami w usłudze Azure Digital bliźniaczych reprezentacji

W usłudze Azure Digital bliźniaczych reprezentacji można kierować [powiadomieniami o zdarzeniach](how-to-interpret-event-data.md) do usług podrzędnych lub połączyć się z zasobami obliczeniowymi. W tym celu należy najpierw skonfigurować **punkty końcowe** , które mogą odbierać zdarzenia, a następnie [**trasy zdarzeń**](concepts-route-events.md) , które określają, które zdarzenia wygenerowane przez usługę Azure Digital bliźniaczych reprezentacji są dostarczane do których punktów końcowych.

Obsługiwane typy punktów końcowych to:
* [Centrum zdarzeń](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Aby uzyskać więcej informacji na temat różnych punktów końcowych, zobacz [Wybieranie między usługami Azure Messaging](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Punkty końcowe i trasy są zarządzane za pomocą [**interfejsów API EventRoutes**](how-to-use-apis-sdks.md), [zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Mogą być również zarządzane przez [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Zarządzanie trasami zdarzeń za pomocą Azure Portal jest obecnie dostępne tylko dla użytkowników platformy Azure na kontach domeny firmowej. 
>
>Jeśli używasz osobistego [konto Microsoft (MSA)](https://account.microsoft.com/account/Account), takiego jak @outlook.com konto, Użyj interfejsów API Digital bliźniaczych reprezentacji lub interfejsu wiersza polecenia platformy Azure do zarządzania trasami zdarzeń zgodnie z opisem w tym artykule.

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Tworzenie punktu końcowego dla usługi Azure Digital bliźniaczych reprezentacji

Aby można było połączyć punkt końcowy z usługą Azure Digital bliźniaczych reprezentacji, należy już istnieć centrum zdarzeń, temat usługi Event Grid lub Service Bus, które są używane dla punktu końcowego. 

Poniższy przykład pokazuje, jak utworzyć temat usługi Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Aby uzyskać listę nazw regionów platformy Azure, które mogą być przekazywane do poleceń w interfejsie wiersza polecenia platformy Azure, uruchom następujące polecenie:
> ```azurecli
> az account list-locations -o table
> ```

Po utworzeniu tematu można połączyć go z usługą Azure Digital bliźniaczych reprezentacji za pomocą następującego polecenia:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Dzięki temu temat usługi Event Grid jest dostępny w usłudze Azure Digital bliźniaczych reprezentacji pod nazwą określoną za pomocą `--endpoint-name` argumentu. Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia**, którą utworzysz w następnej sekcji za pomocą interfejsu API usługi Digital bliźniaczych reprezentacji.

Istnieją równoważne polecenia dla centrum zdarzeń i Service Bus punkty końcowe:

* Dodaj punkt końcowy tematu Service Bus (wymaga wstępnie utworzonego zasobu Service Bus)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Dodaj punkt końcowy centrum zdarzeń (wymaga wstępnie utworzonego zasobu centrum zdarzeń)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Zarządzanie trasami zdarzeń przy użyciu interfejsów API

Aby faktycznie wysyłać dane z usługi Azure Digital bliźniaczych reprezentacji do punktu końcowego, należy zdefiniować trasę zdarzeń. **Interfejsy API** usługi Azure Digital bliźniaczych reprezentacji EventRoutes umożliwiają deweloperom tworzenie przepływów zdarzeń w całym systemie i w ramach usług podrzędnych. Przeczytaj więcej na temat tras zdarzeń w temacie [pojęcia: Routing Digital bliźniaczych reprezentacji Events](concepts-route-events.md).

W przykładach w tym artykule użyto zestawu SDK języka C#.

Trasy zdarzeń są definiowane przy użyciu interfejsów API płaszczyzny danych. Definicja trasy może zawierać następujące elementy:
* Identyfikator trasy, który ma być używany
* Nazwa punktu końcowego, który ma być używany
* Filtr określający, które zdarzenia są wysyłane do punktu końcowego. 

Jeśli nie ma identyfikatora trasy, żadne komunikaty nie są kierowane poza usługę Azure Digital bliźniaczych reprezentacji. Jeśli istnieje identyfikator trasy i filtr jest `true` , wszystkie komunikaty są kierowane do punktu końcowego. Jeśli istnieje identyfikator trasy i zostanie dodany inny filtr, komunikaty będą filtrowane zgodnie z filtrem.

Jedna trasa powinna zezwalać na wybranie wielu powiadomień i typów zdarzeń. 

`CreateEventRoute`to wywołanie zestawu SDK, które służy do dodawania trasy zdarzenia. Oto przykład użycia:

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

Można ograniczyć wysyłanie zdarzeń przez dodanie filtru do punktu końcowego.

Aby dodać filtr, można użyć żądania PUT do *protokołu https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-Preview* z następującą treścią:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Poniżej przedstawiono obsługiwane filtry tras.

| Nazwa filtru | Opis | Filtruj schemat | Obsługiwane wartości | 
| --- | --- | --- | --- |
| Typ | [Typ zdarzenia](./concepts-route-events.md#types-of-event-messages) przepływającego przez swoje cyfrowe wystąpienie sieci dwuosiowej | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Element źródłowy | Nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji | `"filter" : "source = '<hostname>'"`|  **Powiadomienia**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Dla telemetrii**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Podmiot | Opis zdarzenia w kontekście źródła zdarzeń powyżej | `"filter": " subject = '<subject>'"` | **Powiadomienia**: podmiot jest`<twinid>` <br> lub format identyfikatora URI dla tematów, które są jednoznacznie identyfikowane przez wiele części lub identyfikatorów:<br>`<twinid>/relationships/<relationshipid>`<br> **W przypadku telemetrii**: podmiot jest ścieżką składnika (Jeśli dane telemetryczne są emitowane z składnika przędzy), np `comp1.comp2` .. Jeśli dane telemetryczne nie są emitowane ze składnika, jego pole podmiotu jest puste. |
| Schemat danych | Identyfikator modelu DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **W przypadku telemetrii**: schemat danych to identyfikator modelu dwuosiowego lub składnika, który emituje dane telemetryczne <br>**Dla powiadomień**: schemat danych nie jest obsługiwany|
| Typ zawartości | Typ zawartości wartości danych | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Wersja specyfikacji | Wersja schematu zdarzeń, którego używasz | `"filter": "specversion = '<version>'"` | Musi być `1.0` . Oznacza to, że schemat CloudEvents w wersji 1,0 |
| PRAWDA/FAŁSZ | Umożliwia tworzenie trasy bez filtrowania lub wyłączanie trasy | `"filter" : "<true/false>"` | `true`= Trasa jest włączona bez filtrowania <br> `false`= Trasa jest wyłączona |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Istnieje również możliwość łączenia filtrów przy użyciu następujących operacji:

| Nazwa filtru | Filtruj schemat | Przykład | 
| --- | --- | --- |
| I/LUB | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| I/LUB | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Operacje zagnieżdżone | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> W trakcie okresu zapoznawczego obsługiwane są tylko ciągi równości (=,! =).

Po zaimplementowaniu lub zaktualizowaniu filtru zmiana może potrwać kilka minut w potoku danych.

## <a name="manage-endpoints-and-routes-with-cli"></a>Zarządzanie punktami końcowymi i trasami przy użyciu interfejsu wiersza polecenia

Punkty końcowe i trasy można także zarządzać za pomocą interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji. Polecenia można znaleźć w [opisie procedury: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Monitorowanie tras zdarzeń

Metryki routingu, takie jak liczba, opóźnienie i współczynnik błędów, można wyświetlić w [Azure Portal](https://portal.azure.com/). 

Na stronie głównej portalu Wyszukaj wystąpienie usługi Azure Digital bliźniaczych reprezentacji, aby uzyskać szczegółowe informacje. Wybierz opcję **metryki** z menu wystąpienia usługi Azure Digital bliźniaczych reprezentacji, aby wyświetlić stronę *metryk* .

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Strona metryki wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal":::

W tym miejscu można wyświetlić metryki dla wystąpienia i utworzyć widoki niestandardowe.

## <a name="next-steps"></a>Następne kroki

Przeczytaj o różnych typach komunikatów zdarzeń, które można odbierać:
* [Instrukcje: interpretowanie danych zdarzenia](how-to-interpret-event-data.md)
