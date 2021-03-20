---
title: Używanie buforów protokołu z symulacją urządzenia — Azure | Microsoft Docs
description: W tym przewodniku krok po kroku dowiesz się, jak za pomocą buforów protokołu serializować dane telemetryczne wysyłane z akceleratora rozwiązania do symulacji urządzeń.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852154"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializowanie telemetrii przy użyciu buforów protokołu

Bufory protokołu (protobuf) to binarny format serializacji dla danych strukturalnych. Protobuf zaprojektowano tak, aby podkreślić prostotę i wydajność w celu ich zmniejszenia i przyspieszenia od formatu XML.

Symulacja urządzenia obsługuje wersję **proto3** języka protokołu bufors.

Ponieważ protobuf wymaga skompilowanego kodu do serializacji danych, należy utworzyć niestandardową wersję symulacji urządzenia.

Kroki opisane w tym przewodniku przedstawiają, jak:

1. Przygotuj środowisko programistyczne
1. Określanie przy użyciu formatu protobuf w modelu urządzenia
1. Definiowanie formatu protobuf
1. Generuj klasy protobuf
1. Testuj lokalnie

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku:

* Program Visual Studio Code. Możesz pobrać [Visual Studio Code dla systemów Mac, Linux i Windows](https://code.visualstudio.com/download).
* .NET Core. Możesz pobrać [.NET Core dla systemów Mac, Linux i Windows](https://www.microsoft.com/net/download).
* Postman. Możesz pobrać program [Poster dla komputerów Mac, Windows lub Linux](https://www.getpostman.com/apps).
* [Centrum IoT zostało wdrożone w ramach subskrypcji platformy Azure](../iot-hub/iot-hub-create-through-portal.md). Do wykonania kroków opisanych w tym przewodniku potrzebne są parametry połączenia Centrum IoT Hub. Parametry połączenia można uzyskać z Azure Portal.
* [Baza danych Cosmos DB wdrożona w ramach subskrypcji platformy Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) korzystającej z interfejsu API SQL i skonfigurowana pod kątem [silnej spójności](../cosmos-db/how-to-manage-database-account.md). Do wykonania kroków opisanych w tym przewodniku potrzebne są parametry połączenia bazy danych Cosmos DB. Parametry połączenia można uzyskać z Azure Portal.
* [Konto usługi Azure Storage wdrożone w ramach subskrypcji platformy Azure](../storage/common/storage-account-create.md). Do wykonania kroków opisanych w tym przewodniku potrzebne są parametry połączenia konta magazynu. Parametry połączenia można uzyskać z Azure Portal.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania, aby przygotować środowisko programistyczne:

* Pobierz Źródło dla mikrousługi symulacji urządzenia.
* Pobierz Źródło dla mikrousługi karty pamięci.
* Uruchom lokalnie kartę usługi Storage.

W instrukcjach przedstawionych w tym artykule przyjęto założenie, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek i poleceń plików do środowiska.

### <a name="download-the-microservices"></a>Pobierz mikrousługi

Pobierz i rozpakuj [mikrousługi zdalnego monitorowania](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z witryny GitHub do odpowiedniej lokalizacji na komputerze lokalnym. To repozytorium obejmuje mikrousługę karty pamięci, która jest potrzebna do wykonania tej procedury.

Pobierz i rozpakuj [mikrousługę symulacji urządzenia z usługi](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) GitHub do odpowiedniej lokalizacji na komputerze lokalnym.

### <a name="run-the-storage-adapter-microservice"></a>Uruchamianie mikrousługi karty pamięci

W Visual Studio Code Otwórz folder **Remote-Monitoring-Services-dotnet-master\storage-adapter** . Kliknij dowolne przyciski **przywracania** , aby naprawić nierozwiązane zależności.

Otwórz plik **. programu vscode/launch.jsw** pliku i przypisz parametry połączenia Cosmos DB do zmiennej środowiskowej **\_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

> [!NOTE]
> Po uruchomieniu mikrousługi lokalnie na maszynie nadal wymagane jest wystąpienie Cosmos DB na platformie Azure.

Aby uruchomić mikrousługę dla karty magazynu lokalnie, kliknij pozycję **Debuguj \> Rozpocznij debugowanie**.

W oknie **terminalu** w Visual Studio Code są wyświetlane dane wyjściowe z uruchomionej mikrousługi, w tym adres URL sprawdzania kondycji usługi sieci Web: <http://127.0.0.1:9022/v1/status> . Po przejściu na ten adres stan powinien mieć wartość "OK: Alive i".

Podczas wykonywania poniższych kroków należy pozostawić działającą w tym wystąpieniu mikrousługę kart pamięci Visual Studio Code.

## <a name="define-your-device-model"></a>Definiowanie modelu urządzenia

Otwórz folder **"symulacja urządzenia-dotnet"** , który został pobrany z usługi GitHub w nowym wystąpieniu Visual Studio Code. Kliknij dowolne przyciski **przywracania** , aby naprawić wszystkie nierozwiązane zależności.

W tym przewodniku krok po kroku utworzysz nowy model urządzenia dla narzędzia do śledzenia zasobów:

1. Utwórz nowy plik modelu urządzenia o nazwie **assettracker-01.js** w folderze **Services\data\devicemodels** .

1. Zdefiniuj funkcje urządzenia w modelu urządzenia **assettracker-01.jsw** pliku. Sekcja telemetrii modelu urządzenia protobuf musi:

   * Dołącz nazwę klasy protobuf, która została wygenerowana dla urządzenia. W poniższej sekcji pokazano, jak wygenerować tę klasę.
   * Określ protobuf jako format wiadomości.

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

### <a name="create-device-behaviors-script"></a>Skrypt tworzenia zachowań dotyczących urządzeń

Napisz skrypt zachowania, który definiuje sposób działania urządzenia. Aby uzyskać więcej informacji, zobacz [Tworzenie zaawansowanego urządzenia symulowanego](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiowanie formatu protobuf

Jeśli masz model urządzenia i określisz Format wiadomości, możesz utworzyć plik **proto** . W pliku **proto** dodaje się:

* Obiekt `csharp_namespace` , który jest zgodny z właściwością **ClassName** w modelu urządzenia.
* Komunikat dla każdej struktury danych do serializacji.
* Nazwa i typ dla każdego pola w komunikacie.

1. Utwórz nowy plik o nazwie **assettracker. proto** w folderze **Services\Models\Protobuf\proto**

1. Zdefiniuj składnię, przestrzeń nazw i schemat komunikatu w pliku **proto** w następujący sposób:

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

`=1`Znaczniki, `=2` dla każdego elementu określają unikatowy tag, którego pole używa w kodowaniu binarnym. Liczby 1-15 wymagają jednego mniejszego bajtu do kodowania niż większe liczby.

## <a name="generate-the-protobuf-class"></a>Generuj klasę protobuf

Jeśli masz plik **proto** , następnym krokiem jest wygenerowanie klas wymaganych do odczytu i zapisu komunikatów. Do wykonania tego kroku jest potrzebny kompilator **Protoc** protobuf.

1. [Pobierz kompilator protobuf z usługi GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Uruchom kompilator, określając Katalog źródłowy, katalog docelowy i nazwę pliku **proto** . Na przykład:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    To polecenie generuje plik **Assettracker. cs** w folderze **Services\Models\Protobuf** .

## <a name="test-protobuf-locally"></a>Testowanie protobuf lokalnie

W tej sekcji przetestujesz urządzenie do śledzenia zasobów utworzone w poprzednich sekcjach lokalnie.

### <a name="run-the-device-simulation-microservice"></a>Uruchamianie mikrousługi symulacji urządzenia

Otwórz plik **. programu vscode/launch.jsw** pliku i przypisz:

* IoT Hub parametry połączenia z zmienną środowiskową **\_ IOTHUB \_ CONNSTRING** .
* Parametry połączenia konta magazynu z zmienną środowiskową **\_ \_ \_ konta usługi Azure Storage** .
* Cosmos DB parametry połączenia z zmienną środowiskową **\_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

Otwórz plik **WebService/Properties/launchSettings.jsw** pliku i przypisz:

* IoT Hub parametry połączenia z zmienną środowiskową **\_ IOTHUB \_ CONNSTRING** .
* Parametry połączenia konta magazynu z zmienną środowiskową **\_ \_ \_ konta usługi Azure Storage** .
* Cosmos DB parametry połączenia z zmienną środowiskową **\_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

Otwórz plik **WebService\appsettings.ini** i zmodyfikuj ustawienia w następujący sposób:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Skonfiguruj rozwiązanie w celu uwzględnienia nowych plików modelu urządzenia

Domyślnie nowe pliki JSON i JS modelu urządzenia nie zostaną skopiowane do skompilowanego rozwiązania. Należy je jawnie uwzględnić.

Dodaj wpis do pliku **services\services.csproj** dla każdego pliku, który ma zostać uwzględniony. Na przykład:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Aby uruchomić mikrousługę lokalnie, kliknij pozycję **Debuguj \> Rozpocznij debugowanie**.

W oknie **terminalu** w Visual Studio Code są wyświetlane dane wyjściowe z uruchomionej mikrousługi.

W tym wystąpieniu Visual Studio Code należy pozostawić uruchomioną mikrousługę symulacji urządzenia podczas wykonywania następnych kroków.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurowanie monitora dla zdarzeń urządzeń

W tej sekcji użyto interfejsu wiersza polecenia platformy Azure, aby skonfigurować Monitor zdarzeń do wyświetlania danych telemetrycznych wysyłanych z urządzeń podłączonych do centrum IoT.

W poniższym skrypcie założono, że nazwa Twojego centrum IoT to **symulacja urządzenia**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Pozostaw uruchomiony Monitor zdarzeń podczas testowania symulowanych urządzeń.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Tworzenie symulacji przy użyciu typu urządzenia do śledzenia zasobów

W tej sekcji użyjemy narzędzia do ogłaszania, aby zażądać mikrousługi symulacji urządzenia do uruchomienia symulacji przy użyciu typu urządzenia do śledzenia zasobów. Program Poster to narzędzie, które umożliwia wysyłanie żądań REST do usługi sieci Web.

Aby skonfigurować notkę:

1. Otwórz wpis na komputerze lokalnym.

1. Kliknij **pozycję \> Importuj plik**. Następnie kliknij pozycję **Wybierz pliki**.

1. Wybierz **Akcelerator rozwiązania do symulacji urządzeń Azure IoT. \_ Kolekcja ogłoszeń** i narzędzie **Azure IoT Device symulacja rozwiązania. \_** 

1. Rozwiń **Akcelerator rozwiązania do symulacji urządzeń Azure IoT** , aby wyświetlić możliwe do wysłania żądania.

1. Kliknij pozycję **Brak środowiska** i wybierz **Akcelerator rozwiązania do symulacji urządzeń Azure IoT**.

Masz teraz kolekcję i środowisko załadowane w obszarze roboczym programu Poster, którego można użyć do współdziałania z mikrousługą symulacji urządzenia.

Aby skonfigurować i uruchomić symulację:

1. W kolekcji Poster wybierz pozycję **Utwórz symulację śledzenia zasobów** i kliknij przycisk **Wyślij**. To żądanie tworzy cztery wystąpienia symulowanego typu urządzenia w monitorze zasobów.

1. Dane wyjściowe monitora zdarzeń w oknie interfejsu wiersza polecenia platformy Azure pokazują dane telemetryczne z symulowanych urządzeń.

Aby zatrzymać symulację, wybierz żądanie **zatrzymania symulacji** w programie Poster i kliknij pozycję **Wyślij**.

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Można zatrzymać dwa lokalnie działające mikrousługi w Visual Studio Code wystąpieniach (debugowanie **\> Zatrzymaj debugowanie**).

Jeśli nie potrzebujesz już IoT Hub i Cosmos DB wystąpień, usuń je z subskrypcji platformy Azure, aby uniknąć niepotrzebnych opłat.

## <a name="iot-hub-support"></a>Obsługa IoT Hub

Wiele funkcji IoT Hub nie obsługuje natywnie protobuf ani innych formatów binarnych. Na przykład nie można kierować na podstawie ładunku komunikatu, ponieważ IoT Hub nie będzie w stanie przetworzyć ładunku komunikatu. Można jednak kierować trasy na podstawie nagłówków komunikatów.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak dostosować symulację urządzenia, aby użyć usługi protobuf do wysłania telemetrii, następnym krokiem jest zapoznanie się z repozytorium GitHub w celu uzyskania większej [symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet).
