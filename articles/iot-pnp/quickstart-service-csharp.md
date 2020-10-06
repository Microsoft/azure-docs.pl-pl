---
title: Korzystanie z urządzenia Plug and Play IoT połączonego z rozwiązaniem Azure IoT (C#) | Microsoft Docs
description: Użyj języka C#, aby nawiązać połączenie z urządzeniem IoT Plug and Play i korzystać z niego, które jest połączone z rozwiązaniem usługi Azure IoT.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b35268cd8d36901f750225713407c5392e5c429e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759173"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play, które jest połączone z rozwiązaniem (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play upraszcza IoT, umożliwiając korzystanie z możliwości urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka C# do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, na komputerze deweloperskim musi być zainstalowane następujące oprogramowanie:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Usługi [git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonowanie repozytorium zestawu SDK przy użyciu przykładowego kodu

Jeśli zakończysz [Przewodnik Szybki Start: Podłącz przykładową aplikację urządzenia IoT Plug and Play działającą w systemie Windows do IoT Hub (C#)](quickstart-connect-device-csharp.md), masz już Sklonowane repozytorium.

Sklonuj przykłady z repozytorium Microsoft Azure IoT SDK dla programu .NET GitHub. Otwórz wiersz polecenia w wybranym folderze. Uruchom następujące polecenie, aby sklonować [Microsoft Azure IoT przykłady dla repozytorium programu .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start użyto przykładowego urządzenia z termostatem, które jest zapisywana w języku C# jako urządzenie Plug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz plik projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* w programie Visual Studio 2019.

1. W programie Visual Studio przejdź do **Właściwości programu Project > termostat > Debuguj**. Następnie Dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DOKUMENTY |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Wartość zanotowana po zakończeniu [konfigurowania środowiska](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | My-PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Wartość zanotowana po zakończeniu [konfigurowania środowiska](set-up-environment.md) |


1. Teraz można skompilować przykład w programie Visual Studio i uruchomić go w trybie debugowania.

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego wystąpienia programu Visual Studio, musisz sprawdzić, czy przykład usługi działa.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W obszarze [Konfigurowanie środowiska dla usługi IoT Plug and Play Przewodniki Szybki Start i samouczki](set-up-environment.md) zostały utworzone dwie zmienne środowiskowe w celu skonfigurowania przykładu w celu połączenia z Centrum IoT i urządzeniem:

* **IOTHUB_CONNECTION_STRING**: parametry połączenia usługi IoT Hub, dla których wykonano wcześniej adnotację.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku C#, aby można było korzystać z właśnie skonfigurowanego przykładowego urządzenia.

1. W innym wystąpieniu programu Visual Studio Otwórz projekt *Azure-IoT-Samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* .

1. W programie Visual Studio przejdź do **Właściwości programu Project > termostat > Debuguj**. Następnie Dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | My-PnP-Device |
    | IOTHUB_CONNECTION_STRING | Wartość zanotowana po zakończeniu [konfigurowania środowiska](set-up-environment.md) |

1. Teraz można skompilować przykład w programie Visual Studio i uruchomić go w trybie debugowania.

### <a name="get-digital-twin"></a>Uzyskaj wieloosiową cyfrę

Poniższy fragment kodu przedstawia sposób, w jaki aplikacja usługi pobiera dwuosiową cyfrę:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> Ten przykład używa przestrzeni nazw **Microsoft. Azure. Devices. Client;** z **klienta usługi IoT Hub**. Aby dowiedzieć się więcej na temat pobierania identyfikatora modelu, zobacz [przewodnik dewelopera](concepts-developer-guide-service.md).

Ten kod generuje następujące dane wyjściowe:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

Poniższy fragment kodu przedstawia sposób używania *poprawki* do aktualizowania właściwości za pomocą dwuosiowej:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Ten kod generuje następujące dane wyjściowe z urządzenia, gdy usługa aktualizuje `targetTemperature` Właściwość:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Wywołaj polecenie

Poniższy fragment kodu przedstawia sposób wywołania polecenia:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Ten kod generuje następujące dane wyjściowe z urządzenia, gdy usługa wywołuje `getMaxMinReport` polecenie:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dewelopera modelowania Plug and Play IoT](concepts-developer-guide-device-csharp.md)
