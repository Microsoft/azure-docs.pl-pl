---
title: Jak podłączyć przykład IoT Plug and Play Bridge działającego w systemie Linux lub Windows do centrum IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj mostek Plug and Play IoT w systemie Linux lub Windows, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6670f654685f8d5cdcaf55d2b1679738a57ecab4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042800"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Jak podłączyć przykład IoT Plug and Play Bridge działającego w systemie Linux lub Windows do IoT Hub

W tym przykładzie przedstawiono sposób tworzenia przykładowego adaptera środowiskowego usługi IoT Plug and Play Bridge, łączenia go z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Usługa IoT Plug and Play Bridge jest zapisywana w języku C i zawiera zestaw SDK urządzeń Azure IoT dla języka C. Po zakończeniu tego samouczka powinno być możliwe uruchomienie mostka Plug and Play IoT i wyświetlenie telemetrii raportów IT w programie Azure IoT Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="zrzut ekranu przedstawiający program Azure IoT Explorer z tabelą zgłoszonych danych telemetrycznych (wilgotności, temperatury) z mostka Plug and Play IoT.":::

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start można uruchomić w systemie Linux lub Windows. Polecenia powłoki w tym przewodniku z instrukcjami są zgodne z Konwencją systemu Windows dla separatorów ścieżek " `\` ", jeśli są używane w systemie Linux, pamiętaj o zamianie tych separatorów dla " `/` ".

Wymagania wstępne różnią się w zależności od systemu operacyjnego:

### <a name="linux"></a>Linux

W tym przewodniku szybki start przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym przewodniku Szybki Start zostały przetestowane przy użyciu Ubuntu 18,04.

Aby ukończyć ten przewodnik Szybki Start w systemie Linux, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Linux:

Zainstaluj oprogramowanie w **zatoce**, **git**, **CMAKE**i wszystkie wymagane zależności przy użyciu `apt-get` polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy wersja programu `cmake` jest powyżej **2.8.12** , a wersja **tej** wersji jest większa niż **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas [instalowania](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) programu Visual Studio dołączysz **programowanie klasyczne przy użyciu języka C++** .
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

Aby móc korzystać z przykładowego urządzenia w drugiej części tego przewodnika Szybki Start, użyj narzędzia **Azure IoT Explorer** . [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _IoT Hub parametry połączenia_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Możesz również użyć narzędzia Azure IoT Explorer, aby znaleźć parametry połączenia usługi IoT Hub.

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Wyświetl model

Aby wyświetlić urządzenie podczas nawiązywania połączenia z Centrum IoT, użyjesz programu Azure IoT Explorer w dalszej części kroków. W programie Azure IoT Explorer wymagana jest lokalna kopia pliku modelu zgodna z **identyfikatorem modelu** wysyłanym przez urządzenie. Plik modelu umożliwia Eksplorator IoT wyświetlanie danych telemetrycznych, właściwości i poleceń wdrażanych przez urządzenie.

Po pobraniu kodu w kroku poniżej zawiera przykładowe pliki modelu w `pnpbridge/docs/schema` folderze. Aby przeprowadzić przygotowanie programu Azure IoT Explorer:

1. Utwórz folder o nazwie *modele* na komputerze lokalnym.
1. Wyświetl [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) i Zapisz plik JSON w folderze *modele*
1. Wyświetl [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) i Zapisz plik JSON w folderze *modele* .

## <a name="download-the-code"></a>Pobieranie kodu

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium usługi [IoT Plug and Play Bridge](https://aka.ms/iotplugandplaybridge) w tej lokalizacji:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Po sklonowaniu repozytorium usługi IoT Plug and Play do maszyny Otwórz wiersz polecenia administracyjne i przejdź do katalogu sklonowanego repozytorium:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Oczekiwanie na ukończenie tej operacji.

>[!NOTE]
> Jeśli występują problemy z niepowodzeniem aktualizacji modułu podrzędnego klonowania git, jest to znany problem z ścieżkami plików systemu Windows i usługą git See: https://github.com/msysgit/git/pull/110 . Aby rozwiązać ten problem, można wypróbować następujące polecenie: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Konfigurowanie pliku JSON konfiguracji

Po sklonowaniu repozytorium usługi IoT Plug and Play do maszyny przejdź do `pnpbridge/docs/schema` katalogu sklonowanego repozytorium, w którym można znaleźć [konfigurację JSON](https://aka.ms/iot-pnp-bridge-env-config) lub `config.json` próbkowanie czujnika środowiska mostka. Więcej informacji na temat plików konfiguracji można znaleźć w [dokumencie koncepcje dotyczące programu IoT Plug and Play Bridge](concepts-iot-pnp-bridge.md).

W przypadku `root-_interface_model_id` pola należy SKOPIOWAĆ Identyfikator modelu Plug and Play IoT, który identyfikuje model dla urządzenia. W tym przykładzie jest to `dtmi:com:example:SampleDevice;1`. Zmodyfikuj następujące parametry w węźle **pnp_bridge_parameters** w `config.json` pliku ":

  Używanie parametrów połączenia (Uwaga: symmetric_key musi być zgodna z kluczem sygnatury dostępu współdzielonego w parametrach połączenia):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Po wprowadzeniu `config.json` pliku plik powinien wyglądać podobnie do następującego:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Po skompilowaniu mostka należy go umieścić w tym samym katalogu, w którym znajduje `config.json` się mostek, lub określić jego ścieżkę podczas uruchamiania.

## <a name="build-the-iot-plug-and-play-bridge"></a>Tworzenie mostka Plug and Play IoT

Przejdź do folderu *pnpbridge* w katalogu repozytorium.

W przypadku systemu Windows wykonaj następujące czynności w [wiersz polecenia dla deweloperów dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Podobnie w przypadku systemu Linux Uruchom następujące czynności:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>W systemie Windows można otworzyć rozwiązanie wygenerowane przez polecenie CMAKE w programie Visual Studio 2019. Otwórz plik projektu *azure_iot_pnp_bridge. sln* w katalogu CMAKE i ustaw projekt *pnpbridge_bin* jako projekt startowy w rozwiązaniu. Teraz można skompilować przykład w programie Visual Studio i uruchomić go w trybie debugowania.

## <a name="start-the-iot-plug-and-play-bridge"></a>Uruchamianie mostka Plug and Play IoT

 Uruchom przykład usługi IoT Plug and Play Bridge dla czujników środowiska, przechodząc do folderu *pnpbridge* i uruchamiając następujące polecenie w wierszu polecenia:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

* [Co to jest IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Zobacz informacje dotyczące deweloperów usługi GitHub dla usługi IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [Mostek Plug and Play IoT w serwisie GitHub](https://aka.ms/iotplugandplaybridge)