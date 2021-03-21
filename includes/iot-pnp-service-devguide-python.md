---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510978"
---
Dostępne są również następujące zasoby:

- [Dokumentacja referencyjna zestawu Python SDK](/python/api/azure-iot-hub/azure.iot.hub)
- [Przykłady klienta usługi](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Cyfrowe przykłady bliźniaczych reprezentacji](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Przykłady klienta usługi IoT Hub

W tej sekcji przedstawiono przykłady języka Python przy użyciu klienta usługi IoT Hub oraz klasy **IoTHubRegistryManager** i **CloudToDeviceMethod** . Klasa **IoTHubRegistryManager** służy do korzystania z stanu urządzenia przy użyciu usługi Device bliźniaczych reprezentacji. Można również użyć klasy **IoTHubRegistryManager** do [wykonywania zapytań dotyczących rejestracji urządzeń](../articles/iot-hub/iot-hub-devguide-query-language.md) w IoT Hub. Klasa **CloudToDeviceMethod** jest używana do wywoływania poleceń na urządzeniu. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) dla urządzenia definiuje właściwości i polecenia implementowane przez urządzenie. W fragmentach kodu `device_id` zmienna zawiera identyfikator urządzenia urządzenia Plug and Play IoT zarejestrowanego w centrum IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Pobierz sznurki urządzenia i Identyfikator modelu

Aby uzyskać sznurki urządzenia i Identyfikator modelu urządzenia Plug and Play IoT, które połączyło się z Centrum IoT:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Aktualizacja sznurka urządzenia

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości na urządzeniu. W przykładzie pokazano, jak należy uzyskać splot `etag` przed aktualizacją. Właściwość jest zdefiniowana w domyślnym składniku urządzenia:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości składnika. W przykładzie pokazano, jak należy uzyskać splot `ETag` przed aktualizacją. Właściwość jest definiowana w składniku **thermostat1** :

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia w składniku. Polecenie jest zdefiniowane w składniku **thermostat1** :

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Przykłady IoT Hub cyfrowych bliźniaczych

Klasa **DigitalTwinClient** służy do korzystania z stanu urządzenia za pomocą funkcji Digital bliźniaczych reprezentacji. Model [DTDL](../articles/iot-pnp/concepts-digital-twin.md) dla urządzenia definiuje właściwości i polecenia implementowane przez urządzenie.

`device_id`Zmienna przechowuje identyfikator urządzenia Plug and Play IoT zarejestrowanego w centrum IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Uzyskaj cyfrowe sznurki i Identyfikator modelu

Aby uzyskać cyfrowe sznurki i Identyfikator modelu urządzenia Plug and Play IoT, które połączyło się z Centrum IoT:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Aktualizuj wieloosiową cyfrę

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości na urządzeniu. Właściwość jest zdefiniowana w domyślnym składniku urządzenia:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Poniższy fragment kodu przedstawia sposób aktualizowania `targetTemperature` właściwości składnika. Właściwość jest definiowana w składniku **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Call — polecenie

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia zdefiniowanego w składniku domyślnym:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Poniższy fragment kodu przedstawia sposób wywołania `getMaxMinReport` polecenia w składniku. Polecenie jest zdefiniowane w składniku **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Odczytaj dane telemetryczne urządzenia

Urządzenia Plug and Play IoT wysyłają dane telemetryczne zdefiniowane w modelu DTDL, aby IoT Hub. Domyślnie IoT Hub kieruje dane telemetryczne do punktu końcowego Event Hubs, gdzie można go wykorzystać. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Poniższy fragment kodu przedstawia sposób odczytywania danych telemetrycznych z domyślnego punktu końcowego Event Hubs. Kod w tym fragmencie kodu jest pobierany z IoT Hub szybkiego startu [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie go przy użyciu aplikacji zaplecza](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Poniższe dane wyjściowe z poprzedniego kodu pokazują dane telemetryczne dotyczące temperatury wysyłane przez urządzenie Plug and Play **usługi IoT,** które ma tylko domyślny składnik. `dt-dataschema`Właściwość system zawiera identyfikator modelu:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Poniższe dane wyjściowe z poprzedniego kodu pokazują dane telemetryczne dotyczące temperatury wysyłane przez wieloskładnikowe urządzenie **TemperatureController** Plug and Play IoT. `dt-subject`Właściwość system zawiera nazwę składnika, który wysłał dane telemetryczne. W tym przykładzie dwa składniki są `thermostat1` i `thermostat2` zdefiniowane w modelu DTDL. `dt-dataschema`Właściwość system zawiera identyfikator modelu:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Odczytaj powiadomienia o zmianie bliźniaczych urządzeń

IoT Hub można skonfigurować w celu wygenerowania powiadomień o zmianach z urządzeń z systemem, które będą kierowane do obsługiwanego punktu końcowego. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych > zdarzeń niezwiązanych z telemetrią](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kod przedstawiony w poprzednim fragmencie kodu w języku Python generuje następujące dane wyjściowe, gdy IoT Hub generuje powiadomienia o zmianie przędzy urządzenia dla nieskładnikowego termostatu. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Kod pokazywany w poprzednim fragmencie kodu w języku Python generuje następujące dane wyjściowe, gdy IoT Hub generuje powiadomienia o zmianach w urządzeniu z urządzeniami ze składnikami. Ten przykład przedstawia dane wyjściowe, gdy urządzenie czujnika temperatury ze składnikiem termostatu generuje powiadomienia. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Odczytaj wielocyfrowe powiadomienia o zmianach

Można skonfigurować IoT Hub, aby generować wielocyfrowe powiadomienia o zmianach w celu kierowania do obsługiwanego punktu końcowego. Aby dowiedzieć się więcej, zobacz [używanie IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych > zdarzeń niezwiązanych z telemetrią](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Kod przedstawiony w poprzednim fragmencie kodu w języku Python generuje następujące dane wyjściowe, gdy IoT Hub generuje wieloskładnikowe powiadomienia o zmianie dla urządzenia z termostatem. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Kod pokazywany w poprzednim fragmencie kodu w języku Python generuje następujące dane wyjściowe, gdy IoT Hub generuje wielocyfrowe powiadomienia o zmianach dla urządzenia ze składnikami. Ten przykład przedstawia dane wyjściowe, gdy urządzenie czujnika temperatury ze składnikiem termostatu generuje powiadomienia. Właściwości aplikacji `iothub-message-schema` oraz `opType` Informacje o typie powiadomienia o zmianach:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
