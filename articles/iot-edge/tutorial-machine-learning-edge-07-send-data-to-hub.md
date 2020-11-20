---
title: 'Samouczek: wysyłanie danych urządzenia za pośrednictwem przezroczystej bramy — Machine Learning na Azure IoT Edge'
description: W tym samouczku pokazano, jak można użyć maszyny deweloperskiej jako symulowanego urządzenia IoT Edge do wysyłania danych do IoT Hub przez przechodzenie urządzenia jako niejawnej bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: f0695af6922182aa8be7acfb4b0a931bed35ef7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959309"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Samouczek: wysyłanie danych za pośrednictwem niewidocznej bramy

W tym artykule wielokrotnie korzystamy z maszyny wirtualnej tworzenia jako symulowanego urządzenia. Jednak zamiast bezpośredniego przesyłania danych do IoT Hub urządzenie wysyła dane do urządzenia IoT Edge skonfigurowanego jako nieprzezroczysta brama.

Monitorujemy działanie urządzenia IoT Edge, gdy symulowane urządzenie wysyła dane. Po zakończeniu działania urządzenia zapoznaj się z danymi na naszym koncie magazynu, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

Ten krok jest zwykle wykonywany przez programistę lub dewelopera urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="review-device-harness"></a>Przejrzyj zespół urządzeń

Ponownie Użyj [projektu DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) w celu zasymulowania urządzenia podrzędnego (lub liścia). Połączenie z nieprzezroczystą bramą wymaga dwóch dodatkowych czynności:

* Zarejestruj certyfikat, aby udostępnić podrzędnym urządzeniu IoT urząd certyfikacji używany przez środowisko uruchomieniowe IoT Edge. W naszym przypadku urządzenie podrzędne jest maszyną wirtualną programistyczną.
* Dodaj w pełni kwalifikowaną nazwę domeny (FQDN) bramy usługi Edge do parametrów połączenia urządzenia.

Spójrz na kod, aby zobaczyć, jak te dwa elementy są implementowane.

1. Na komputerze deweloperskim Otwórz Visual Studio Code.

1. Użyj **File**  >  **folderu Otwórz folder...** , aby otworzyć plik C: \\ Source \\ IoTEdgeAndMlSample \\ DeviceHarness.

1. Spójrz na metodę InstallCertificate () w Program.cs.

1. Należy pamiętać, że jeśli kod odnajdzie ścieżkę certyfikatu, wywołuje metodę CertificateManager. InstallCACert, aby zainstalować certyfikat na komputerze.

1. Teraz przyjrzyjmy się metodzie GetIotHubDevice klasy TurbofanDevice.

1. Jeśli użytkownik określi nazwę FQDN bramy przy użyciu opcji "-g", ta wartość jest przenoszona do tej metody jako `gatewayFqdn` zmienna, która jest dołączana do parametrów połączenia urządzenia.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Kompiluj i uruchom urządzenie liścia

1. Gdy projekt DeviceHarness jest wciąż otwarty w Visual Studio Code, Skompiluj projekt. Z menu **Terminal** wybierz polecenie **Uruchom zadanie kompilacji** i wybierz opcję **Kompiluj**.

1. Znajdź w pełni kwalifikowaną nazwę domeny (FQDN) dla bramy granicznej, przechodząc do urządzenia IoT Edge (maszyna wirtualna z systemem Linux) w Azure Portal i kopiując wartość **nazwy DNS** z strony przegląd.

1. Uruchom urządzenie IoT (maszyna wirtualna z systemem Linux), jeśli nie zostało jeszcze uruchomione.

1. Otwórz Terminal Visual Studio Code. Z menu **Terminal** wybierz pozycję **Nowy terminal** i uruchom następujące polecenie, zastępując `<edge_device_fqdn>` nazwę DNS skopiowaną z urządzenia IoT Edge (maszyna wirtualna z systemem Linux):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Aplikacja próbuje zainstalować certyfikat na komputerze deweloperskim. Gdy tak się robi, zaakceptuj ostrzeżenie o zabezpieczeniach.

1. Po wyświetleniu monitu o parametry połączenia IoT Hub kliknij przycisk wielokropka (**...**) w panelu urządzenia platformy Azure IoT Hub i wybierz polecenie **Kopiuj IoT Hub parametry połączenia**. Wklej wartość do terminalu.

1. Zobaczysz dane wyjściowe podobne do:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Należy pamiętać, że dodanie "GatewayHostName" do parametrów połączenia urządzenia, które powoduje, że urządzenie komunikuje się za pomocą IoT Hub za pomocą IoT Edge przezroczystej bramy.

## <a name="check-output"></a>Sprawdź dane wyjściowe

### <a name="iot-edge-device-output"></a>IoT Edge dane wyjściowe urządzenia

Dane wyjściowe modułu avroFileWriter można łatwo zaobserwować, przeglądając urządzenie IoT Edge.

1. Użyj protokołu SSH do maszyny wirtualnej IoT Edge.

