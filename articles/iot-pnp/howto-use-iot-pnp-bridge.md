---
title: Jak podłączyć przykład IoT Plug and Play Bridge działającego w systemie Linux lub Windows do centrum IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj mostek Plug and Play IoT w systemie Linux lub Windows, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834106"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Jak podłączyć przykład IoT Plug and Play Bridge działającego w systemie Linux lub Windows do IoT Hub

W tym artykule opisano sposób tworzenia przykładowego adaptera środowiskowego usługi IoT Plug and Play Bridge, nawiązywania połączenia z Centrum IoT i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Usługa IoT Plug and Play Bridge jest zapisywana w języku C i zawiera zestaw SDK urządzeń Azure IoT dla języka C. Po zakończeniu tego samouczka powinno być możliwe uruchomienie mostka Plug and Play IoT i wyświetlenie telemetrii raportów IT w programie Azure IoT Explorer:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Zrzut ekranu przedstawiający program Azure IoT Explorer z tabelą zgłoszonych danych telemetrycznych (wilgotności, temperatury) z usługi IoT Plug and Play Bridge.":::

## <a name="prerequisites"></a>Wymagania wstępne

Przykład można uruchomić w artykule w systemie Windows lub Linux. Polecenia powłoki w tym przewodniku z instrukcjami są zgodne z Konwencją systemu Windows dla separatorów ścieżek " `\` ", jeśli są używane w systemie Linux, pamiętaj o zamianie tych separatorów dla " `/` ".

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

Aby móc korzystać z przykładowego urządzenia w drugiej części tego artykułu, użyj narzędzia **Azure IoT Explorer** . [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _IoT Hub parametry połączenia_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego artykułu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego artykułu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Pobieranie i uruchamianie mostka

W tym artykule dostępne są dwie opcje uruchomienia mostka. Oto co możesz zrobić:

- Pobierz wstępnie utworzony plik wykonywalny i uruchom go zgodnie z opisem w tej sekcji.
- Pobierz kod źródłowy, a następnie [Skompiluj i uruchom mostek](#build-and-run-the-bridge) zgodnie z opisem w poniższej sekcji.

Aby pobrać i uruchomić mostek:

1. Przejdź do [strony wydań](https://github.com/Azure/iot-plug-and-play-bridge/releases)Plug and Play IoT.
1. Pobierz prekompilowany plik wykonywalny dla systemu operacyjnego: **pnpbridge_bin.exe** dla systemu Windows lub **pnpbridge_bin** for Linux.
1. Pobierz przykładową [config.jsw](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) pliku konfiguracyjnym dla przykładu czujnika środowiska. Upewnij się, że plik konfiguracji znajduje się w tym samym folderze co plik wykonywalny.
1. Edytuj *config.jsw* pliku:

    - Dodaj `connection-string` wartość będącą _ciągiem połączenia urządzenia_ , które zostało wcześniej wykonane.
    - Dodaj wartość `symmetric_key` , która jest wartością klucza dostępu współdzielonego z _parametrów połączenia urządzenia_.
    - Zastąp `root_interface_model_id` wartość wartością `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    Pierwsza sekcja *config.jsw* pliku wygląda teraz następująco:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Uruchom plik wykonywalny w środowisku wiersza polecenia. Mostek generuje dane wyjściowe, które wyglądają następująco:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Kompilowanie i uruchamianie mostka

Jeśli wolisz skompilować plik wykonywalny samodzielnie, możesz pobrać kod źródłowy i skrypty kompilacji.

Otwórz wiersz polecenia w wybranym folderze. Uruchom następujące polecenie, aby sklonować repozytorium usługi [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) do tej lokalizacji:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Po sklonowaniu repozytorium należy zaktualizować moduły podrzędne. Moduły podrzędne zawierają zestaw SDK usługi Azure IoT dla języka C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Oczekiwanie na ukończenie tej operacji.

> [!TIP]
> Jeśli napotkasz problemy z niepowodzeniem aktualizacji modułu podrzędnego klonowania git, jest to znany problem z ścieżkami plików systemu Windows. Aby rozwiązać ten problem, można wypróbować następujące polecenie: `git config --system core.longpaths true`

Wymagania wstępne dotyczące tworzenia mostka różnią się w zależności od systemu operacyjnego:

### <a name="windows"></a>Windows

Aby skompilować mostek Plug and Play IoT w systemie Windows, Zainstaluj następujące oprogramowanie:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas [instalowania](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) programu Visual Studio dołączysz **programowanie klasyczne przy użyciu języka C++** .
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="linux"></a>Linux

W tym artykule przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym artykule zostały przetestowane przy użyciu Ubuntu 18,04.

Aby skompilować mostek Plug and Play IoT w systemie Linux, zainstaluj oprogramowanie do serwisu w **zatoce**, **git**, **CMAKE** i wszystkie wymagane zależności przy użyciu `apt-get` polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy wersja programu `cmake` jest powyżej **2.8.12** , a wersja **tej** wersji jest większa niż **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Tworzenie mostka Plug and Play IoT

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

### <a name="edit-the-configuration-file"></a>Edytuj plik konfiguracji

Więcej informacji na temat plików konfiguracji można znaleźć w [dokumencie koncepcje dotyczące programu IoT Plug and Play Bridge](concepts-iot-pnp-bridge.md).

Otwórz *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.jsw* pliku w edytorze tekstu.

- Dodaj `connection-string` wartość będącą _ciągiem połączenia urządzenia_ , które zostało wcześniej wykonane.
- Dodaj wartość `symmetric_key` , która jest wartością klucza dostępu współdzielonego z _parametrów połączenia urządzenia_.
- Zastąp `root_interface_model_id` wartość wartością `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

Pierwsza sekcja *config.jsw* pliku wygląda teraz następująco:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Uruchamianie mostka Plug and Play IoT

Uruchom przykład czujnika środowiska mostka Plug and Play IoT. Parametr jest ścieżką do `config.json` pliku edytowanego w poprzedniej sekcji:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Mostek generuje dane wyjściowe, które wyglądają następująco:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Użyj następujących poleceń, aby uruchomić mostek w systemie Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Pobierz pliki modelu

Użyj programu Azure IoT Explorer później, aby wyświetlić urządzenie podczas łączenia się z Centrum IoT. W programie Azure IoT Explorer wymagana jest lokalna kopia pliku modelu zgodna z **identyfikatorem modelu** wysyłanym przez urządzenie. Plik modelu umożliwia Eksplorator IoT wyświetlanie danych telemetrycznych, właściwości i poleceń wdrażanych przez urządzenie.

Aby pobrać modele dla programu Azure IoT Explorer:

1. Utwórz folder o nazwie *modele* na komputerze lokalnym.
1. Zapisz [EnvironmentalSensor.jsw](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) folderze *modele* utworzonym w poprzednim kroku.
1. Jeśli otworzysz ten plik modelu w edytorze tekstu, można zobaczyć, że model definiuje składnik `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` jako jego identyfikator. Jest to ten sam Identyfikator modelu, który został użyty w *config.js* pliku.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po uruchomieniu mostka Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa. Można wyświetlić dane telemetryczne, właściwości i polecenia zdefiniowane w `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modelu.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

* [Co to jest IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Tworzenie, wdrażanie i poszerzanie mostka Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md)
* [Mostek Plug and Play IoT w serwisie GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
