---
title: 'Samouczek: Tworzenie i wdrażanie modułów niestandardowych — Machine Learning na Azure IoT Edge'
description: W tym samouczku pokazano, jak tworzyć i wdrażać moduły IoT Edge, które przetwarzają dane z urządzeń liściowych za pośrednictwem modelu uczenia maszynowego, a następnie wysyłają szczegółowe informacje do IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c7503cf51ab74dc486f83de8033bfe4850a8c68
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575061"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Samouczek: Tworzenie i wdrażanie niestandardowych modułów IoT Edge

W tym artykule tworzymy trzy moduły IoT Edge, które odbierają komunikaty z urządzeń IoT z liścia, uruchamiają dane za pośrednictwem modelu uczenia maszynowego, a następnie przesyłają szczegółowe informacje do IoT Hub.

IoT Edge Hub ułatwia komunikację modułu z modułem. Używanie Centrum IoT Edge jako brokera komunikatów zachowuje moduły niezależne od siebie. Moduły muszą określać dane wejściowe, na których są akceptowane wiadomości i dane wyjściowe, do których są zapisywane wiadomości.

Chcemy, aby IoT Edge urządzenie miało cztery rzeczy dla nas:

* Odbieraj dane z urządzeń liścia.
* Przewidywanie pozostałego okresu użytkowania (pozostałego czasu eksploatacji) dla urządzenia, które wysłało dane.
* Wyślij komunikat z pozostałego czasu eksploatacji dla urządzenia do IoT Hub. Ta funkcja może zostać zmodyfikowana w celu wysyłania danych tylko wtedy, gdy pozostałego czasu eksploatacji spadnie poniżej określonego poziomu.
* Zapisz dane urządzenia liścia w pliku lokalnym na urządzeniu IoT Edge. Ten plik danych jest okresowo przekazywany do IoT Hub w celu udoskonalenia szkolenia modelu uczenia maszynowego. Używanie przekazywania plików zamiast stałego przesyłania strumieniowego wiadomości jest tańsze.

Aby wykonać te zadania, używamy trzech modułów niestandardowych:

* **Klasyfikator pozostałego czasu eksploatacji:** Moduł turboFanRulClassifier utworzony podczas [uczenia i wdrażania modelu Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) jest standardowym modułem uczenia maszynowego, który udostępnia dane wejściowe o nazwie "amlInput" i dane wyjściowe o nazwie "amlOutput". "AmlInput" oczekuje, że dane wejściowe wyglądają dokładnie podobnie jak dane wejściowe wysyłane do usługi sieci Web opartej na ACI. Podobnie "amlOutput" zwraca te same dane, co usługa sieci Web.

* **Avro:** Ten moduł odbiera komunikaty w danych wejściowych "avroModuleInput" i utrzymuje komunikat w formacie Avro na dysku w celu późniejszego przekazania do IoT Hub.

* **Moduł routera:** Moduł routera odbiera komunikaty z podrzędnych urządzeń liścia, a następnie formatuje i wysyła komunikaty do klasyfikatora. Następnie moduł otrzymuje komunikaty z klasyfikatora i przekazuje komunikat do modułu Avro Writer. Na koniec moduł wysyła tylko prognozę pozostałego czasu eksploatacji do IoT Hub.

  * Danych wejściowych
    * **deviceInput**: odbiera komunikaty z urządzeń liścia
    * **rulInput:** odbiera komunikaty z "amlOutput"

  * Wydajności
    * **klasyfikowanie:** wysyła komunikaty do "amlInput"
    * **writeAvro:** wysyła komunikaty do "avroModuleInput"
    * **toIotHub:** wysyła komunikaty do $Upstream, które przekazują komunikaty do podłączonego IoT Hub

Na poniższym diagramie przedstawiono moduły, dane wejściowe, wyjścia i trasy Centrum IoT Edge dla pełnego rozwiązania:

