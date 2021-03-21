---
title: Jak przenieść ładunek między urządzeniami a usługą Azure Device Provisioning
description: W tym dokumencie opisano sposób transferu ładunku między urządzeniami a usługą Device Provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259523"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Jak transferować ładunki między urządzeniami i usługą DPS
Czasami usługa DPS potrzebuje więcej danych z urządzeń, aby prawidłowo udostępnić je właściwym IoT Hub i że dane muszą być dostarczone przez urządzenie. Na odwrót usługa DPS może zwrócić dane do urządzenia w celu ułatwienia logiki po stronie klienta. 

## <a name="when-to-use-it"></a>Zastosowanie
Tej funkcji można użyć jako rozszerzenia [alokacji niestandardowej](./how-to-use-custom-allocation-policies.md). Na przykład, chcesz przydzielić urządzenia w oparciu o model urządzenia bez interwencji człowieka. W takim przypadku zostanie użyta [alokacja niestandardowa](./how-to-use-custom-allocation-policies.md). Można skonfigurować urządzenie do zgłaszania informacji o modelu w ramach [wywołania rejestracji urządzenia](/rest/api/iot-dps/runtimeregistration/registerdevice). Usługa DPS przekaże ładunek urządzenia do elementu webhook niestandardowej alokacji. A funkcja może zdecydować, które IoT Hub urządzenie będzie przejdzie, gdy otrzyma informacje o modelu urządzenia. Podobnie, jeśli element webhook chce zwrócić pewne dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Urządzenie wysyła ładunek danych do programu DPS
Gdy urządzenie wysyła [wywołanie rejestracji urządzenia](/rest/api/iot-dps/runtimeregistration/registerdevice) do punktu dystrybucji, wywołanie Register można rozszerzyć, aby przyjąć inne pola w treści. Treść wygląda następująco: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>Usługa DPS zwraca dane do urządzenia
Jeśli element webhook niestandardowych zasad alokacji chce zwrócić pewne dane do urządzenia, przekaże dane z powrotem jako ciąg w odpowiedzi elementu webhook. Ta zmiana znajduje się w sekcji ładunek poniżej. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Obsługa zestawu SDK
Ta funkcja jest dostępna w językach C, C#, JAVA i Node.js [Client SDK](./index.yml).  

## <a name="next-steps"></a>Następne kroki
* Opracowywanie zawartości przy użyciu [zestawu Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) dla platformy Azure IoT Hub i platformy Azure IoT Hub Device Provisioning Service