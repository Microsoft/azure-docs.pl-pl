---
title: Przewodnik dla deweloperów — IoT Plug and Play Preview | Microsoft Docs
description: Opis Plug and Play IoT dla deweloperów
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9e6d13fedbfa495448164c1354868e12992dd71c
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856034"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>Przewodnik dla deweloperów Plug and Play IoT w wersji zapoznawczej

Usługa IoT Plug and Play w wersji zapoznawczej umożliwia tworzenie inteligentnych urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT.

W tym przewodniku opisano podstawowe kroki wymagane do utworzenia urządzenia zgodnego z [konwencjami Plug and Play IoT](concepts-convention.md)oraz dostępne interfejsy API REST, których można użyć do współpracy z urządzeniem.

Aby skompilować urządzenie IoT Plug and Play, wykonaj następujące kroki:

1. Upewnij się, że urządzenie korzysta z protokołu MQTT lub MQTT over WebSockets, aby nawiązać połączenie z usługą Azure IoT Hub.
1. Utwórz model [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) , aby opisać urządzenie. Aby dowiedzieć się więcej, zobacz temat [Omówienie składników w modelach Plug and Play IoT](concepts-components.md).
1. Zaktualizuj urządzenie, aby ogłosić w `model-id` ramach połączenia urządzenia.
1. Implementowanie danych telemetrycznych, właściwości i poleceń przy użyciu [konwencji Plug and Play IoT](concepts-convention.md)

Gdy wdrożenie urządzenia jest gotowe, użyj programu [Azure IoT Explorer](howto-use-iot-explorer.md) , aby sprawdzić, czy urządzenie jest zgodne z konwencjami Plug and Play IoT.

> [!Tip]
> Wszystkie fragmenty kodu w tym artykule korzystają z języka C#, ale koncepcje dotyczą dowolnego z dostępnych zestawów SDK dla języków C, Python, Node i Java.

## <a name="model-id-announcement"></a>Anonsowanie identyfikatora modelu

Aby ogłosić Identyfikator modelu, urządzenie musi uwzględnić je w informacjach o połączeniu:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Nowe `ClientOptions` Przeciążenie jest dostępne we wszystkich `DeviceClient` metodach użytych do zainicjowania połączenia.

Anons identyfikatora modelu został dodany do następnych wersji zestawów SDK

|SDK|Wersja|
|---|-------|
|C — ZESTAW SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Węzeł|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>Ładunek DPS

Urządzenia korzystające z [usługi Device Provisioning (DPS)](/iot-dps/) mogą zawierać `modelId` do użycia podczas procesu aprowizacji przy użyciu następującego ładunku JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementowanie danych telemetrycznych, właściwości i poleceń

Zgodnie z opisem w artykule [Omówienie składników w modelach Plug and Play IoT](concepts-components.md), konstruktory urządzeń muszą zdecydować, czy chcą używać składników do opisywania swoich urządzeń. W przypadku korzystania ze składników urządzenia muszą przestrzegać zasad opisanych w tej sekcji.

### <a name="telemetry"></a>Telemetria

Modele bez składników nie wymagają żadnej specjalnej właściwości.

W przypadku korzystania ze składników urządzenia muszą ustawiać Właściwość Message z nazwą składnika:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Właściwości tylko do odczytu

Modele bez składników nie wymagają żadnej specjalnej konstrukcji:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

W przypadku używania składników właściwości muszą być tworzone w obrębie nazwy składnika:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Właściwości do zapisu

Te właściwości można ustawić na urządzeniu lub zaktualizować je przez rozwiązanie. Jeśli rozwiązanie aktualizuje właściwość, klient otrzymuje powiadomienie jako wywołanie zwrotne w `DeviceClient` . Aby postępować zgodnie z konwencjami Plug and Play IoT, urządzenie musi poinformować usługę, że właściwość została pomyślnie odebrana.

#### <a name="report-a-writable-property"></a>Zgłoś właściwość do zapisu

Gdy urządzenie zgłosi modyfikowalną właściwość, musi zawierać `ack` wartości zdefiniowane w konwencjach.

