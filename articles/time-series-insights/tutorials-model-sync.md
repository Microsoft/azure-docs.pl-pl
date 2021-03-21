---
title: Synchronizacja modeli między usługą Azure Digital bliźniaczych reprezentacji i Time Series Insights | Microsoft Docs
description: Najlepsze rozwiązania i narzędzia służące do tłumaczenia modelu zasobów w ADT na model zasobów w ramach TSI platformy Azure
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682906"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Synchronizacja modeli między usługą Azure Digital Twins i Time Series Insights Gen2

W tym artykule opisano najlepsze rozwiązania i narzędzia służące do tłumaczenia modelu zasobów w usłudze Azure Digital bliźniaczych reprezentacji (ADT) na model zasobów w Azure Time Series Insights (TSI).  Ten artykuł jest drugą częścią serii samouczków z dwiema częściami wyjaśniającą integrację usługi Azure Digital bliźniaczych reprezentacji z programem Azure Time Series Insights. Integracja usługi Azure Digital bliźniaczych reprezentacji z programem Time Series Insights umożliwia archiwizowanie i śledzenie historii telemetrii i obliczonych właściwości cyfrowych bliźniaczych reprezentacji. Ta seria samouczków jest przeznaczona dla deweloperów pracujących nad integracją Time Series Insights z usługą Azure Digital bliźniaczych reprezentacji. W części 1 opisano  [ustanawianie potoku danych, który zawiera rzeczywiste dane szeregów czasowych z usługi Azure Digital bliźniaczych reprezentacji do Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md) , a druga część serii samouczków zawiera opis synchronizacji modelu zasobów między usługą Azure Digital bliźniaczych reprezentacji i Time Series Insights. W tym samouczku wyjaśniono najlepsze rozwiązania dotyczące wyboru i ustanowienia konwencji nazewnictwa dla identyfikatora szeregów czasowych (TS ID) oraz ręcznego ustanawiania hierarchii w modelu szeregów czasowych (TSM).

## <a name="choosing-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Identyfikator szeregów czasowych jest unikatowym identyfikatorem używanym do identyfikowania zasobów w Time Series Insights. Dane szeregów czasowych (telemetrii z pola, które są parami wartości czasowych) są reprezentowane przy użyciu zmiennych wymienionych w obszarze TSID. W programie Azure Digital bliźniaczych reprezentacji właściwości bliźniaczych i telemetrii są używane do reprezentowania stanu sznurka i pomiarów produkowanych odpowiednio przez sznurek. Począwszy od bieżącego projektu TSM, TSIDs musi być unikatowy. Użycie identyfikatorów bliźniaczych bliźniaczych reprezentacji w usłudze Azure Digital bliźniaczych reprezentacji lub w połączeniu z właściwością lub telemetrią zawsze ma unikatowy identyfikator TS w TSM. W typowym przypadku, **_`<Twin ID>`_** będzie to identyfikator TSID, a nazwy właściwości i telemetrii będą zmiennymi w TSM. Istnieją jednak przypadki użycia, w których preferowane są hierarchie zasobów w Time Series Insights być spłaszczone przy użyciu formatu kluczy złożonych, takich jak **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Przyjrzyjmy się przykładowi do wyjaśnienia w późniejszym przypadku. Weź pod uwagę pokój, który jest modelem w formie sznurka i który ma Room22 o IDENTYFIKATORze bliźniaczym. Właściwość ustawienia temperatury ma zostać przetłumaczona jako **_identyfikator TSID Room22_TempSetting_** i pomiar temperatury do **_Room22_TempMea_** w TSM.

