---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 7658b5a51c9e24e5530114ebca7455a0ed3ea097
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511531"
---
## <a name="model-id-announcement"></a>Anonsowanie identyfikatora modelu

Aby ogłosić Identyfikator modelu, urządzenie musi uwzględnić je w informacjach o połączeniu:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> W przypadku modułów i IoT Edge Użyj zamiast `IoTHubModuleClient` `IoTHubDeviceClient` .

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

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Właściwości tylko do odczytu

Raportowanie właściwości z domyślnego składnika nie wymaga żadnej specjalnej konstrukcji:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

Te właściwości można ustawić na urządzeniu lub zaktualizować je przez rozwiązanie. Jeśli rozwiązanie aktualizuje właściwość, klient otrzymuje powiadomienie jako wywołanie zwrotne w `IoTHubDeviceClient` lub `IoTHubModuleClient` . Aby postępować zgodnie z konwencjami Plug and Play IoT, urządzenie musi poinformować usługę, że właściwość została pomyślnie odebrana.

#### <a name="report-a-writable-property"></a>Zgłoś właściwość do zapisu

Gdy urządzenie zgłosi modyfikowalną właściwość, musi zawierać `ack` wartości zdefiniowane w konwencjach.

Aby zgłosić modyfikowalną właściwość z domyślnego składnika:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
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

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
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

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> Nazwy żądań i odpowiedzi nie są obecne w serializowanych ładunku przesyłanych za pośrednictwem sieci.
