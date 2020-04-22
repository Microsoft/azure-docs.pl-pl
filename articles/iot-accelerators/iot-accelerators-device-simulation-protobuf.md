---
title: Używanie buforów protokołów z symulacją urządzenia — Azure| Dokumenty firmy Microsoft
description: W tym przewodniku instrukcja, dowiedzieć się, jak za pomocą buforów protokołu do serializacji danych telemetrycznych wysyłanych z akceleratora rozwiązania symulacji urządzenia.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom:
- mvc
- amqp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c49745b30d2c4acc115a72af095f3e941dc4d509
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684005"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializacja danych telemetrycznych przy użyciu buforów protokołu

Bufory protokołów (Protobuf) to binarny format serializacji dla danych strukturalnych. Protobuf został zaprojektowany, aby podkreślić prostotę i wydajność, aby być mniejszym i szybszym niż XML.

Device Simulation obsługuje wersję **proto3** języka buforów protokołu.

Ponieważ Protobuf wymaga skompilowanego kodu do serializacji danych, należy utworzyć niestandardową wersję symulacji urządzenia.

Kroki opisane w tym przewodniku pokazują, jak:

1. Przygotowanie środowiska programistycznego
1. Określanie przy użyciu formatu Protobuf w modelu urządzenia
1. Zdefiniuj format Protobuf
1. Generowanie klas Protobuf
1. Testowanie lokalnie

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz:

