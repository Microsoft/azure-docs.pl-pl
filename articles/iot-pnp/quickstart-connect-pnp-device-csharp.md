---
title: Łączenie przykładowego kodu urządzenia w usłudze IoT Plug and Play w wersji zapoznawczej IoT Hub | Microsoft Docs
description: Za pomocą języka C# (.NET), kompilowania i uruchamiania usługi IoT Plug and Play Podgląd przykładowego kodu urządzenia, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77121020"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej do IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C# (z platformą .NET) i jest dostępna jako część kolekcji Azure IoT przykłady dla języka C# (.NET). Deweloperzy rozwiązań mogą korzystać z narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować platformę .NET Core 3,0 na swoim komputerze deweloperskim. Tę wersję zestaw .NET Core SDK można pobrać z wielu platform [pobrać z programu .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Możesz sprawdzić wersję programu .NET, która znajduje się na komputerze deweloperskim, uruchamiając następujące polecenie w lokalnym oknie terminalu: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania przykładów usługi Azure IoT dla języka C# (.NET).

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować z repozytorium GitHub [przykłady usługi Azure IoT dla języka C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Wykonanie tej operacji może potrwać kilka minut.

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Sklonowany przykładowy kod służy do kompilowania aplikacji symulowanej na urządzeniu, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W oknie terminalu lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu **Azure-IoT-Samples-CSharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** . 

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom przykładową aplikację, aby symulować urządzenie Plug and Play IoT, które wysyła telemetrię do centrum IoT. W tym samym oknie terminalu, aby skompilować wymagane pakiety i uruchomić przykładową aplikację, użyj następującego polecenia:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Zobaczysz komunikat informujący o tym, że urządzenie zostało pomyślnie zarejestrowane i oczekuje na aktualizacje z chmury. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytywać definicje modeli interfejsów z urządzenia, wybierz pozycję **Ustawienia**. W menu ustawienia **na połączonym urządzeniu** mogą już występować konfiguracje Plug and Play; Jeśli tak nie jest, wybierz pozycję **+ Dodaj źródło definicji modułu** , a następnie **na podłączonym urządzeniu** , aby je dodać.

1. Na stronie Przegląd **urządzeń** Znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia jest nadal uruchomiona w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w programie Azure IoT Explorer jest raportowany jako _połączony_ (jeśli nie, należy **odświeżyć** do momentu). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: csharp_sdk_sample: EnvironmentalSensor: 1** , aby odsłonić interfejs i elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Porada: łączenie z urządzeniem IoT Plug and Play Preview i korzystanie z niego](howto-develop-solution.md)
