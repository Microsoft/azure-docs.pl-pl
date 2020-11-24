---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: b72bbe944a2709d639fdbc3ca467e6950a4c9fbf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511522"
---
## <a name="model-id-announcement"></a>Anonsowanie identyfikatora modelu

Aby ogłosić Identyfikator modelu, urządzenie musi uwzględnić je w informacjach o połączeniu:

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

`ClientOptions`Przeciążenie jest dostępne we wszystkich `DeviceClient` metodach użytych do zainicjowania połączenia.

> [!TIP]
> W przypadku modułów i IoT Edge Użyj zamiast `ModuleClient` `DeviceClient` .

## <a name="dps-payload"></a>Ładunek DPS

Urządzenia korzystające z [usługi Device Provisioning (DPS)](../articles/iot-dps/about-iot-dps.md) mogą zawierać `modelId` do użycia podczas procesu aprowizacji przy użyciu następującego ładunku JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementowanie danych telemetrycznych, właściwości i poleceń

Zgodnie z opisem w artykule [Omówienie składników w modelach Plug and Play IoT](../articles/iot-pnp/concepts-components.md), konstruktory urządzeń muszą zdecydować, czy chcą używać składników do opisywania swoich urządzeń. W przypadku korzystania ze składników urządzenia muszą przestrzegać zasad opisanych w tej sekcji.

### <a name="telemetry"></a>Telemetria

Składnik domyślny nie wymaga żadnej specjalnej właściwości.

W przypadku używania składników zagnieżdżonych urządzenia muszą ustawiać Właściwość Message z nazwą składnika:

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

### <a name="read-only-properties"></a>Właściwości tylko do odczytu

Raportowanie właściwości z domyślnego składnika nie wymaga żadnej specjalnej konstrukcji:

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

W przypadku używania składników zagnieżdżonych należy utworzyć właściwości w obrębie nazwy składnika:

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
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

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Aby zgłosić modyfikowalną właściwość z składnika zagnieżdżonego, sznur musi zawierać znacznik:

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
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

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
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
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Składnik zagnieżdżony otrzymuje odpowiednie właściwości opakowane nazwą składnika i powinien zgłosić `ack` raportowaną Właściwość:

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
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

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

#### <a name="request-and-response-payloads"></a>Ładunki żądań i odpowiedzi

Polecenia używają typów do definiowania ich ładunku żądania i odpowiedzi. Urządzenie musi deserializować przychodzący parametr wejściowy i serializować odpowiedź.

Poniższy przykład pokazuje, jak zaimplementować polecenie z typami złożonymi zdefiniowanymi w ładunku:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Poniższe fragmenty kodu pokazują, jak urządzenie implementuje tę definicję polecenia, w tym typy używane do włączania serializacji i deserializacji:

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> Nazwy żądań i odpowiedzi nie są obecne w serializowanych ładunku przesyłanych za pośrednictwem sieci.
