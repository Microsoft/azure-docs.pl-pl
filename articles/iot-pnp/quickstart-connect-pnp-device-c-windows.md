---
title: Podłączanie przykładowego kodu urządzenia do programu IoT Plug and Play w wersji zapoznawczej do IoT Hub (Windows) | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykładowe kod urządzenia w systemie Windows, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769864"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej w systemie Windows do IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest zawarta w zestawie SDK usługi Azure IoT Hub Device. Deweloperzy rozwiązań mogą korzystać z narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio uwzględniono składnik **Menedżera pakietów NuGet** i **Programowanie aplikacji klasycznych w języku C++** .
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla systemu Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. `cmake` Utwórz podkatalog w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Jeśli CMAKE nie może znaleźć kompilatora języka C++, podczas uruchamiania poprzedniego polecenia pojawiają się błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Uruchom przykładową aplikację w zestawie SDK, aby symulować urządzenie Plug and Play IoT wysyłające dane telemetryczne do centrum IoT. Aby uruchomić przykładową aplikację, Użyj tych poleceń i przekaż _Parametry połączenia urządzenia_ jako parametr.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytywać definicje modeli interfejsów z urządzenia, wybierz pozycję **Ustawienia**. W menu ustawienia **na połączonym urządzeniu** mogą już występować konfiguracje Plug and Play; Jeśli tak nie jest, wybierz pozycję **+ Dodaj źródło definicji modułu** , a następnie **na podłączonym urządzeniu** , aby je dodać.

1. Na stronie Przegląd **urządzeń** Znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia jest nadal uruchomiona w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w programie Azure IoT Explorer jest raportowany jako _połączony_ (jeśli nie, należy **odświeżyć** do momentu). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** , aby odsłonić interfejs i elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Porada: łączenie z urządzeniem IoT Plug and Play Preview i korzystanie z niego](howto-develop-solution.md)
