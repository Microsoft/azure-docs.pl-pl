---
title: Przewodnik dla deweloperów urządzeń (C#) — IoT Plug and Play | Microsoft Docs
description: Opis Plug and Play IoT dla deweloperów urządzeń w języku C#
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: fd36006292de68e1433ccdfb721c1a4613d0658a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580073"
---
# <a name="iot-plug-and-play-device-developer-guide-c"></a>Przewodnik dla deweloperów urządzeń IoT Plug and Play (C#)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Anonsowanie identyfikatora modelu

Aby ogłosić Identyfikator modelu, urządzenie musi uwzględnić je w informacjach o połączeniu:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Nowe `ClientOptions` Przeciążenie jest dostępne we wszystkich `DeviceClient` metodach użytych do zainicjowania połączenia.

> [!TIP]
> W przypadku modułów i IoT Edge Użyj zamiast `ModuleClient` `DeviceClient` .

## <a name="dps-payload"></a>Ładunek DPS

Urządzenia korzystające z [usługi Device Provisioning (DPS)](../iot-dps/about-iot-dps.md) mogą zawierać `modelId` do użycia podczas procesu aprowizacji przy użyciu następującego ładunku JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementowanie danych telemetrycznych, właściwości i poleceń

Zgodnie z opisem w artykule [Omówienie składników w modelach Plug and Play IoT](concepts-components.md), konstruktory urządzeń muszą zdecydować, czy chcą używać składników do opisywania swoich urządzeń. W przypadku korzystania ze składników urządzenia muszą przestrzegać zasad opisanych w tej sekcji.

### <a name="telemetry"></a>Telemetria

Składnik domyślny nie wymaga żadnej specjalnej właściwości.

W przypadku używania składników zagnieżdżonych urządzenia muszą ustawiać Właściwość Message z nazwą składnika:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Właściwości tylko do odczytu

Raportowanie właściwości z domyślnego składnika nie wymaga żadnej specjalnej konstrukcji:

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

W przypadku używania składników zagnieżdżonych należy utworzyć właściwości w obrębie nazwy składnika:

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

Te właściwości można ustawić na urządzeniu lub zaktualizować je przez rozwiązanie. Jeśli rozwiązanie aktualizuje właściwość, klient otrzymuje powiadomienie jako wywołanie zwrotne w `DeviceClient` lub `ModuleClient` . Aby postępować zgodnie z konwencjami Plug and Play IoT, urządzenie musi poinformować usługę, że właściwość została pomyślnie odebrana.

#### <a name="report-a-writable-property"></a>Zgłoś właściwość do zapisu

Gdy urządzenie zgłosi modyfikowalną właściwość, musi zawierać `ack` wartości zdefiniowane w konwencjach.

Aby zgłosić modyfikowalną właściwość z domyślnego składnika:

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

Aby zgłosić modyfikowalną właściwość z składnika zagnieżdżonego, sznur musi zawierać znacznik:

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

Składnik domyślny widzi pojedynczą Właściwość i tworzy zgłoszoną w `ack` otrzymanej wersji:

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

Składnik zagnieżdżony otrzymuje odpowiednie właściwości opakowane nazwą składnika i powinien zgłosić `ack` raportowaną Właściwość:

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

Sznurki urządzenia dla składnika zagnieżdżonego przedstawia odpowiednie i zgłoszone sekcje w następujący sposób:

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

Składnik domyślny otrzymuje nazwę polecenia, ponieważ został wywołany przez usługę.

Składnik zagnieżdżony otrzymuje nazwę polecenia poprzedzoną nazwą składnika i `*` separatorem.

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

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
