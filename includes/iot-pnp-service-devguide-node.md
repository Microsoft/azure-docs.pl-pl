---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244862"
---
Dostępne są również następujące zasoby:

- [ Dokumentacja referencyjna zestawuNode.js SDK](/javascript/api/azure-iothub)
- [Przykłady klienta usługi](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Cyfrowe przykłady bliźniaczych reprezentacji](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Przykłady klienta usługi IoT Hub

W tej sekcji przedstawiono przykłady języka JavaScript przy użyciu klienta usługi IoT Hub oraz klasy **rejestru** i **klienta** . Klasa **rejestru** służy do korzystania z stanu urządzenia za pomocą usługi Device bliźniaczych reprezentacji. Można również użyć klasy **rejestru** do [wykonywania zapytań dotyczących rejestracji urządzeń](../articles/iot-hub/iot-hub-devguide-query-language.md) w IoT Hub. Użyj klasy **Client** do wywołania poleceń na urządzeniu. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) dla urządzenia definiuje właściwości i polecenia implementowane przez urządzenie. W fragmentach kodu `deviceId` zmienna zawiera identyfikator urządzenia urządzenia Plug and Play IoT zarejestrowanego w centrum IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Pobierz sznurki urządzenia i Identyfikator modelu

Aby uzyskać sznurki urządzenia i Identyfikator modelu urządzenia Plug and Play IoT, które połączyło się z Centrum IoT:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Aktualizacja sznurka urządzenia

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości na urządzeniu. W przykładzie pokazano, jak należy pobrać sznurki przed aktualizacją. Właściwość jest zdefiniowana w domyślnym składniku urządzenia:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości składnika. W przykładzie pokazano, jak należy pobrać sznurki przed aktualizacją. Właściwość jest definiowana w składniku **thermostat1** :

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

W przypadku właściwości w składniku poprawka właściwości wygląda podobnie do poniższego przykładu:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Call — polecenie

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia zdefiniowanego w składniku domyślnym:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia w składniku. Polecenie jest zdefiniowane w składniku **thermostat1** :

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Przykłady IoT Hub cyfrowych bliźniaczych

Klasa **DigitalTwinClient** służy do korzystania z stanu urządzenia za pomocą funkcji Digital bliźniaczych reprezentacji. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) dla urządzenia definiuje właściwości i polecenia implementowane przez urządzenie.

W tej sekcji przedstawiono przykłady języka JavaScript za pomocą interfejsu API Digital bliźniaczych reprezentacji.

`digitalTwinId`Zmienna przechowuje identyfikator urządzenia Plug and Play IoT zarejestrowanego w centrum IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Uzyskaj cyfrowe sznurki i Identyfikator modelu

Aby uzyskać cyfrowe sznurki i Identyfikator modelu urządzenia Plug and Play IoT, które połączyło się z Centrum IoT:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Aktualizuj wieloosiową cyfrę

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości na urządzeniu. Właściwość jest zdefiniowana w domyślnym składniku urządzenia:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości składnika. Właściwość jest definiowana w składniku **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Call — polecenie

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia zdefiniowanego w składniku domyślnym:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia w składniku. Polecenie jest zdefiniowane w składniku **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Odczytaj dane telemetryczne urządzenia

Urządzenia Plug and Play IoT wysyłają dane telemetryczne zdefiniowane w modelu DTDL, aby IoT Hub. Domyślnie IoT Hub kieruje dane telemetryczne do punktu końcowego Event Hubs, gdzie można go wykorzystać. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Poniższy fragment kodu przedstawia sposób odczytywania danych telemetrycznych z domyślnego punktu końcowego Event Hubs. Kod w tym fragmencie kodu jest pobierany z IoT Hub szybkiego startu [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie go przy użyciu aplikacji zaplecza](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Poniższe dane wyjściowe z poprzedniego kodu pokazują dane telemetryczne dotyczące temperatury wysyłane przez wieloskładnikowe urządzenie **TemperatureController** Plug and Play IoT. `dt-subject`Właściwość system zawiera nazwę składnika, który wysłał dane telemetryczne. W tym przykładzie dwa składniki są `thermostat1` i `thermostat2` zdefiniowane w modelu DTDL. `dt-dataschema`Właściwość system zawiera identyfikator modelu:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Odczytaj powiadomienia o zmianie bliźniaczych urządzeń

IoT Hub można skonfigurować w celu wygenerowania powiadomień o zmianach z urządzeń z systemem, które będą kierowane do obsługiwanego punktu końcowego. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych > zdarzeń niezwiązanych z telemetrią](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kod pokazywany w poprzednim fragmencie kodu JavaScript generuje następujące dane wyjściowe, gdy IoT Hub generuje powiadomienia o zmianie przędzy urządzenia dla nieskładnikowego termostatu. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Kod pokazywany we wcześniejszym fragmencie kodu JavaScript generuje następujące dane wyjściowe, gdy IoT Hub generuje powiadomienia o zmianach w urządzeniu z urządzeniami ze składnikami. Ten przykład przedstawia dane wyjściowe, gdy urządzenie czujnika temperatury ze składnikiem termostatu generuje powiadomienia. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Odczytaj wielocyfrowe powiadomienia o zmianach

Można skonfigurować IoT Hub, aby generować wielocyfrowe powiadomienia o zmianach w celu kierowania do obsługiwanego punktu końcowego. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych > zdarzeń niezwiązanych z telemetrią](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kod pokazywany w poprzednim fragmencie kodu JavaScript generuje następujące dane wyjściowe, gdy IoT Hub generuje dwuskładnikowe powiadomienia o zmianie dla urządzenia z termostatem. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Kod pokazywany we wcześniejszym fragmencie kodu JavaScript generuje następujące dane wyjściowe, gdy IoT Hub generuje cyfrowe powiadomienia o zmianach w urządzeniu ze składnikami. Ten przykład przedstawia dane wyjściowe, gdy urządzenie czujnika temperatury ze składnikiem termostatu generuje powiadomienia. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
