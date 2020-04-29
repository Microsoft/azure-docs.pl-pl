---
title: Wyślij komunikaty zabezpieczeń urządzenia
description: Dowiedz się, jak wysyłać komunikaty zabezpieczeń przy użyciu Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310995"
---
# <a name="send-security-messages-sdk"></a>Wyślij zestaw SDK komunikatów zabezpieczeń

W tym przewodniku opisano Azure Security Center funkcji usługi IoT, gdy użytkownik zdecyduje się zbierać i wysyłać komunikaty zabezpieczeń urządzenia bez użycia Azure Security Center dla agenta IoT, a także wyjaśnia, jak to zrobić.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wysyłanie komunikatów zabezpieczeń przy użyciu zestawu Azure IoT C SDK
> * Wysyłanie komunikatów zabezpieczeń przy użyciu zestawu Azure IoT C# SDK
> * Wysyłanie komunikatów zabezpieczeń przy użyciu zestawu Azure IoT Python SDK
> * Wysyłanie komunikatów zabezpieczeń przy użyciu zestawu SDK środowiska Node. js platformy Azure IoT
> * Wysyłanie komunikatów zabezpieczeń przy użyciu zestawu Azure IoT Java SDK

## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center funkcji IoT

Azure Security Center dla IoT mogą przetwarzać i analizować dowolny rodzaj danych komunikatów zabezpieczeń, o ile wysyłane dane są zgodne ze [schematem Azure Security Center dla schematu IoT](https://aka.ms/iot-security-schemas) , a komunikat jest ustawiany jako komunikat zabezpieczeń.

## <a name="security-message"></a>Komunikat zabezpieczeń

Azure Security Center dla IoT definiuje komunikat zabezpieczeń przy użyciu następujących kryteriów:

- Jeśli wiadomość została wysłana z zestawem SDK usługi Azure IoT
- Jeśli komunikat jest zgodny ze [schematem komunikatu zabezpieczeń](https://aka.ms/iot-security-schemas)
- Jeśli wiadomość została ustawiona jako komunikat zabezpieczeń przed wysłaniem

Każdy komunikat zabezpieczeń zawiera metadane nadawcy, takie jak `AgentId`, `AgentVersion` `MessageSchemaVersion` i listę zdarzeń zabezpieczeń.
Schemat definiuje prawidłowe i wymagane właściwości komunikatu zabezpieczeń, w tym typy zdarzeń.

> [!NOTE]
> Wysłane komunikaty, które nie są zgodne ze schematem, są ignorowane. Przed zainicjowaniem wysyłania danych upewnij się, że został on zweryfikowany, ponieważ ignorowane komunikaty nie są obecnie przechowywane.

> [!NOTE]
> Komunikaty wysłane, które nie zostały ustawione jako komunikat zabezpieczeń przy użyciu zestawu Azure IoT SDK, nie będą kierowane do Azure Security Center dla potoku usługi IoT.

## <a name="valid-message-example"></a>Prawidłowy przykład wiadomości

W poniższym przykładzie pokazano prawidłowy obiekt komunikatu zabezpieczeń. Przykład zawiera metadane komunikatów i jedno `ProcessCreate` zdarzenie zabezpieczeń.

Po ustawieniu jako komunikat zabezpieczeń i wysłaniu ten komunikat zostanie przetworzony przez Azure Security Center dla IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Wysyłanie komunikatów zabezpieczeń

Wysyłanie komunikatów zabezpieczeń *bez* korzystania z Azure Security Center dla agenta IoT przy użyciu [zestawu SDK urządzeń Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [zestawu SDK dla usługi](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)Azure IoT C# oraz zestawu SDK [środowiska](https://github.com/Azure/azure-iot-sdk-java) [Node. js](https://github.com/Azure/azure-iot-sdk-node)platformy Azure IoT, [zestawu SDK usługi](https://github.com/Azure/azure-iot-sdk-python)Azure IoT

Aby wysłać dane urządzenia z urządzeń do przetwarzania przez Azure Security Center IoT, użyj jednego z następujących interfejsów API, aby oznaczyć komunikaty do poprawnego routingu do Azure Security Center potoku przetwarzania IoT.

Wszystkie dane, które są wysyłane, nawet jeśli są oznaczone prawidłowym nagłówkiem, muszą również być zgodne z [Azure Security Center dla schematu wiadomości IoT](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Wyślij interfejs API komunikatów zabezpieczeń

Interfejs API **wysyłania komunikatów zabezpieczeń** jest obecnie dostępny w językach C i C#, Python, Node. js i Java.

#### <a name="c-api"></a>INTERFEJS API JĘZYKA C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>Interfejs API języka C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Interfejs API środowiska Node. js

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Interfejs API języka Python

Aby użyć interfejsu API języka Python, musisz zainstalować pakiet [Azure-IoT-Device](https://pypi.org/project/azure-iot-device/).

Korzystając z interfejsu API języka Python, można wysłać komunikat zabezpieczeń za pośrednictwem modułu lub za pośrednictwem urządzenia przy użyciu unikatowych parametrów połączenia urządzenia lub modułu. Korzystając z następującego przykładowego skryptu języka Python, z urządzeniem, użyj **IoTHubDeviceClient**i z modułem, użyj **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Interfejs API języka Java

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o [architekturze](architecture.md) Azure Security Center dla usługi IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- Omówienie [zaleceń](concept-recommendations.md)
- Informacje o [alertach](concept-security-alerts.md)
