---
title: Pozyskiwanie danych telemetrycznych z usługi IoT Hub
titleSuffix: Azure Digital Twins
description: Zobacz, jak pozyskiwanie komunikatów telemetrycznych urządzenia z IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725855"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Pozyskiwanie danych telemetrycznych IoT Hub na platformie Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji jest oparta na danych z urządzeń IoT i innych źródeł. Typowym źródłem danych urządzenia do użycia w usłudze Azure Digital bliźniaczych reprezentacji jest [IoT Hub](../iot-hub/about-iot-hub.md).

W trakcie korzystania z wersji zapoznawczej proces pozyskiwania danych do usługi Azure Digital bliźniaczych reprezentacji polega na skonfigurowaniu zewnętrznego zasobu obliczeniowego, takiego jak [Funkcja platformy Azure](../azure-functions/functions-overview.md), która odbiera dane, i używa [interfejsów API DigitalTwins](how-to-use-apis-sdks.md) do ustawiania właściwości lub zdarzeń telemetrii na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) . 

Ten dokument zawiera instrukcje dotyczące tworzenia funkcji platformy Azure, która może pozyskać dane telemetryczne z IoT Hub.

## <a name="example-telemetry-scenario"></a>Przykładowy scenariusz telemetrii

Ten sposób przedstawia sposób wysyłania komunikatów z IoT Hub do usługi Azure Digital bliźniaczych reprezentacji przy użyciu funkcji platformy Azure. Istnieje wiele możliwych konfiguracji i strategie dopasowania, których można użyć w tym przypadku, ale przykład tego artykułu zawiera następujące części:
* Urządzenie termometru w IoT Hub ze znanym IDENTYFIKATORem urządzenia.
* Cyfrowa dwuosiowa do reprezentowania urządzenia, o zgodnym IDENTYFIKATORze
* Cyfrowe sznurki reprezentujące Pokój

> [!NOTE]
> W tym przykładzie zastosowano prostą zgodność identyfikatora między IDENTYFIKATORem urządzenia i odpowiadającym mu IDENTYFIKATORem cyfrowej sznurka, ale można zapewnić bardziej zaawansowane mapowania z urządzenia do jego sznurka (na przykład z tabelą mapowania).

Za każdym razem, gdy urządzenie termometru wysyła zdarzenie telemetrii temperatury, należy zaktualizować właściwość *temperatury* *dla sznurka* . W takim przypadku mapowanie odbywa się na podstawie zdarzenia telemetrii na urządzeniu do metody ustawiającej właściwości na dwuosiowej cyfrowej. Informacje o topologii z [grafu bliźniaczyego](concepts-twins-graph.md) są używane do *znajdowania sznurka* , a następnie można ustawić właściwość sznurka. W innych scenariuszach użytkownicy mogą chcieć ustawić właściwość na pasujących sznurach (w tym przykładzie, z IDENTYFIKATORem *123*). Usługa Azure Digital bliźniaczych reprezentacji zapewnia dużą elastyczność, aby zdecydować, jak dane telemetryczne są mapowane na bliźniaczych reprezentacji. 