Aby zgłosić modyfikowalną Właściwość bez składników:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Aby zgłosić właściwość z możliwością zapisu z składnika, sznur musi zawierać znacznik:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Subskrybowanie aktualizacji żądanych właściwości

Usługi mogą aktualizować żądane właściwości, które wyzwalają powiadomienie na podłączonych urządzeniach. To powiadomienie obejmuje zaktualizowane wymagane właściwości, w tym numer wersji identyfikującej aktualizację. Urządzenia muszą odpowiadać z tym samym `ack` komunikatem co raportowane właściwości.

Modele bez składników widzą pojedynczą Właściwość i tworzą zgłoszoną w `ack` otrzymanej wersji:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Sznurki urządzenia przedstawia właściwość w żądanych i zgłoszonych sekcjach:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Modele ze składnikami otrzymują odpowiednie właściwości opakowane przy użyciu nazwy składnika i powinny raportować `ack` raportowaną Właściwość:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Sznurki urządzenia dla składników przedstawia odpowiednie i zgłoszone sekcje w następujący sposób:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Polecenia

Modele bez składników otrzymują nazwę polecenia, która została wywołana przez usługę.

Modele ze składnikami otrzymają nazwę polecenia poprzedzoną składnikiem i `*` separatorem.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Ładunki żądań i odpowiedzi

Polecenia używają typów do definiowania ich ładunku żądania i odpowiedzi. Urządzenie musi deserializować przychodzący parametr wejściowy i serializować odpowiedź. Poniższy przykład pokazuje, jak zaimplementować polecenie z typami złożonymi zdefiniowanymi w ładunku:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Poniższe fragmenty kodu pokazują, jak urządzenie implementuje tę definicję polecenia, w tym typy używane do włączania serializacji i deserializacji:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Nazwy żądań i odpowiedzi nie są obecne w serializowanych ładunku przesyłanych za pośrednictwem sieci.

## <a name="interact-with-the-device"></a>Korzystanie z urządzenia 

Plug and Play IoT umożliwia korzystanie z urządzeń, które ogłosiły swój identyfikator modelu w usłudze IoT Hub. Można na przykład uzyskać dostęp do właściwości i poleceń urządzenia bezpośrednio.

Aby użyć urządzenia Plug and Play IoT, które jest połączone z Centrum IoT, użyj interfejsu API REST IoT Hub lub jednego z zestawów SDK języka IoT. W poniższych przykładach użyto interfejsu API REST IoT Hub. Bieżąca wersja interfejsu API to `2020-05-31-preview` . Dołącz `?api-version=2020-05-31` do wywołań pi Rest.

Jeśli urządzenie z termostatem zostanie wywołane `t-123` , uzyskasz wszystkie właściwości wszystkich interfejsów zaimplementowanych przez urządzenie przy użyciu interfejsu API REST Get:

```REST
GET /digitalTwins/t-123
```

To wywołanie będzie zawierać właściwość JSON `$metadata.$model` z identyfikatorem modelu ogłoszonym przez urządzenie.

Wszystkie właściwości wszystkich interfejsów są dostępne z `GET /DigitalTwin/{device-id}` szablonem interfejsu API REST, gdzie `{device-id}` jest identyfikatorem urządzenia:

```REST
GET /digitalTwins/{device-id}
```

Polecenia IoT Plug and Play Device można wywołać bezpośrednio. Jeśli `Thermostat` składnik na `t-123` urządzeniu ma `restart` polecenie, można wywołać go przy użyciu wywołania interfejsu API REST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Bardziej ogólnie rzecz biorąc, polecenia można wywołać za poorednictwem szablonu interfejsu API REST:

- `device-id`: Identyfikator urządzenia.
- `component-name`: nazwa interfejsu z sekcji Implements w modelu możliwości urządzenia.
- `command-name`: nazwa polecenia.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak modelowanie urządzeń, Oto kilka dodatkowych zasobów:

- [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Składniki modelu](./concepts-components.md)
