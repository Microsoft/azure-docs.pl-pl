---
title: Używanie buforów protokołu z symulacją urządzenia — Azure| Microsoft Docs
description: Z tego przewodnika dowiesz się, jak za pomocą buforów protokołu serializować dane telemetryczne wysyłane z akceleratora rozwiązania do symulacji urządzeń.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 2d2c33d0b6f86bc1a779361b86d242cde4c5df38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873671"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializowanie telemetrii przy użyciu buforów protokołu

Protocol Buffers (Protobuf) to binarny format serializacji danych strukturalnych. Protobuf zaprojektowano w celu kładenia nacisku na prostotę i wydajność, dzięki temu, że jest mniejszy i szybszy niż xml.

Symulacja urządzenia obsługuje **wersję proto3** języka buforów protokołu.

Ponieważ protobuf wymaga skompilowanego kodu do serializacji danych, należy utworzyć niestandardową wersję symulacji urządzenia.

Kroki opisane w tym przewodniku pokazują, jak:

1. Przygotowywanie środowiska projektowego
1. Określanie przy użyciu formatu Protobuf w modelu urządzenia
1. Definiowanie formatu Protobuf
1. Generowanie klas Protobuf
1. Testowanie lokalne

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebne są następujące informacje:

* Program Visual Studio Code. Możesz pobrać aplikację [dla Visual Studio Code Mac, Linux i Windows.](https://code.visualstudio.com/download)
* .NET Core. Program .NET Core dla [komputerów Mac, Linux i Windows można pobrać.](https://dotnet.microsoft.com/download)
* Listonosz. Możesz pobrać program [Postman dla komputerów Mac, systemów Windows lub Linux.](https://www.getpostman.com/apps)
* Centrum [IoT wdrożone w subskrypcji platformy Azure.](../iot-hub/iot-hub-create-through-portal.md) Aby wykonać kroki opisane w tym przewodniku, potrzebne są ciągi połączenia centrum IoT. Możesz pobrać parametrów połączenia z Azure Portal.
* Baza [Cosmos DB wdrożona w](../cosmos-db/create-sql-api-dotnet.md#create-account) subskrypcji platformy Azure, która korzysta z interfejsu API SQL i jest skonfigurowana w celu zapewnienia [silnej spójności.](../cosmos-db/how-to-manage-database-account.md) Do wykonania kroków Cosmos DB w tym przewodniku potrzebne są następujące wartości parametrów połączenia bazy danych. Możesz pobrać parametrów połączenia z Azure Portal.
* Konto [usługi Azure Storage wdrożone w subskrypcji platformy Azure.](../storage/common/storage-account-create.md) Aby wykonać kroki opisane w tym przewodniku, potrzebne są ciągi połączenia konta magazynu. Możesz pobrać parametrów połączenia z Azure Portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania, aby przygotować środowisko projektowe:

* Pobierz źródło mikrousługi symulacji urządzenia.
* Pobierz źródło mikrousługi adaptera magazynu.
* Uruchom lokalnie mikrousługę adaptera magazynu.

W instrukcjach w tym artykule założono, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek plików i poleceń do własnego środowiska.

### <a name="download-the-microservices"></a>Pobieranie mikrousług

Pobierz i rozpakować [mikrousługi](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) zdalnego monitorowania z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym. To repozytorium zawiera mikrousługę adaptera magazynu, która jest potrzebna do tego celu.

Pobierz i rozpakować [mikrousługę symulacji](https://github.com/Azure/azure-iot-pcs-device-simulation/archive/master.zip) urządzenia z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousługi adaptera magazynu

W Visual Studio Code otwórz folder **remote-monitoring-services-dotnet-master\storage-adapter.** Kliknij dowolne **przyciski Przywróć,** aby naprawić nierozwiązane zależności.

Otwórz plik **.vscode/launch.jsi** przypisz swoje Cosmos DB do zmiennej środowiskowej **\_ PCS STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

> [!NOTE]
> Po uruchomieniu mikrousługi lokalnie na maszynie nadal wymagane jest wystąpienie Cosmos DB na platformie Azure, aby działało prawidłowo.

Aby uruchomić mikrousługę adaptera magazynu lokalnie, kliknij pozycję **\> Debuguj rozpocznij debugowanie.**

W **oknie** terminalu Visual Studio Code dane wyjściowe uruchomionej mikrousługi, w tym adres URL kontroli kondycji usługi internetowej: <http://127.0.0.1:9022/v1/status> . Po nawigowanie po tym adresie powinien mieć stan "OK: Aktywne i dobrze".

Pozostaw mikrousługę karty magazynu uruchomionej w tym wystąpieniu Visual Studio Code podczas wykonania poniższych kroków.

## <a name="define-your-device-model"></a>Definiowanie modelu urządzenia

Otwórz **folder device-simulation-dotnet-master** pobrany z usługi GitHub w nowym wystąpieniu Visual Studio Code. Kliknij dowolne **przyciski Przywróć,** aby naprawić wszystkie nierozwiązane zależności.

W tym przewodniku utworzysz nowy model urządzenia dla modułu śledzącego elementy zawartości:

1. Utwórz nowy plik modelu urządzenia o **nazwieassettracker-01.jsw** **folderze Services\data\devicemodels.**

1. Zdefiniuj funkcje urządzenia w modelu **assettracker-01.jsw pliku.** Sekcja telemetrii modelu urządzenia Protobuf musi:

   * Dołącz nazwę klasy Protobuf wygenerowaną dla urządzenia. W poniższej sekcji pokazano, jak wygenerować tę klasę.
   * Określ format komunikatu Protobuf.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Tworzenie skryptu zachowania urządzenia

Napisz skrypt zachowania, który definiuje sposób działania urządzenia. Aby uzyskać więcej informacji, zobacz [Tworzenie zaawansowanego symulowanego urządzenia](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiowanie formatu Protobuf

Jeśli masz model urządzenia i ustalisz format wiadomości, możesz utworzyć **plik proto.** W **pliku proto** dodaj:

* Obiekt `csharp_namespace` , który odpowiada właściwości **ClassName** w modelu urządzenia.
* Komunikat dla każdej struktury danych do serializacji.
* Nazwa i typ dla każdego pola w komunikacie.

1. Utwórz nowy plik o nazwie **assettracker.proto** w **folderze Services\Models\Protobuf\proto.**

1. Zdefiniuj składnię, przestrzeń nazw i schemat komunikatów w **pliku proto** w następujący sposób:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Znaczniki , na każdym elemencie określają unikatowy `=1` tag używany przez pole w `=2` kodowaniu binarnym. Cyfry 1–15 wymagają o jeden bajt mniej do zakodowania niż wyższe liczby.

## <a name="generate-the-protobuf-class"></a>Generowanie klasy Protobuf

Jeśli masz plik **proto,** następnym krokiem jest wygenerowanie klas potrzebnych do odczytywania i zapisu komunikatów. Do wykonania tego kroku potrzebny jest kompilator **Protoc** Protobuf.

1. [Pobieranie kompilatora Protobuf z usługi GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Uruchom kompilator, określając katalog źródłowy, katalog docelowy i nazwę pliku **proto.** Na przykład:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    To polecenie generuje plik **Assettracker.cs** w **folderze Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Testowanie protobuf lokalnie

W tej sekcji przetestujemy lokalnie urządzenie do śledzenia zasobów utworzone w poprzednich sekcjach.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousługi symulacji urządzenia

Otwórz plik **.vscode/launch.jsw pliku** i przypisz:

* IoT Hub parametrów połączenia ze zmienną środowiskową **PCS \_ IOTHUB \_ CONNSTRING.**
* Parametrów połączenia konta magazynu ze zmienną **środowiskową KONTO \_ USŁUGI AZURE \_ STORAGE \_ PCS.**
* Cosmos DB parametrów połączenia ze zmienną środowiskową **\_ PCS STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

Otwórz plik **WebService/Properties/launchSettings.jsi** przypisz:

* IoT Hub parametrów połączenia ze zmienną środowiskową **PCS \_ IOTHUB \_ CONNSTRING.**
* Parametrów połączenia konta magazynu ze zmienną **środowiskową KONTO \_ USŁUGI AZURE \_ STORAGE \_ PCS.**
* Cosmos DB parametrów połączenia ze zmienną środowiskową **\_ PCS STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

Otwórz **plikWebService\appsettings.ini** i zmodyfikuj ustawienia w następujący sposób:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurowanie rozwiązania w celu dołączania nowych plików modelu urządzenia

Domyślnie nowe pliki JSON i JS modelu urządzenia nie będą kopiowane do rozwiązania wbudowanego. Należy je jawnie dołączyć.

Dodaj wpis do pliku **services\services.csproj** dla każdego pliku, który ma zostać dołączony. Na przykład:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Aby uruchomić mikrousługę lokalnie, kliknij pozycję **\> Debuguj rozpocznij debugowanie.**

W **oknie terminalu** Visual Studio Code dane wyjściowe uruchomionej mikrousługi.

Pozostaw mikrousługę symulacji urządzenia uruchamianą w tym wystąpieniu Visual Studio Code podczas następnych kroków.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora zdarzeń urządzenia

W tej sekcji skonfigurujemy monitor zdarzeń przy użyciu interfejsu wiersza polecenia platformy Azure, aby wyświetlić dane telemetryczne wysyłane z urządzeń połączonych z centrum IoT.

W poniższym skrypcie przyjęto założenie, że nazwa centrum IoT to **device-simulation-test.**

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw monitor zdarzeń uruchomiony podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Tworzenie symulacji z typem urządzenia do śledzenia zasobów

W tej sekcji użyjemy narzędzia Postman, aby zażądać mikrousługi symulacji urządzenia w celu uruchomienia symulacji przy użyciu typu urządzenia śledzenie zasobów. Postman to narzędzie, które umożliwia wysyłanie żądań REST do usługi internetowej.

Aby skonfigurować postman:

1. Otwórz program Postman na komputerze lokalnym.

1. Kliknij **pozycję \> Importuj plik.** Następnie kliknij **pozycję Choose Files (Wybierz pliki).**

1. Wybierz **kolekcję azure IoT Device Simulation solution accelerator.postman \_ i** środowisko akceleratora rozwiązania do symulacji urządzeń usługi **Azure IoT.postman, \_** a następnie kliknij **przycisk Otwórz**.

1. Rozwiń akcelerator rozwiązania do symulacji urządzeń usługi **Azure IoT,** aby wyświetlić żądania, które możesz wysłać.

1. Kliknij **pozycję Brak środowiska** i wybierz akcelerator rozwiązania do symulacji urządzenia usługi Azure **IoT.**

Masz teraz załadowaną kolekcję i środowisko w obszarze roboczym narzędzia Postman, których możesz użyć do interakcji z mikrousługą symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Postman wybierz pozycję Utwórz **symulację monitora zasobów i** kliknij pozycję **Wyślij**. To żądanie tworzy cztery wystąpienia urządzenia typu symulowanego modułu śledzącego elementy zawartości.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazują dane telemetryczne z symulowanych urządzeń.

Aby zatrzymać symulację, wybierz żądanie **Zatrzymaj symulację** w programie Postman i kliknij pozycję **Wyślij**.

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zatrzymać dwie lokalnie uruchomione mikrousługi w ich wystąpieniach Visual Studio Code (**Debugowanie \> zatrzymania debugowania).**

Jeśli nie potrzebujesz już wystąpień IoT Hub i Cosmos DB, usuń je z subskrypcji platformy Azure, aby uniknąć niepotrzebnych opłat.

## <a name="iot-hub-support"></a>IoT Hub pomocy technicznej

Wiele IoT Hub nie obsługuje natywnie formatu Protobuf ani innych formatów binarnych. Na przykład nie można przekierowyć na podstawie ładunku komunikatu, ponieważ IoT Hub nie będzie można przetworzyć ładunku komunikatu. Można jednak trasę na podstawie nagłówków komunikatów.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak dostosować symulację urządzenia w celu wysyłania danych telemetrycznych za pomocą narzędzia Protobuf, następnym krokiem jest odwiedź repozytorium GitHub, aby dowiedzieć się więcej o symulacji [urządzenia.](https://github.com/Azure/azure-iot-pcs-device-simulation)