Ten scenariusz przedstawiono na poniższym diagramie:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Urządzenie IoT Hub wysyła dane telemetryczne dotyczące temperatury poprzez IoT Hub, Event Grid lub tematy systemowe do funkcji platformy Azure, która aktualizuje właściwość temperatury bliźniaczych reprezentacji w usłudze Azure Digital bliźniaczych reprezentacji." border="false":::

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem tego przykładu należy wykonać poniższe wymagania wstępne.
1. Utwórz centrum IoT. Aby uzyskać instrukcje, zobacz sekcję *tworzenie IoT Hub* w [tym IoT Hub przewodniku szybki start](../iot-hub/quickstart-send-telemetry-cli.md) .
2. Utwórz co najmniej jedną funkcję platformy Azure, aby przetwarzać zdarzenia z IoT Hub. Zapoznaj się z tematem [jak to zrobić: Konfigurowanie funkcji platformy Azure do przetwarzania danych](how-to-create-azure-function.md) w celu utworzenia podstawowej funkcji platformy Azure, która może łączyć się z usługą Azure Digital bliźniaczych reprezentacji i wywoływać funkcje interfejsu API Digital bliźniaczych reprezentacji platformy Azure. Pozostała część tego, jak zostanie utworzona w tej funkcji.
3. Skonfiguruj miejsce docelowe zdarzenia dla danych centrum. W [Azure Portal](https://portal.azure.com/)przejdź do wystąpienia IoT Hub. W obszarze *zdarzenia*Utwórz subskrypcję dla funkcji platformy Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: Dodawanie subskrypcji zdarzeń":::

4. Na stronie *Tworzenie subskrypcji zdarzeń* Wypełnij pola w następujący sposób:
   * W obszarze *szczegóły subskrypcji zdarzeń*Nazwij subskrypcję, którą chcesz
   * W obszarze *typy zdarzeń*wybierz pozycję *Telemetria urządzenia* jako typ zdarzenia do filtrowania
      - Dodaj filtry do innych typów zdarzeń, jeśli chcesz.
   * W obszarze *Szczegóły punktu końcowego*wybierz funkcję platformy Azure jako punkt końcowy

## <a name="create-an-azure-function-in-visual-studio"></a>Tworzenie funkcji platformy Azure w programie Visual Studio

Ta sekcja używa tych samych kroków uruchamiania programu Visual Studio i szkieletu funkcji platformy Azure z pozostałej części [: Konfigurowanie funkcji platformy Azure do przetwarzania danych](how-to-create-azure-function.md). Szkielet obsługuje uwierzytelnianie i tworzy klienta usługi, gotowy do przetwarzania danych i wywoływania interfejsów API Digital bliźniaczych reprezentacji w odpowiedzi na platformie Azure. 

Sercem funkcji szkieletowej jest:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

W kolejnych krokach dodasz do niego konkretny kod służący do przetwarzania zdarzeń telemetrii IoT z IoT Hub.  

## <a name="add-telemetry-processing"></a>Dodaj przetwarzanie danych telemetrycznych

Zdarzenia telemetrii są dostarczane w formie komunikatów z urządzenia. Pierwszym krokiem w dodawaniu kodu przetwarzania danych telemetrycznych jest wyodrębnienie odpowiedniej części tego komunikatu urządzenia ze zdarzenia Event Grid. 

Różne urządzenia mogą być w inny sposób struktury swoich komunikatów, więc kod dla tego kroku zależy od podłączonego urządzenia. 

Poniższy kod przedstawia przykład prostego urządzenia, które wysyła dane telemetryczne jako kod JSON. Przykład wyodrębnia identyfikator urządzenia, które wysłało wiadomość, a także wartość temperatury.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Odwołaj się, że celem tego ćwiczenia jest zaktualizowanie temperatury *pokoju* w grafie bliźniaczym. Oznacza to, że naszym celem wiadomości nie jest dwuosiowa cyfra, która jest skojarzona z tym urządzeniem, ale do *pokoju* , który jest elementem nadrzędnym. Można znaleźć jednostkę nadrzędną przy użyciu wartości identyfikatora urządzenia wyodrębnionej z komunikatu telemetrii przy użyciu powyższego kodu.

Aby to zrobić, Użyj interfejsów API Digital bliźniaczych reprezentacji na platformie Azure, aby uzyskać dostęp do przychodzących relacji do urządzenia, które reprezentuje sznury (w tym przypadku ma ten sam identyfikator co urządzenie). Z przychodzącej relacji można znaleźć identyfikator elementu nadrzędnego z poniższym fragmentem kodu.

Poniższy fragment kodu pokazuje, jak pobrać przychodzące relacje dla sznurka:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Element nadrzędny dla sznurka jest we właściwości *SourceId* relacji.

Jest to dość powszechne dla modelu sznurka reprezentującego urządzenie do tylko jednej relacji przychodzącej. W takim przypadku można wybrać następującą relację (i tylko) zwracaną. Jeśli modele umożliwiają stosowanie wielu typów relacji do tego dwuosiowego, może być konieczne określenie dalszych opcji wyboru spośród wielu relacji przychodzących. Typowym sposobem, aby to zrobić, jest wybranie relacji przez `RelationshipName` . 

Po umieszczeniu identyfikatora bliźniaczych sznurów reprezentujących *pomieszczenie*można "zastosować poprawki" (wprowadzić aktualizacje). W tym celu należy użyć następującego kodu:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Pełny kod funkcji platformy Azure

Używając kodu z wcześniejszych przykładów, oto cała funkcja platformy Azure w kontekście:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Funkcja narzędziowa do znajdowania relacji przychodzących:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

I funkcja narzędziowa do naprawienia sznurka:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Teraz masz funkcję platformy Azure, która jest wyposażona w odczytywanie i interpretowanie danych scenariusza pochodzących z IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Lokalne debugowanie aplikacji funkcji platformy Azure

Istnieje możliwość lokalnego debugowania usługi Azure Functions z wyzwalaczem Event Grid. Aby uzyskać więcej informacji na ten temat, zobacz [debugowanie Event Grid wyzwalacza lokalnie](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z informacjami dotyczącymi transferu danych przychodzących i wychodzących przy użyciu usługi Azure Digital bliźniaczych reprezentacji:
* [Koncepcje: integracja z innymi usługami](concepts-integration.md)