![Diagram architektury IoT Edge trzech modułów](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroki opisane w tym artykule są zwykle wykonywane przez dewelopera chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="create-a-new-iot-edge-solution"></a>Utwórz nowe rozwiązanie IoT Edge

Podczas wykonywania drugiego z naszych dwóch Azure Notebooks utworzyliśmy i opublikowano obraz kontenera zawierający nasz model pozostałego czasu eksploatacji. Azure Machine Learning w ramach procesu tworzenia obrazu pakuje ten model, dzięki czemu obraz jest wdrażany jako moduł Azure IoT Edge.

W tym kroku utworzymy rozwiązanie Azure IoT Edge przy użyciu modułu "Azure Machine Learning" i wskażesz obraz opublikowany przy użyciu Azure Notebooks.

1. Otwórz sesję pulpitu zdalnego na maszynie wirtualnej deweloperskiej.

1. Otwórz folder **C: \\ Source \\ IoTEdgeAndMlSample** w Visual Studio Code.

1. Kliknij prawym przyciskiem myszy panel Eksplorator (w pustym obszarze) i wybierz polecenie **nowe IoT Edge rozwiązanie**.

    ![Utwórz nowe rozwiązanie IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Zaakceptuj domyślną nazwę rozwiązania **EdgeSolution**.

1. Wybierz **Azure Machine Learning** jako szablon modułu.

1. Nazwij moduł **turbofanRulClassifier**.

1. Wybierz obszar roboczy uczenia maszynowego. Ten obszar roboczy jest obszarem roboczym **turboFanDemo** utworzonym w [samouczku: uczenie i wdrażanie modelu Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)

1. Wybierz obraz utworzony podczas pracy z notesem platformy Azure.

1. Sprawdź rozwiązanie i zwróć uwagę na utworzone pliki:

   * **deployment.template.js:** Ten plik zawiera definicje wszystkich modułów w rozwiązaniu. W tym pliku znajdują się trzy sekcje, do których należy zwrócić uwagę:

     * **Poświadczenia rejestru:** Definiuje zbiór niestandardowych rejestrów kontenerów używanych w rozwiązaniu. Teraz powinien zawierać rejestr z obszaru roboczego uczenia maszynowego, w którym jest przechowywany obraz Azure Machine Learning. Możesz mieć dowolną liczbę rejestrów kontenerów, ale dla uproszczenia będziemy używać tego rejestru dla wszystkich modułów.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduły:** Ta sekcja zawiera zestaw modułów zdefiniowanych przez użytkownika, które przechodzą do tego rozwiązania. Definicja modułu turbofanRulClassifier wskazuje obraz w rejestrze kontenerów. Po dodaniu kolejnych modułów do rozwiązania zostaną one wyświetlone w tej sekcji.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Trasy:** będziemy pracować z trasami w tym samouczku. Trasy definiują, jak moduły komunikują się ze sobą. Istniejąca trasa zdefiniowana przez szablon nie jest zgodna z wymaganą trasą. Usuń `turbofanRulClassifierToIoTHub` trasę.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.js:** ten plik to wersja debugowania deployment.template.jswłączona. Zazwyczaj należy zachować ten plik zsynchronizowany z zawartością deployment.template.jsw pliku, ale nie jest to wymagane w tym samouczku.

   * **. env:** ten plik jest miejscem, w którym należy podać nazwę użytkownika i hasło, aby uzyskać dostęp do rejestru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Kliknij prawym przyciskiem myszy deployment.template.jsw pliku w Eksploratorze Visual Studio Code i wybierz polecenie **kompiluj IoT Edge rozwiązanie**.

1. Należy zauważyć, że to polecenie tworzy folder konfiguracji z deployment.amd64.jsem na pliku. Ten plik jest konkretnym szablonem wdrożenia dla rozwiązania.

## <a name="add-router-module"></a>Dodaj moduł routera

Następnie dodamy moduł routera do naszego rozwiązania. Moduł routera obsługuje kilka obowiązków dla naszych rozwiązań:

* **Odbieraj komunikaty z urządzeń typu liść:** po odebraniu komunikatów do urządzenia IoT Edge z urządzeń podrzędnych moduł routera odbiera komunikat i rozpoczyna organizowanie routingu wiadomości.
* **Wysyłaj komunikaty do modułu KLASYFIKATORA pozostałego czasu eksploatacji:** po odebraniu nowej wiadomości z urządzenia podrzędnego moduł routera przekształca komunikat w formacie, którego oczekuje klasyfikator pozostałego czasu eksploatacji. Router wysyła komunikat do klasyfikatora pozostałego czasu eksploatacji na potrzeby przewidywania pozostałego czasu eksploatacji. Gdy klasyfikator wykona prognozę, wysyła komunikat z powrotem do modułu routera.
* **Wysyłaj komunikaty pozostałego czasu eksploatacji do IoT Hub:** gdy router odbiera komunikaty z klasyfikatora, przekształca komunikat w taki sposób, aby zawierał tylko podstawowe informacje, identyfikator urządzenia i pozostałego czasu eksploatacji, i wysyła skrócony komunikat do centrum IoT Hub. Dalsze uściślenie, które nie zostało wykonane w tym miejscu, wyśle komunikaty do IoT Hub tylko wtedy, gdy Prognoza pozostałego czasu eksploatacji spadnie poniżej wartości progowej (na przykład gdy pozostałego czasu eksploatacji jest mniejsza niż 100 cykli). Filtrowanie w ten sposób zmniejsza ilość komunikatów i zmniejsza koszty Centrum IoT.
* **Wyślij komunikat do modułu Avro Writer:** aby zachować wszystkie dane wysyłane przez urządzenie podrzędne, moduł routera wysyła cały komunikat otrzymany z klasyfikatora do modułu Avro Writer, który poprowadzi i przekaże dane przy użyciu funkcji przekazywania plików IoT Hub.

Moduł routera jest ważnym elementem rozwiązania, które zapewnia, że komunikaty są przetwarzane w odpowiedniej kolejności.

### <a name="create-the-module-and-copy-files"></a>Tworzenie modułu i kopiowanie plików

1. Kliknij prawym przyciskiem myszy folder modules w Visual Studio Code i wybierz polecenie **Dodaj moduł IoT Edge**.

1. Wybierz **moduł C#** dla szablonu modułu.

1. Nazwij moduł **turbofanRouter**.

1. Po wyświetleniu monitu dotyczącego repozytorium obrazów platformy Docker Użyj rejestru z obszaru roboczego usługi Machine Learning (możesz znaleźć rejestr w węźle registryCredentials *wdeployment.template.js* pliku). Ta wartość jest w pełni kwalifikowanym adresem do rejestru, np **\<your registry\> . azurecr.IO/turbofanrouter**.

    > [!NOTE]
    > W tym artykule używamy Azure Container Registry, który został utworzony przez obszar roboczy Azure Machine Learning. Jest to wyłącznie wygoda. Możemy utworzyć nowy rejestr kontenerów i opublikować tam nasze moduły.

1. W terminalu przy użyciu powłoki wiersza polecenia skopiuj pliki z przykładowego modułu do rozwiązania.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Zaakceptuj monit, aby zastąpić plik program.cs.

### <a name="build-router-module"></a>Moduł tworzenia routera

1. W Visual Studio Code wybierz pozycję **Terminal**  >  **Skonfiguruj domyślne zadanie kompilacji**.

1. Wybierz pozycję **utwórz tasks.jsw pliku z szablonu**.

1. Wybierz pozycję **.NET Core**.

1. Zastąp zawartość tasks.jsprzy użyciu następującego kodu.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. Zapisz i Zamknij tasks.jsna.

1. Uruchom kompilację `Ctrl + Shift + B` z **Terminal** lub  >  **Uruchom zadanie kompilacji**.

### <a name="set-up-module-routes"></a>Konfigurowanie tras modułu

Jak wspomniano powyżej, środowisko uruchomieniowe IoT Edge używa tras skonfigurowanych w *deployment.template.js* pliku do zarządzania komunikacją między luźno połączonymi modułami. W tej sekcji omówiono sposób konfigurowania tras dla modułu turbofanRouter. Najpierw pokryjemy trasy wejściowe, a następnie przechodzą na dane wyjściowe.

#### <a name="inputs"></a>Dane wejściowe

1. W metodzie init () Program.cs rejestrujemy dwa wywołania zwrotne dla modułu:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Pierwsze wywołanie zwrotne nasłuchuje komunikatów wysyłanych do ujścia **deviceInput** . Na powyższym diagramie widzimy, że chcemy skierować komunikaty z dowolnego urządzenia liściowego do tego wejścia. W *deployment.template.jsna* pliku Dodaj trasę, która instruuje centrum brzegów, aby rozsyłał dowolny komunikat odebrany przez urządzenie IoT Edge, które nie zostało wysłane przez moduł IoT Edge do danych wejściowych o nazwie "deviceInput" w module turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Następnie Dodaj trasę do komunikatów z modułu rulClassifier do modułu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Dane wyjściowe

Dodaj cztery dodatkowe trasy do $edgeHub parametru trasy, aby obsłużyć dane wyjściowe modułu routera.

1. Program.cs definiuje metodę SendMessageToClassifier (), która używa klienta modułu do wysyłania komunikatu do klasyfikatora pozostałego czasu eksploatacji przy użyciu trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub () używa klienta modułu do wysyłania tylko danych pozostałego czasu eksploatacji dla urządzenia do IoT Hub za pośrednictwem trasy:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter () używa klienta modułu do wysyłania komunikatu z danymi pozostałego czasu eksploatacji, które zostały dodane do modułu avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage () wysyła do strumienia nieudanych komunikatów IoT Hub, gdzie mogą być kierowane do nich później.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Wszystkie trasy połączone z węzłem "$edgeHub" powinny wyglądać podobnie do następującego kodu JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > Dodanie modułu turbofanRouter spowodowało utworzenie następującej trasy dodatkowej: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream` . Usuń tę trasę, pozostawiając tylko trasy wymienione powyżej w deployment.template.jspliku.

## <a name="add-avro-writer-module"></a>Dodawanie modułu składnika zapisywania Avro

Moduł zapisywania Avro ma dwie obowiązki w naszym rozwiązaniu, do przechowywania wiadomości i przekazywania plików.

* **Komunikaty magazynu**: gdy moduł zapisywania Avro odbiera komunikat, zapisuje komunikat w lokalnym systemie plików w formacie Avro. Używamy instalacji wiązania, która instaluje katalog (w tym przypadku/Data/avrofiles) w ścieżce do kontenera modułu. Ta instalacja umożliwia modułowi zapis do ścieżki lokalnej (/avrofiles) i udostępnienie tych plików bezpośrednio z urządzenia IoT Edge.

* **Przekazywanie plików**: Moduł zapisywania Avro używa funkcji przekazywania plików IoT Hub platformy Azure do przekazywania plików na konto usługi Azure Storage. Po pomyślnym przekazaniu pliku moduł usuwa plik z dysku

### <a name="create-module-and-copy-files"></a>Utwórz moduł i skopiuj pliki

1. W Visual Studio Code wybierz pozycję **Widok**  >  **paleta poleceń**, a następnie wyszukaj i wybierz pozycję **Python: Wybierz interpreter**.

1. Wybierz zainstalowany język Python w wersji 3,7 lub nowszej.

1. Kliknij prawym przyciskiem myszy folder modules w Visual Studio Code i wybierz polecenie **Dodaj moduł IoT Edge**.

1. Wybierz pozycję **Moduł Python**.

1. Nadaj nazwę modułowi `avroFileWriter` .

1. Po wyświetleniu monitu dotyczącego repozytorium obrazów platformy Docker Użyj tego samego rejestru, który został użyty podczas dodawania modułu routera.

1. Kopiuj pliki z przykładowego modułu do rozwiązania.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Zaakceptuj zastąpienie main.py.

1. Zwróć uwagę, że filemanager.py i schema.py zostały dodane do rozwiązania, a main.py zostało zaktualizowane.

> [!NOTE]
> Po otwarciu pliku w języku Python może zostać wyświetlony monit o zainstalowanie pylint. Aby ukończyć ten samouczek, nie trzeba instalować Linter.

### <a name="bind-mount-for-data-files"></a>Powiąż instalację plików danych

Jak wspomniano wcześniej, moduł składnika zapisywania opiera się na obecności instalacji wiązania w celu zapisania plików Avro w systemie plików urządzenia.

#### <a name="add-directory-to-device"></a>Dodaj katalog do urządzenia

1. W Azure Portal Uruchom maszynę wirtualną urządzenia IoT Edge, jeśli nie została uruchomiona. Połącz się z nim przy użyciu protokołu SSH. Połączenie wymaga nazwy DNS, którą można skopiować ze strony przegląd dla maszyny wirtualnej w Azure Portal.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. Po zalogowaniu się Utwórz katalog, w którym będą przechowywane zapisane komunikaty urządzeń typu liść.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Zaktualizuj uprawnienia do katalogu, aby umożliwić możliwość zapisu przez kontener.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Sprawdź, czy katalog ma teraz uprawnienie Zapis (w) dla użytkownika, grupy i właściciela.

   ```bash
   ls -la /data
   ```

   ![Uprawnienia katalogu dla avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Dodaj katalog do modułu

Aby dodać katalog do kontenera modułu, zmodyfikujemy wieloetapowe dockerfile skojarzony z modułem avroFileWriter. Istnieją trzy wieloetapowe dockerfile skojarzone z modułem: pliku dockerfile. amd64, pliku dockerfile. amd64. Debug i pliku dockerfile. arm32v7. Te pliki powinny być utrzymywane w synchronizacji w przypadku, gdy chcemy debugować lub wdrożyć na urządzeniu arm32. W tym artykule należy skoncentrować się tylko na pliku dockerfile. amd64.

1. Na maszynie wirtualnej deweloperskiej Otwórz plik **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** .

1. Zmodyfikuj plik w taki sposób, aby wyglądał następująco:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir`Polecenia i `chown` instruują proces kompilacji platformy Docker w celu utworzenia katalogu najwyższego poziomu o nazwie/avrofiles w obrazie, a następnie aby moduleuser właściciela tego katalogu. Należy pamiętać, że te polecenia są wstawiane po dodaniu użytkownika modułu do obrazu za pomocą `useradd` polecenia, a przed przełączeniem kontekstu do moduleuser (User moduleuser).

1. W razie potrzeby wprowadź odpowiednie zmiany w pliku dockerfile. amd64. Debug i pliku dockerfile. arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Dodaj konfigurację powiązania do avroFileWriter

Ostatnim krokiem tworzenia powiązania jest aktualizacja deployment.template.js(i deployment.debug.template.jsna) plików z informacjami o powiązaniu.

1. Otwórz deployment.template.jsna.

2. Zmodyfikuj definicję modułu dla avroFileWriter przez dodanie `Binds` parametru, który wskazuje katalog kontenera/avrofiles do katalogu lokalnego na urządzeniu brzegowym. Definicja modułu powinna być zgodna z tym przykładem:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>Powiąż instalację, aby uzyskać dostęp do pliku config. YAML

Musimy dodać jeszcze jedno powiązanie dla modułu zapisywania. To powiązanie daje modułowi dostęp do odczytu parametrów połączenia z pliku/etc/iotedge/config.YAML na urządzeniu IoT Edge. Potrzebujemy parametrów połączenia w celu utworzenia elementu usługi iothubclient, aby umożliwić wywołanie \_ \_ metody asynchronicznej przekazywania obiektów BLOB w celu przekazywania plików do centrum IoT. Kroki związane z dodawaniem tego powiązania są podobne do tych w poprzedniej sekcji.

#### <a name="update-directory-permission"></a>Uprawnienie do aktualizacji katalogu

1. Połącz się z urządzeniem IoT Edge przy użyciu protokołu SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Dodaj uprawnienie do odczytu do pliku config. YAML.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Sprawdź, czy uprawnienia zostały ustawione prawidłowo.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Upewnij się, że uprawnienia do pliku config. YAML są **-r--r--r--**.

#### <a name="add-directory-to-module"></a>Dodaj katalog do modułu

1. Na komputerze deweloperskim Otwórz plik **pliku dockerfile. amd64** .

1. Dodaj do pliku dodatkowy zestaw `mkdir` poleceń i, `chown` Aby wygląda następująco:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. Wprowadź odpowiednie zmiany w pliku dockerfile. amd64. Debug i pliku dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizowanie konfiguracji modułu

1. Otwórz **deployment.template.js** pliku.

1. Zmodyfikuj definicję modułu dla avroFileWriter przez dodanie drugiego wiersza do `Binds` parametru, który wskazuje katalog kontenera (/App/iotconfig) do katalogu lokalnego na urządzeniu (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. Wprowadź odpowiednie zmiany w deployment.debug.template.js.

## <a name="install-dependencies"></a>Instalowanie zależności

Moduł zapisywania wykonuje zależność od dwóch bibliotek języka Python, fastavro i PyYAML. Musimy zainstalować zależności na naszym komputerze deweloperskim i nakazać procesowi kompilacji platformy Docker zainstalowanie ich w obrazie modułu.

### <a name="pyyaml"></a>PyYAML

1. Na komputerze deweloperskim Otwórz `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` plik i Dodaj "pyyaml" w nowym wierszu w pliku.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Otwórz plik **pliku dockerfile. amd64** i Dodaj polecenie, `pip install` Aby uaktualnić setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. W wierszu polecenia zainstaluj program pyyaml na komputerze deweloperskim.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. W requirements.txt Dodaj fastavro po pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Zainstaluj program fastavro na komputerze deweloperskim.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Skonfiguruj ponownie IoT Hub

Wprowadzając IoT Edge urządzenia i moduły do systemu, zmieniłeś nasze oczekiwania na to, jakie dane zostaną wysłane do centrum i w jakim celu. Musimy zmienić konfigurację routingu w centrum, aby zająć się naszym nowym rzeczywistości.

> [!NOTE]
> Ponownie skonfigurujemy centrum przed wdrożeniem modułów, ponieważ niektóre ustawienia centrum, w tym przekazywanie plików, muszą być poprawnie skonfigurowane do prawidłowego działania modułu avroFileWriter

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Konfigurowanie trasy dla komunikatów pozostałego czasu eksploatacji w IoT Hub

W przypadku routera i klasyfikatora oczekuje się otrzymywania zwykłych komunikatów zawierających tylko identyfikator urządzenia i prognozowanie pozostałego czasu eksploatacji dla urządzenia. Chcemy skierować dane pozostałego czasu eksploatacji do własnej lokalizacji przechowywania, aby można było monitorować stan urządzeń, tworzyć raporty i uruchamiać alerty w razie potrzeby. W tym samym czasie chcemy, aby wszystkie dane urządzeń, które są nadal wysyłane bezpośrednio przez urządzenie liścia, które nie zostały jeszcze dołączone do urządzenia IoT Edge, aby kontynuować kierowanie do bieżącej lokalizacji magazynu.

#### <a name="create-a-rul-message-route"></a>Tworzenie trasy komunikatów pozostałego czasu eksploatacji

1. W Azure Portal przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Obsługa wiadomości** wybierz pozycję **routing wiadomości**.

1. Na karcie **trasy** wybierz pozycję **Dodaj**.

1. Nazwij trasę **RulMessageRoute**.

1. Wybierz pozycję **Dodaj punkt końcowy** po prawej stronie selektora **punktów końcowych** , a następnie wybierz pozycję **Magazyn**.

1. Na stronie **Dodawanie punktu końcowego magazynu** Nadaj nazwę punktowi końcowemu **ruldata**.

1. Wybierz pozycję **Wybierz kontener**.

1. Na stronie **konta magazynu** Znajdź konto magazynu używane w tym samouczku, którego nazwa jest taka sama jak **iotedgeandml \<unique suffix\>**.

1. Wybierz kontener **ruldata** , a następnie kliknij pozycję **Wybierz**.

1. Wróć na stronę **Dodawanie punktu końcowego magazynu** i wybierz pozycję **Utwórz** , aby utworzyć punkt końcowy magazynu.

1. Z powrotem na stronie **Dodawanie trasy** dla **kwerendy routingu** Zastąp ciąg `true` następującym zapytaniem:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Rozwiń sekcję **test** , a następnie sekcję **treść wiadomości** . Zastąp treść komunikatu tym przykładem oczekiwanych komunikatów:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Wybierz opcję **trasa testowa**. Jeśli test zakończy się pomyślnie, zobaczysz komunikat pasujący do zapytania.

1. Kliknij przycisk **Zapisz**.

#### <a name="update-turbofandevicedatatostorage-route"></a>Aktualizowanie trasy turbofanDeviceDataToStorage

Nie chcemy kierować nowych danych prognoz do naszej starej lokalizacji magazynu, więc zaktualizuj trasę, aby zapobiec.

1. Na stronie IoT Hub **Routing komunikatów** wybierz kartę **trasy** .

1. Wybierz **turbofanDeviceDataToStorage** lub inną nazwę nadaną do początkowej trasy danych urządzenia.

1. Aktualizowanie zapytania routingu do

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Rozwiń sekcję **test** , a następnie sekcję **treść wiadomości** . Zastąp komunikat tym przykładem oczekiwanych komunikatów:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. Wybierz opcję **trasa testowa**. Jeśli test zakończy się pomyślnie, zobaczysz komunikat pasujący do zapytania.

1. Wybierz pozycję **Zapisz**.

### <a name="configure-file-upload"></a>Konfigurowanie przekazywania plików

Skonfiguruj funkcję przekazywania plików IoT Hub, aby umożliwić modułowi zapisywania plików przekazywanie plików do magazynu.

1. W menu po lewej stronie w IoT Hub w obszarze **Obsługa wiadomości** wybierz pozycję **przekazywanie plików**.

1. Wybierz **kontener usługi Azure Storage**.

1. Wybierz konto magazynu z listy.

1. Wybierz kontener, który rozpoczyna się od platformy **Azure elementu BlobStore** z identyfikatorem GUID, a następnie kliknij przycisk **Wybierz**.

1. Wybierz pozycję **Zapisz**. W portalu zostanie wyświetlone powiadomienie po zakończeniu zapisywania.

> [!Note]
> Nie włączamy powiadomień dotyczących przekazywania dla tego samouczka, ale zobacz [odbieranie powiadomienia o przekazywaniu plików](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) , aby uzyskać szczegółowe informacje na temat obsługi powiadomienia o przekazywaniu plików.

## <a name="build-publish-and-deploy-modules"></a>Kompilowanie, publikowanie i wdrażanie modułów

Po wprowadzeniu zmian w konfiguracji wszystko jest gotowe do skompilowania obrazów i opublikowania ich w usłudze Azure Container Registry. Proces kompilacji używa deployment.template.jsna pliku, aby określić, które moduły muszą zostać skompilowane. Ustawienia dla każdego modułu, w tym wersji, znajdują się w module.jsw pliku w folderze modułu. Proces kompilacji najpierw uruchamia kompilację platformy Docker na wieloetapowe dockerfile zgodnym z bieżącą konfiguracją znalezioną w module.jsw pliku, aby utworzyć obraz. Następnie publikuje obraz w rejestrze z module.jsw pliku ze znacznikiem wersji pasującym do tej w module.jspliku. Na koniec tworzy manifest wdrożenia specyficzny dla konfiguracji (na przykład deployment.amd64.json), który zostanie wdrożony na urządzeniu IoT Edge. Urządzenie IoT Edge odczytuje informacje z manifestu wdrożenia i zgodnie z instrukcjami pobierze moduły, skonfiguruje trasy i ustawi wszystkie wymagane właściwości. Ta metoda wdrażania ma dwa efekty uboczne, z którymi należy się zapoznać:

* **Opóźnienie wdrożenia:** ponieważ środowisko uruchomieniowe IoT Edge musi rozpoznać zmianę żądanych właściwości przed rozpoczęciem ponownej konfiguracji, może upłynąć trochę czasu po wdrożeniu modułów do momentu pobrania przez środowisko uruchomieniowe i zaktualizowania urządzenia IoT Edge.

* **Wersje modułów:** w przypadku opublikowania nowej wersji kontenera modułu w rejestrze kontenerów przy użyciu tych samych tagów wersji jak w poprzednim module środowisko uruchomieniowe nie pobierze nowej wersji modułu. Wykonuje ono porównanie znacznika Version obrazu lokalnego i żądanego obrazu z manifestu wdrożenia. Jeśli te wersje są zgodne, środowisko uruchomieniowe nie przyjmuje żadnej akcji. W związku z tym ważne jest, aby zwiększyć wersję modułu za każdym razem, gdy chcesz wdrożyć nowe zmiany. Zwiększ wersję, zmieniając właściwość **Version** we właściwości **tag** w module.jsw pliku dla modułu, który jest zmieniany. Następnie Skompiluj i Opublikuj moduł.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Kompilowanie i publikowanie

1. Na maszynie wirtualnej programistycznej Uruchom platformę Docker, jeśli nie została uruchomiona.

1. W Visual Studio Code Uruchom nowy terminal z wierszem polecenia i zaloguj się do usługi Azure Container Registry (ACR).

  Wymagane wartości Nazwa użytkownika, hasło i serwer logowania można znaleźć w Azure Portal. Nazwa rejestru kontenerów ma format "turbofandemo \<unique id\> ". W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **klucze dostępu** , aby je wyświetlić.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. W Visual Studio Code kliknij prawym przyciskiem myszy deployment.template.jsna i wybierz polecenie **Kompiluj i wypchnij IoT Edge rozwiązanie**.

### <a name="view-modules-in-the-registry"></a>Wyświetlanie modułów w rejestrze

Po pomyślnym zakończeniu kompilacji będziemy mogli używać Azure Portal do przeglądania naszych opublikowanych modułów.

1. Otwórz Azure Container Registry w tym samouczku. Nazwa rejestru kontenerów ma format "turbofandemo \<unique id\> ". 

1. W menu po lewej stronie w obszarze **usługi** wybierz pozycję **repozytoria**.

1. Należy zauważyć, że oba moduły utworzone, **avrofilewriter** i **turbofanrouter** są wyświetlane jako repozytoria.

1. Wybierz pozycję **turbofanrouter** i pamiętaj, że Opublikowano jeden obraz oznaczony jako 0.0.1-amd64.

   ![Wyświetlanie pierwszej otagowanej wersji turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Wdrażanie modułów na IoT Edge urządzeniu

Moduły zostały skompilowane i zostały skonfigurowane w naszym rozwiązaniu. teraz wdrażamy moduły na urządzeniu IoT Edge.

1. W Visual Studio Code kliknij prawym przyciskiem myszy **deployment.amd64.jsna** pliku w folderze config.

1. Wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

1. Wybierz urządzenie IoT Edge, **aaTurboFanEdgeDevice**.

1. Odśwież panel usługi Azure IoT Hub Devices w Eksploratorze Visual Studio Code. Powinny zostać wyświetlone trzy nowe moduły, ale jeszcze nie działają.

1. Odśwież ponownie za kilka minut i zobaczysz uruchomione moduły.

   ![Wyświetlanie uruchomionych modułów w Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Uruchomienie modułów i rozliczenie w stałym stanie uruchomienia może potrwać kilka minut. W tym czasie można zobaczyć, że moduły zaczynają i zatrzymują się podczas próby nawiązania połączenia z modułem Centrum IoT Edge.

## <a name="diagnosing-failures"></a>Diagnozowanie błędów

W tej sekcji udostępniamy kilka technik, aby zrozumieć, co zostało usunięte z modułu lub modułów. Często awarię można najpierw uzyskać ze stanu w Visual Studio Code.

### <a name="identify-failed-modules"></a>Identyfikowanie modułów zakończonych niepowodzeniem

* **Visual Studio Code:** Zobacz panel usługi Azure IoT Hub Devices. Jeśli większość modułów jest w stanie uruchomionym, ale jeden z nich jest zatrzymany, należy sprawdzić, czy zatrzymany moduł został jeszcze zatrzymywany. Jeśli wszystkie moduły są w stanie zatrzymania przez długi czas, może to oznaczać również niepowodzenie.

* **Azure Portal:** Przechodząc do centrum IoT Hub w portalu, a następnie wyszukując stronę szczegóły urządzenia (w obszarze IoT Edge przejdź do szczegółów urządzenia), możesz się dowiedzieć, że moduł zgłosił błąd lub nigdy nie zgłosił niczego do centrum IoT Hub.

### <a name="diagnosing-from-the-device"></a>Diagnozowanie z urządzenia

Logując się do urządzenia IoT Edge (maszyna wirtualna z systemem Linux w naszym przypadku), możesz uzyskać dostęp do wystarczającej ilości informacji na temat stanu modułów. Głównym mechanizmem używanym przez nas są polecenia platformy Docker, które umożliwiają nam badanie kontenerów i obrazów na urządzeniu.

1. Zaloguj się do urządzenia IoT Edge:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Wyświetl listę wszystkich uruchomionych kontenerów. Oczekujemy, że dla każdego modułu zostanie wyświetlony kontener o nazwie odpowiadającej modułowi. Ponadto to polecenie wyświetla dokładny obraz dla kontenera, w tym wersję, tak aby można było dopasować się do oczekiwań. Możesz również wyświetlić listę obrazów, zastępując "Image" "Container" w poleceniu.

   ```bash
   sudo docker container ls
   ```

1. Pobierz dzienniki dla kontenera. To polecenie wyprowadza dane, które zostały wprowadzone do StdErr i StdOut w kontenerze. To polecenie działa w przypadku kontenerów, które zostały uruchomione, a następnie zapadły z jakiegoś powodu. Jest on również przydatny do poznania informacji o tym, co się dzieje z kontenerami edgeAgent lub edgeHub.

   ```bash
   sudo docker container logs <container id>
   ```

1. Inspekcja kontenera. To polecenie daje tona informacji o obrazie. Dane można filtrować w zależności od tego, czego szukasz. Jeśli na przykład chcesz zobaczyć, czy powiązania na avroFileWriter są poprawne, możesz użyć polecenia:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Połącz z działającym kontenerem. To polecenie może być przydatne, jeśli chcesz przeanalizować kontener, gdy jest on uruchomiony:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyliśmy IoT Edge rozwiązanie w Visual Studio Code z trzema modułami: klasyfikatorem, routerem i składnikiem zapisywania/obiektu przekazującego. Skonfigurujemy trasy, aby umożliwić modułom komunikowanie się ze sobą na urządzeniu brzegowym. Zmodyfikowano konfigurację urządzenia brzegowego i Zaktualizowano wieloetapowe dockerfile w celu zainstalowania zależności i dodania instalacji powiązań do kontenerów modułów. 

Następnie Zaktualizowaliśmy konfigurację IoT Hub w celu rozesłania komunikatów opartych na typie i obsłudze przekazywania plików. We wszystkich miejscach wdrożono moduły na urządzeniu IoT Edge i upewnimy się, że moduły działały prawidłowo.

Przejdź do następnego artykułu, aby rozpocząć wysyłanie danych i zobaczyć rozwiązanie w działaniu.

> [!div class="nextstepaction"]
> [Wysyłanie danych za pośrednictwem nieprzezroczystej bramy](tutorial-machine-learning-edge-07-send-data-to-hub.md)