1. Wyszukaj pliki zapisywane na dysku.

   ```bash
   find /data/avrofiles -type f
   ```

1. Dane wyjściowe polecenia będą wyglądać podobnie jak w poniższym przykładzie:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Może istnieć więcej niż jeden plik w zależności od czasu uruchomienia.

1. Zwróć uwagę na sygnatury czasowe. Moduł avroFileWriter przekazuje pliki do chmury po ostatniej modyfikacji w przeszłości ponad 10 minut (zobacz zmodyfikowany \_ \_ limit czasu pliku w Uploader.py w module avroFileWriter).

1. Po upływie 10 minut moduł powinien przekazać pliki. Jeśli przekazywanie zakończy się pomyślnie, program usunie pliki z dysku.

### <a name="azure-storage"></a>Azure Storage

Można obserwować wyniki wysyłania danych z urządzenia typu liść, przeglądając konta magazynu, w przypadku których oczekujemy, że dane mają być kierowane.

1. Na maszynie deweloperskiej Otwórz Visual Studio Code.

1. W panelu "AZURE STORAGE" w oknie Eksplorowanie przejdź do drzewa, aby znaleźć konto magazynu.

1. Rozwiń węzeł **kontenery obiektów BLOB** .

1. Z pracy wykonanej w poprzedniej części samouczka oczekujemy, że kontener **ruldata** powinien zawierać komunikaty z pozostałego czasu eksploatacji. Rozwiń węzeł **ruldata** .

1. Zobaczysz jeden lub więcej plików obiektów BLOB o nazwie like: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Kliknij prawym przyciskiem myszy jeden z plików, a następnie wybierz pozycję **Pobierz obiekt BLOB** , aby zapisać plik na komputerze deweloperskim.

1. Następnie rozwiń węzeł **uploadturbofanfiles** . W poprzednim artykule ustawimy tę lokalizację jako element docelowy dla plików przekazanych przez moduł avroFileWriter.

1. Kliknij prawym przyciskiem myszy pliki i wybierz polecenie **Pobierz obiekt BLOB** , aby zapisać go na komputerze deweloperskim.

### <a name="read-avro-file-contents"></a>Odczytaj zawartość pliku Avro

Dodaliśmy proste narzędzie wiersza polecenia do odczytywania pliku Avro i zwracania ciągu JSON komunikatów w pliku. W tej sekcji zostanie zainstalowana i uruchomiona.

1. Otwórz terminal w **Visual Studio Code (**  >  **Nowy terminal** terminalu).

1. Zainstaluj hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Użyj hubavroreader, aby odczytać plik Avro pobrany z usługi **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Należy zauważyć, że treść komunikatu wygląda zgodnie z oczekiwaniami z IDENTYFIKATORem urządzenia i przewidywaną pozostałego czasu eksploatacji.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Uruchom to samo polecenie, przekazując plik Avro pobrany z **uploadturbofanfiles**.

1. Zgodnie z oczekiwaniami te komunikaty zawierają wszystkie dane czujnika i ustawienia operacyjne z oryginalnej wiadomości. Te dane mogą służyć do ulepszania modelu pozostałego czasu eksploatacji na naszym urządzeniu brzegowym.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz Eksplorowanie zasobów używanych przez ten kompleksowy samouczek, zaczekaj na ukończenie czyszczenia utworzonych zasobów. W przeciwnym razie wykonaj następujące kroki, aby je usunąć:

1. Usuń grupy zasobów utworzone w celu przechowywania maszyny wirtualnej deweloperskiej, IoT Edge maszyny wirtualnej, IoT Hub, konta magazynu, usługi obszaru roboczego uczenia maszynowego (i utworzonych zasobów: Registry Container, Application Insights, Magazyn kluczy, konto magazynu).

1. Usuń projekt uczenia maszynowego w [Azure Notebooks](https://notebooks.azure.com).

1. Jeśli repozytorium zostało sklonowane lokalnie, zamknij wszystkie środowiska programu PowerShell lub VS Code systemu Windows odwołujące się do lokalnego repozytorium, a następnie usuń katalog repozytorium.

1. Jeśli certyfikaty zostały utworzone lokalnie, Usuń folder c: \\ edgeCertificates.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto naszej maszyny wirtualnej do programowania, aby symulować dane dotyczące czujnika i danych operacyjnych urządzenia liścia na naszym urządzeniu IoT Edge. Sprawdzono, że moduły na urządzeniu są kierowane, sklasyfikowane i utrwalane oraz przekazane do danych przez badanie działania urządzenia brzegowego w czasie rzeczywistym i przeglądanie plików przekazanych do konta magazynu.

Więcej informacji można znaleźć na następujących stronach:

> [!div class="nextstepaction"]
> [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

> [!div class="nextstepaction"]
> [Przechowuj dane na krawędzi za pomocą usługi Azure Blob Storage na IoT Edge (wersja zapoznawcza)](how-to-store-data-blob.md)