* Program Visual Studio Code. Program Visual Studio Code można pobrać [dla komputerów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Program .NET Core można pobrać [dla komputerów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* Listonosz. Listonosza można pobrać [dla komputerów Mac, windows lub Linux](https://www.getpostman.com/apps).
* [Centrum IoT wdrożone w ramach subskrypcji platformy Azure.](../iot-hub/iot-hub-create-through-portal.md) Aby wykonać kroki opisane w tym przewodniku, potrzebujesz ciągu połączenia centrum IoT. Ciąg połączenia można uzyskać z witryny Azure portal.
* [Baza danych usługi Cosmos DB wdrożona w ramach subskrypcji platformy Azure,](../cosmos-db/create-sql-api-dotnet.md#create-account) która korzysta z interfejsu API SQL i jest skonfigurowana pod kątem [silnej spójności.](../cosmos-db/manage-account.md) Aby wykonać kroki opisane w tym przewodniku, potrzebny jest parametry połączenia bazy danych usługi Cosmos DB. Ciąg połączenia można uzyskać z witryny Azure portal.
* [Konto magazynu platformy Azure wdrożone w ramach subskrypcji platformy Azure](../storage/common/storage-account-create.md). Aby wykonać kroki opisane w tym przewodniku, potrzebny jest parametry połączenia konta magazynu. Ciąg połączenia można uzyskać z witryny Azure portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania, aby przygotować środowisko programistyczne:

* Pobierz źródło mikrousług symulacji urządzenia.
* Pobierz źródło mikrousługi karty magazynu.
* Uruchom mikrousługę karty magazynu lokalnie.

W instrukcjach w tym artykule założono, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek plików i poleceń do środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousługi

Pobierz i rozpaj [mikrousług zdalnego monitorowania](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z gitHub do odpowiedniej lokalizacji na komputerze lokalnym. To repozytorium zawiera mikrousługi karty magazynu, które są potrzebne do tego sposobu.

Pobierz i rozpaj [mikrousługę symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z usługi GitHub do odpowiedniej lokalizacji na komputerze lokalnym.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousług karty pamięci masowej

W programie Visual Studio Code otwórz folder **remote-monitoring-services-dotnet-master\storage-adapter.** Kliknij dowolne przyciski **Przywracanie,** aby naprawić nierozwiązane zależności.

Otwórz plik **vscode/launch.json** i przypisz parametry połączenia usługi Cosmos DB do zmiennej środowiskowej **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING.**

> [!NOTE]
> Po uruchomieniu mikrousługi lokalnie na komputerze, nadal wymaga wystąpienia usługi Cosmos DB na platformie Azure do poprawnego działania.

Aby lokalnie uruchomić mikrousługę karty magazynu, kliknij przycisk **Debugowanie \> rozpocznij debugowanie**.

Okno **Terminal** w programie Visual Studio Code pokazuje dane wyjściowe z uruchomionej mikrousługi, w tym adres URL sprawdzania kondycji usługi sieci web: <http://127.0.0.1:9022/v1/status>. Po przejściu do tego adresu, stan powinien być "OK: Żyje i dobrze".

Pozostaw mikrousługę karty magazynu uruchomione w tym wystąpieniu programu Visual Studio Code podczas wykonywania następujących kroków.

## <a name="define-your-device-model"></a>Definiowanie modelu urządzenia

Otwórz folder **device-simulation-dotnet-master** pobrany z usługi GitHub w nowym wystąpieniu programu Visual Studio Code. Kliknij dowolne przyciski **Przywracanie,** aby naprawić wszelkie nierozwiązane zależności.

W tym przewodniku można utworzyć nowy model urządzenia dla modułu śledzenia zasobów:

1. Utwórz nowy plik modelu urządzenia o nazwie **assettracker-01.json** w folderze **Usługi\dane\devicemodels.**

1. Zdefiniuj funkcjonalność urządzenia w pliku **assettracker-01.json** modelu urządzenia. Sekcja telemetrii modelu urządzenia Protobuf musi:

   * Podaj nazwę klasy Protobuf, którą generujesz dla swojego urządzenia. W poniższej sekcji pokazano, jak wygenerować tę klasę.
   * Określ Protobuf jako format wiadomości.

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

### <a name="create-device-behaviors-script"></a>Tworzenie skryptu zachowań urządzeń

Napisz skrypt zachowania, który definiuje zachowanie urządzenia. Aby uzyskać więcej informacji, zobacz [Tworzenie zaawansowanego symulowanego urządzenia](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Zdefiniuj format Protobuf

Jeśli masz model urządzenia i określisz format wiadomości, możesz utworzyć plik **proto.** W pliku **proto** dodajesz:

* A, `csharp_namespace` który pasuje **do ClassName** właściwości w modelu urządzenia.
* Komunikat dla każdej struktury danych do serializacji.
* Nazwa i typ dla każdego pola w wiadomości.

1. Utwórz nowy plik o nazwie **assettracker.proto** w folderze **Usługi\Modele\Protobuf\proto.**

1. Zdefiniuj składnię, obszar nazw i schemat wiadomości w pliku **proto** w następujący sposób:

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

`=2` Znaczniki `=1`na każdym elemencie określają unikatowy znacznik używany przez pole w kodowaniu binarnym. Liczby 1-15 wymagają jednego bajtu mniej do zakodowania niż wyższe liczby.

## <a name="generate-the-protobuf-class"></a>Generowanie klasy Protobuf

gdy masz plik **proto,** następnym krokiem jest wygenerowanie klas potrzebnych do odczytu i zapisu wiadomości. Aby wykonać ten krok, potrzebujesz kompilatora **Protoc** Protobuf.

1. [Pobierz kompilator Protobuf z GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Uruchom kompilator, określając katalog źródłowy, katalog docelowy i nazwę pliku **proto.** Przykład:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    To polecenie generuje plik **Assettracker.cs** w folderze **Usługi\Modele\Protobuf.**

## <a name="test-protobuf-locally"></a>Test Protobuf lokalnie

W tej sekcji testujesz urządzenie do śledzenia zasobów utworzone w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousług symulacji urządzenia

Otwórz plik **vscode/launch.json** i przypisz:

* Ciąg połączenia IoT Hub ze zmienną środowiskową **PCS\_\_IOTHUB CONNSTRING.**
* Parametry połączenia konta magazynu ze zmienną środowiska **PCS\_AZURE\_STORAGE\_ACCOUNT.**
* Parametry połączenia usługi Cosmos DB ze zmienną środowiska **PCS\_\_STORAGEADAPTER DOCUMENTDB\_CONNSTRING.**

Otwórz plik **WebService/Properties/launchSettings.json** i przypisz:

* Ciąg połączenia IoT Hub ze zmienną środowiskową **PCS\_\_IOTHUB CONNSTRING.**
* Parametry połączenia konta magazynu ze zmienną środowiska **PCS\_AZURE\_STORAGE\_ACCOUNT.**
* Parametry połączenia usługi Cosmos DB ze zmienną środowiska **PCS\_\_STORAGEADAPTER DOCUMENTDB\_CONNSTRING.**

Otwórz plik **WebService\appsettings.ini** i zmodyfikuj ustawienia w następujący sposób:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Skonfiguruj rozwiązanie tak, aby zawierało nowe pliki modelu urządzenia

Domyślnie pliki JSON i JS nowego modelu urządzenia nie zostaną skopiowane do wbudowanego rozwiązania. Musisz je jawnie uwzględnić.

Dodaj wpis do pliku **services\services.csproj** dla każdego pliku, który chcesz uwzględnić. Przykład:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Aby uruchomić mikrousługę lokalnie, kliknij przycisk **Debugowanie \> start debugowania**.

Okno **Terminal** w programie Visual Studio Code pokazuje dane wyjściowe z uruchomionej mikrousługi.

Pozostaw mikrousługi symulacji urządzenia uruchomione w tym wystąpieniu programu Visual Studio Code podczas wykonywania kolejnych kroków.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora zdarzeń urządzenia

W tej sekcji można użyć interfejsu wiersza polecenia platformy Azure, aby skonfigurować monitor zdarzeń, aby wyświetlić dane telemetryczne wysyłane z urządzeń podłączonych do centrum IoT hub.

Poniższy skrypt zakłada, że nazwa centrum IoT hub jest **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw monitor zdarzeń uruchomiony podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Tworzenie symulacji z typem urządzenia do śledzenia zasobów

W tej sekcji użyj narzędzia Postman, aby zażądać mikrousług symulacji urządzenia do uruchomienia symulacji przy użyciu typu urządzenia do śledzenia zasobów. Listonosz to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci web.

Aby skonfigurować listonosza:

1. Otwórz listonosza na lokalnym komputerze.

1. Kliknij ** \> pozycję Import pliku**. Następnie kliknij pozycję **Wybierz pliki**.

1. Wybierz **środowisko akceleratora rozwiązań\_Azure IoT Device Simulation.postman** i **środowisko akceleratora rozwiązań Azure IoT Device Simulation.postman\_** i kliknij przycisk **Otwórz**.

1. Rozwiń **akcelerator rozwiązań symulacji urządzeń usługi Azure IoT,** aby wyświetlić żądania, które można wysłać.

1. Kliknij **pozycję Brak środowiska** i wybierz **akcelerator rozwiązań symulacji urządzeń Usługi Azure IoT**.

Masz teraz kolekcji i środowiska załadowany w obszarze roboczym Postman, który służy do interakcji z mikrousługi symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Listonosz wybierz pozycję **Utwórz symulację śledzenia zasobów** i kliknij przycisk **Wyślij**. To żądanie tworzy cztery wystąpienia typu urządzenia monitorującego symulowane zasoby.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazują dane telemetryczne z symulowanych urządzeń.

Aby zatrzymać symulację, wybierz żądanie **Zatrzymaj symulację** w postmanie i kliknij przycisk **Wyślij**.

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można zatrzymać dwa lokalnie uruchomione mikrousług w ich wystąpień kodu programu Visual Studio (**Debugowanie zatrzymania \> debugowania**).

Jeśli nie potrzebujesz już wystąpień usługi IoT Hub i usługi Cosmos DB, usuń je z subskrypcji platformy Azure, aby uniknąć niepotrzebnych opłat.

## <a name="iot-hub-support"></a>Obsługa usługi IoT Hub

Wiele funkcji Usługi IoT Hub nie obsługuje natywnie protobuf lub innych formatów binarnych. Na przykład nie można trasy na podstawie ładunku wiadomości, ponieważ Usługa IoT Hub nie będzie w stanie przetworzyć ładunku wiadomości. Można jednak routić na podstawie nagłówków wiadomości.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak dostosować symulację urządzenia do wysyłania danych telemetrycznych za pomocą Protobuf, następnym krokiem jest nauczenie się teraz [wdrażać niestandardowy obraz w chmurze.](iot-accelerators-device-simulation-deploy-image.md)
