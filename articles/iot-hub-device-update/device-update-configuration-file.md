---
title: Opis aktualizacji urządzenia dla usługi Azure IoT Hub Configuration | Microsoft Docs
description: Opis aktualizacji urządzenia dla pliku konfiguracji IoT Hub platformy Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662946"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Aktualizacja urządzenia dla IoT Hub pliku konfiguracji

"adu-conf.txt" to opcjonalny plik, który można utworzyć, aby zarządzać następującymi konfiguracjami.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["producent"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["model"]
* DeviceInformation. Manufacturer
* DeviceInformation. model
* Parametry połączenia urządzenia (jeśli nie są znane przez agenta aktualizacji urządzeń).

## <a name="purpose"></a>Przeznaczenie
Agent aktualizacji urządzeń najpierw podejmie próbę pobrania `manufacturer` `model` wartości i z urządzenia do użycia dla [warstwy interfejsu](device-update-agent-overview.md#the-interface-layer). Jeśli to się nie powiedzie, Agent aktualizacji urządzenia następnym szuka pliku "adu-conf.txt" i użyje wartości z tego miejsca. Jeśli obie próby zakończą się niepowodzeniem, Agent aktualizacji urządzenia będzie używał wartości [domyślnych](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) .

Dowiedz się więcej o [interfejsie adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) i [interfejsie informacji o urządzeniu](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Lokalizacja pliku

W systemie Linux w partycji lub dysku o nazwie `adu` Utwórz plik tekstowy o nazwie "adu-conf.txt" z następującymi polami.

## <a name="list-of-fields"></a>Lista pól

|Nazwa|Opis|
|-----------|--------------------|
|connection_string|Wstępnie udostępniane parametry połączenia, które może być używane przez urządzenie do nawiązywania połączenia z IoT Hub. Uwaga: nie jest to wymagane w przypadku aprowizacji agenta aktualizacji urządzeń za pomocą [usługi Azure IoT Identity Service](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Zgłoszone przez `AzureDeviceUpdateCore:4.ClientMetadata:4` interfejs do klasyfikowania urządzenia na potrzeby wdrożenia aktualizacji.|
|aduc_model|Zgłoszone przez `AzureDeviceUpdateCore:4.ClientMetadata:4` interfejs do klasyfikowania urządzenia na potrzeby wdrożenia aktualizacji.|
|manufacturer|Zgłoszone przez agenta aktualizacji urządzenia jako część `DeviceInformation` interfejsu.|
|model|Zgłoszone przez agenta aktualizacji urządzenia jako część `DeviceInformation` interfejsu.|

## <a name="example-adu-conftxt-file-contents"></a>Przykładowa zawartość pliku "adu-conf.txt"

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
