---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: b7b0cfa20257ad07d8418c39af68724d613adf41
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820995"
---
W tym przewodniku Szybki start pokazano, jak utworzyć przykładową aplikację urządzenia IoT Plug and Play, połączyć ją z centrum IoT i wyświetlić wysyłane dane telemetryczne za pomocą narzędzia Eksplorator usługi Azure IoT. Przykładowa aplikacja jest napisana w języku C# i znajduje się w przykładach usługi Azure IoT dla języka C#. Konstruktor rozwiązań może użyć narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia IoT Plug and Play bez konieczności wyświetlania kodu urządzenia.

[![Przeglądaj kod](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki start w systemie Windows, na komputerze dewelopera musi być zainstalowane następujące oprogramowanie:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Pobieranie kodu

W tym przewodniku Szybki start przygotujemy środowisko projektowe, które umożliwia sklonowanie i skompilowanie repozytorium przykładów usługi Azure IoT dla języka C#.

Otwórz wiersz polecenia w folderze, który można wybrać. Uruchom następujące polecenie, aby sklonować [repozytorium GitHub Microsoft Azure Przykłady IoT dla języka C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) w tej lokalizacji:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Kompilowanie kod

Teraz możesz skompilować przykład w Visual Studio uruchomić go w trybie debugowania.

1. Otwórz plik *projektu azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Termostat\Termostat.csproj* w programie Visual Studio 2019.

1. W Visual Studio przejdź do właściwości **termostatu project >, > debugowania**. Następnie dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Dps |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Wartość zanotowana podczas ukończenia konfigurowanie [środowiska](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Wartość zanotowana podczas ukończenia konfigurowanie [środowiska](../articles/iot-pnp/set-up-environment.md) |

Teraz możesz skompilować przykład w Visual Studio i uruchomić go w trybie debugowania.

## <a name="run-the-device-sample"></a>Uruchamianie przykładowego urządzenia

Aby śledzić wykonywanie kodu w Visual Studio w systemie Windows, dodaj punkt przerwania do funkcji `main` w pliku program.cs.

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i zaczęło wysyłać dane telemetryczne do centrum. Utrzymuj działanie przykładu w kolejnych krokach.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Weryfikowanie kodu za pomocą eksploratora usługi Azure IoT

Po zakończeniu przykładowego klienta urządzenia użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje proste urządzenie IoT Plug and Play termostatu. Model implementowany w tym przykładzie nie używa IoT Plug and Play [składników](../articles/iot-pnp/concepts-modeling-guide.md). Plik [Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) dla urządzenia termostatu definiuje telemetrię, właściwości i polecenia implementowany przez urządzenie.

Kod urządzenia łączy się z centrum IoT przy użyciu standardowej `CreateFromConnectionString` metody. Urządzenie wysyła identyfikator modelu DTDL implementowany w żądaniu połączenia. Urządzenie, które wysyła identyfikator modelu, jest urządzeniem IoT Plug and Play urządzeniem:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Identyfikator modelu jest przechowywany w kodzie, jak pokazano w poniższym fragmencie kodu:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Kod, który aktualizuje właściwości, obsługuje polecenia i wysyła dane telemetryczne, jest identyczny z kodem urządzenia, które nie używa IoT Plug and Play danych.

W przykładzie użyto biblioteki JSON do analizowania obiektów JSON w ładunkach wysyłanych z centrum IoT:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