[![Wybieranie identyfikatora szeregów czasowych](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing szeregi czasowe

Contextualization danych (głównie przestrzenne) w Time Series Insights jest realizowana za pomocą hierarchii zasobów, a ta sama służy do łatwego nawigowania po danych za pomocą widoku drzewa w Eksploratorze Time Series Insights. Typy szeregów czasowych i hierarchie są definiowane przy użyciu modelu szeregów czasowych (TSM) w Time Series Insights. Typy w pomocy TSM definiują zmienne, podczas gdy poziomy hierarchii i wartości pól wystąpienia są używane do konstruowania widoku drzewa w Eksploratorze Time Series Insights. Aby uzyskać więcej informacji na temat TSM, zobacz [dokumentację Time Series Insights online](./concepts-model-overview.md).

W usłudze Azure Digital bliźniaczych reprezentacji połączenie między elementami zawartości jest wyrażane przy użyciu relacji bliźniaczych. Relacje z przędzą są po prostu wykresem połączonych zasobów. Jednak w usłudze Time Series Insights relacje między elementami zawartości są hierarchiczne. Oznacza to, że zasoby współużytkują typ nadrzędny-podrzędny od relacji i są reprezentowane przy użyciu struktury drzewa. Aby przetłumaczyć informacje o relacji z usługi Azure Digital bliźniaczych reprezentacji na hierarchie Time Series Insights, musimy wybrać odpowiednie relacje hierarchiczne z usługi Azure Digital bliźniaczych reprezentacji. Usługa Azure Digital bliźniaczych reprezentacji używa otwartego standardu języka modelowego o nazwie Digital bliźniaczy Definition Language (DTDL). W modelach DTDL są opisane przy użyciu wariantu JSON o nazwie JSON-LD. Zapoznaj się z [dokumentacją DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) , aby uzyskać szczegółowe informacje na temat specyfikacji.

[![Połączenie między elementami zawartości](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Tłumaczenie prezentacji grafu w usłudze Azure Digital bliźniaczych reprezentacji na strukturę drzewa w Time Series Insights

Poniższe sekcje samouczka przechwytują kilka podstawowych scenariuszy ręcznego tłumaczenia struktury grafów w usłudze Azure Digital bliźniaczych reprezentacji na strukturę drzewa w Time Series Insights.

Przykładowy system: w tym samouczku użyto poniższego przykładu, aby wyjaśnić Koncepcje opisane poniżej. Jest to prosty, fikcyjny system zarządzania, który ma jedno piętro i dwa pokoje. Ma trzy termostaty, jeden w każdym z pomieszczeń i drugi wspólny dla podłogi. Ponadto ma również pomiar przepływu wody z Room21 do Room22 za pośrednictwem połączenia potoku między pokojem. Spojrzenie na relację przestrzenną między bliźniaczych reprezentacji, ma oba typy relacji.

1. Najczęstsze, hierarchiczne relacje. Na przykład Building40-> Floor01-> FloorTS *->
1. To nie jest tak popularne, cykliczne relacje. Na przykład rozpoczynając od Building40, FlowMtr może być śledzony przez dwie różne ścieżki.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      Gdzie "Flow" to rzeczywista wartość telemetrii mierząca przepływ wodny między Room21 i Room22

> [!Note]
>
> `*` FloorTS to FloorThermoStat i FlowMtr oznacza Miernik przepływu i zwykle wybierany jako identyfikator TSID. Temperatura i przepływ są nieprzetworzonymi danymi telemetrycznymi określonymi jako zmienne w Time Series Insights.

Uwzględniając bieżące ograniczenie Time Series Insights nie można przedstawić jednego elementu zawartości w wielu gałęziach, w poniższych sekcjach opisano modelowanie relacji hierarchicznych i cyklicznych w Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Przypadek 1: relacja hierarchiczna (nadrzędna-podrzędna)

Jest to najbardziej typowy typ relacji między bliźniaczych reprezentacji. Modelowanie ze ścisłą relacją nadrzędny-podrzędny jest wyjaśnione na poniższej ilustracji. Pola wystąpień i identyfikatory TSID są wyprowadzane z identyfikatorów bliźniaczych, jak pokazano poniżej. Podczas ręcznego aktualizowania pól wystąpień przy użyciu Eksploratora Time Series Insights, w sekcji poniżej o nazwie "Aktualizowanie pól wystąpienia przy użyciu interfejsów API" opisano nasłuchiwanie zmian modelu w usłudze Azure Digital bliźniaczych reprezentacji i aktualizowanie pól wystąpienia w Time Series Insights za pomocą usługi Azure Functions.

[![Mapowanie identyfikatorów przędzy](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mapowanie bliźniaczych identyfikatorów 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Przypadek 2: relacja cykliczna

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Relacja cykliczna w usłudze Azure Digital bliźniaczych reprezentacji w relacji z jedną hierarchią w Time Series Insights

Mając na względzie, że identyfikator TSID musi być unikatowy i może być przedstawiony tylko w jednej hierarchii, ten przypadek reprezentuje _element "FlowMtr"_ z telemetrią o nazwie _"Flow"_ w obszarze " _Room21"_. W przyszłości, gdy Time Series Insights może obsługiwać wiele reprezentacji szeregów czasowych w TSM, dane telemetryczne _"Flow"_ byłyby reprezentowane w _"pokoju 21"_ i _"pokoju 22"_

Poniższy zrzut ekranu przedstawia ręczne mapowanie bliźniaczych identyfikatorów w usłudze Azure Digital bliźniaczych reprezentacji na pole instance w TSM i hierarchię w hierarchii w Time Series Insights.

[![Mapowanie identyfikatorów przędzy w usłudze Azure Digital bliźniaczych reprezentacji](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Relacje cykliczne w usłudze Azure Digital bliźniaczych reprezentacji z wieloma hierarchiami w Time Series Insights przy użyciu duplikatów

W części 1 samouczka wyjaśniono, jak program kliencki (funkcja platformy Azure) pomaga transferować dane telemetryczne z IoT Hub lub innych źródeł zdarzeń do usługi Azure Digital bliźniaczych reprezentacji. Takie podejście sugeruje użycie tego samego programu klienckiego do wprowadzania aktualizacji odpowiednich właściwości nadrzędnego bliźniaczych reprezentacji. W podanym przykładzie, podczas odczytywania telemetrii FlowMtr z IoT Hub i aktualizowania właściwości "Flow" w FlowMtrch bliźniaczych, program może również zaktualizować odpowiednie właściwości we wszystkich możliwych bliźniaczych reprezentacji nadrzędnych źródła. W naszym przykładzie będzie to "outflowmea" (identyfikowane przy użyciu właściwości "Flow" relacji) właściwości Room21 i "inflowmea" klasy Room22.  Poniższy zrzut ekranu przedstawia środowisko użytkownika końcowego w Eksploratorze Time Series Insights. Należy zauważyć, że mamy duplikaty danych, przyjmując takie podejście.

[![Eksplorator Time Series Insights](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Poniższy fragment kodu przedstawia sposób, w jaki aplikacja kliencka może nawigować do relacji bliźniaczych za pomocą cyfrowych interfejsów API usługi Azure bliźniaczych reprezentacji.

> [!Note]
>
> W tym przykładzie fragmentu kodu założono, że czytelnicy znają [część 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) samouczka, a ta zmiana kodu została wprowadzona wewnątrz funkcji "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Aktualizowanie pól wystąpień przy użyciu interfejsów API

W tej części samouczka wyjaśniono nasłuchiwanie zmian modelu w usłudze Azure Digital bliźniaczych reprezentacji, takich jak tworzenie, usuwanie bliźniaczych reprezentacji lub zmiana relacji między bliźniaczych reprezentacji i aktualizowaniem pól wystąpień i hierarchii programowo przy użyciu interfejsów API modelu Time Series Insights. Ta metoda aktualizacji modelu Time Series Insights jest zazwyczaj uzyskiwana za pośrednictwem usługi Azure Functions. W przypadku usługi Azure Digital bliźniaczych reprezentacji powiadomienia o zdarzeniach, takie jak dodawanie lub usuwanie dwuosiowe mogą być kierowane do usług podrzędnych, takich jak Event Hubs, które z kolei mogą być przekazywane do usługi Azure Functions. Więcej informacji o routingu i filtrowaniu zdarzeń wyjaśniono w [tym miejscu](../digital-twins/how-to-manage-routes-portal.md).  W pozostałej części tej sekcji opisano Używanie interfejsów API modelu Time Series Insights w usłudze Azure Functions do aktualizowania Time Series Insights modelu w odpowiedzi na dodanie sznurka (jeden typ zmiany modelu) w usłudze Azure Digital bliźniaczych reprezentacji.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Otrzymywanie i identyfikowanie powiadomienia o zdarzeniu dodawania dwuosiowego

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Tworzenie Time Series Insights klienta i Dodawanie szczegółów wystąpienia

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Stosowanie informacji o hierarchii do wystąpienia

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Następne kroki

Trzecia z serii samouczków pokazuje, jak wykonywać zapytania dotyczące danych historycznych z usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API Time Series Insights. Jest to prace w toku, a sekcja zostanie zaktualizowana, gdy będzie gotowa. W międzyczasie Zachęcamy czytelników do odwoływania się do [dokumentacji interfejsu API zapytań dotyczących danych Time Series Insights](./concepts-query-overview.md).
