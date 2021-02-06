---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: dea63ba4939579f3f227d2eac05eb124ada1e4ec
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616660"
---
## <a name="model-id-announcement"></a>Anonsowanie identyfikatora modelu

Aby ogłosić Identyfikator modelu, urządzenie musi uwzględnić je w informacjach o połączeniu:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> W przypadku modułów i IoT Edge Użyj zamiast `ModuleClient` `Client` .

> [!TIP]
> Jest to jedyny czas, w którym urządzenie może ustawić identyfikator modelu, nie można go zaktualizować po nawiązaniu połączenia z urządzeniem.

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

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Właściwości tylko do odczytu

Raportowanie właściwości z domyślnego składnika nie wymaga żadnej specjalnej konstrukcji:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

W przypadku używania składników zagnieżdżonych należy utworzyć właściwości w obrębie nazwy składnika.:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Właściwości do zapisu

Te właściwości można ustawić na urządzeniu lub zaktualizować je przez rozwiązanie. Jeśli rozwiązanie aktualizuje właściwość, klient otrzymuje powiadomienie jako wywołanie zwrotne w `Client` lub `ModuleClient` . Aby postępować zgodnie z konwencjami Plug and Play IoT, urządzenie musi poinformować usługę, że właściwość została pomyślnie odebrana.

#### <a name="report-a-writable-property"></a>Zgłoś właściwość do zapisu

Gdy urządzenie zgłosi modyfikowalną właściwość, musi zawierać `ack` wartości zdefiniowane w konwencjach.

Aby zgłosić modyfikowalną właściwość z domyślnego składnika:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Sznurek urządzenia zostanie zaktualizowany przy użyciu następnej raportowanej właściwości:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Aby zgłosić modyfikowalną właściwość z składnika zagnieżdżonego, sznur musi zawierać znacznik:

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Subskrybowanie aktualizacji żądanych właściwości

Usługi mogą aktualizować żądane właściwości, które wyzwalają powiadomienie na podłączonych urządzeniach. To powiadomienie obejmuje zaktualizowane wymagane właściwości, w tym numer wersji identyfikującej aktualizację. Urządzenia muszą odpowiadać z tym samym `ack` komunikatem co raportowane właściwości.

Składnik domyślny widzi pojedynczą Właściwość i tworzy zgłoszoną w `ack` otrzymanej wersji:

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
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

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
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

Składnik domyślny otrzymuje nazwę polecenia, ponieważ został wywołany przez usługę.

Składnik zagnieżdżony otrzymuje nazwę polecenia poprzedzoną nazwą składnika i `*` separatorem.

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>Ładunki żądań i odpowiedzi

Polecenia używają typów do definiowania ich ładunku żądania i odpowiedzi. Urządzenie musi deserializować przychodzący parametr wejściowy i serializować odpowiedź. Poniższy przykład pokazuje, jak zaimplementować polecenie z typami złożonymi zdefiniowanymi w ładunku:

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

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> Nazwy żądań i odpowiedzi nie są obecne w serializowanych ładunku przesyłanych za pośrednictwem sieci.
