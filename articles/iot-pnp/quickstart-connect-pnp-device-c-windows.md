---
title: Podłączenie przykładowego kodu urządzenia IoT Plug and Play Preview do usługi IoT Hub (Windows) | Dokumenty firmy Microsoft
description: Tworzenie i uruchamianie przykładowego kodu urządzenia IoT Plug and Play Preview w systemie Windows, który łączy się z centrum IoT hub. Użyj narzędzia Eksploratora IoT platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769864"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Szybki start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play Preview działającej w systemie Windows z centrum IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ten przewodnik Szybki start pokazuje, jak utworzyć przykładową aplikację urządzenia IoT Plug and Play, połączyć ją z centrum IoT hub i użyć narzędzia do eksplorowania Usługi Azure IoT, aby wyświetlić informacje, które wysyła do centrum. Przykładowa aplikacja jest napisana w języku C i jest uwzględniona w zestawie C urządzenia usługi Azure IoT Hub. Deweloper rozwiązania można użyć narzędzia eksploratora Usługi Azure IoT, aby zrozumieć możliwości urządzenia Typu IoT Plug and Play bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio jest dołączany składnik **Menedżera pakietów NuGet** i deweloper pulpitu z obciążeniem **języka C++.**
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalowanie eksploratora IoT platformy Azure

Pobierz i zainstaluj najnowszą wersję **eksploratora IoT platformy Azure** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzia, wybierając plik msi w obszarze "Zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start należy przygotować środowisko programistyczne, którego można użyć do klonowania i tworzenia sdk C urządzenia usługi Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [zestawY SDK C usługi Azure IoT i biblioteki](https://github.com/Azure/azure-iot-sdk-c) github w tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Użyj sdk urządzenia do tworzenia dołączonego przykładowego kodu. Aplikacja, która tworzysz symuluje urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. Utwórz `cmake` podkatalog w folderze głównym sdk urządzenia i przejdź do tego folderu:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć sdk urządzenia i wygenerowany skrót kodu:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Jeśli cmake nie może znaleźć kompilatora Języka C++, po uruchomieniu poprzedniego polecenia są otrzymują błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Uruchamianie próbki urządzenia

Uruchom przykładową aplikację w SDK, aby symulować urządzenie Typu Plug and Play IoT, które wysyła dane telemetryczne do centrum IoT hub. Aby uruchomić przykładową aplikację, użyj tych poleceń i przekaż _parametry połączenia urządzenia_ jako parametr.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do koncentratora. Zachowaj uruchomienie próbki podczas wykonywania kolejnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą Eksploratora IoT platformy Azure

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytać definicje modelu interfejsu z urządzenia, wybierz **ustawienia**. W menu Ustawienia **na podłączonym urządzeniu** może już pojawić się w konfiguracjach Plug and Play; jeśli tak nie jest, wybierz **+ Dodaj źródło definicji modułu,** a następnie **na podłączonym urządzeniu,** aby go dodać.

1. Na stronie **Przegląd urządzeń** znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia nadal działa w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w Eksploratorze Usługi Azure IoT jest raportowanie jako _Połączone_ (jeśli nie, naciśnij **odśwież,** dopóki nie jest). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs za pomocą **identyfikatora urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1,** aby odsłonić interfejs i prymitywy IoT Plug and Play — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do centrum IoT hub. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem IoT Plug and Play Preview i interakcję z nim](howto-develop-solution.md)
