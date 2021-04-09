---
title: Wbudowane metody edgeAgent Direct — Azure IoT Edge
description: Monitorowanie wdrożenia IoT Edge i zarządzanie nim przy użyciu wbudowanych metod bezpośrednich w module IoT Edge Runtime Agent
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201109"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Komunikacja z edgeAgent za pomocą wbudowanych metod bezpośrednich

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Monitorowanie wdrożeń IoT Edge i zarządzanie nimi przy użyciu metod bezpośrednich zawartych w module IoT Edge Agent. Metody bezpośrednie są implementowane na urządzeniu, a następnie mogą być wywoływane z chmury. Agent IoT Edge obejmuje metody bezpośrednie, które ułatwiają zdalne monitorowanie urządzeń IoT Edge i zarządzanie nimi.

Aby uzyskać więcej informacji na temat metod bezpośrednich, sposobu ich używania i sposobu ich implementacji w własnych modułach, zobacz [Omówienie i wywoływanie metod bezpośrednich z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Nazwy tych metod bezpośrednich są obsługiwane bez uwzględniania wielkości liter.

## <a name="ping"></a>Ping

Metoda **ping** jest przydatna do sprawdzania, czy IoT Edge jest uruchomiona na urządzeniu, czy też ma otwarte połączenie z IoT Hub. Użyj tej metody bezpośredniej, aby wysłać polecenie ping do agenta IoT Edge i uzyskać jego stan. Pomyślne polecenie ping zwraca pusty ładunek i **"status": 200**.

Na przykład:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

W Azure Portal Wywołaj metodę z nazwą metody `ping` i pustym ŁADUNKIEM JSON `{}` .

![Wywołaj metodę Direct "ping" w Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Uruchom ponownie moduł

Metoda **RestartModule** umożliwia zdalne zarządzanie modułami uruchomionymi na urządzeniu IoT Edge. Jeśli moduł zgłasza stan awarii lub inne zachowanie w złej kondycji, można wyzwolić IoT Edge agenta, aby go ponownie uruchomić. Pomyślne ponowne uruchomienie polecenia powoduje zwrócenie pustego ładunku i **"status": 200**.

Metoda RestartModule jest dostępna w IoT Edge w wersji 1.0.9 i nowszych. 

Metody RestartModule Direct można użyć dla dowolnego modułu uruchomionego na urządzeniu IoT Edge, w tym do modułu edgeAgent. Jeśli jednak używasz tej metody bezpośredniej do zamykania edgeAgent, nie otrzymasz wyniku sukcesu, ponieważ połączenie zostanie przerwane, gdy moduł zostanie uruchomiony ponownie.

Na przykład:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

W Azure Portal Wywołaj metodę z nazwą metody `RestartModule` i następującym ŁADUNKIEM JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Wywołaj metodę bezpośrednią "RestartModule" w Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Metody diagnostyczne Direct

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): Pobierz dzienniki modułu w tekście odpowiedzi metody bezpośredniej.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): Pobierz dzienniki modułu i przekaż je do usługi Azure Blob Storage.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): Pobierz dzienniki modułu przy użyciu pakietu obsługi i Przekaż plik zip do usługi Azure Blob Storage.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): Sprawdź stan dzienników przekazywania lub obsługi żądania pakietu.

Te metody diagnostyki bezpośredniej są dostępne w wersji 1.0.10.

## <a name="next-steps"></a>Następne kroki

[Właściwości agenta IoT Edge i modułu IoT Edge Hub bliźniaczych reprezentacji](module-edgeagent-edgehub.md)
