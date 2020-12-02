---
title: Opracowywanie i debugowanie modułów w programie Visual Studio — Azure IoT Edge
description: Użyj programu Visual Studio z narzędziami Azure IoT Tools, aby utworzyć moduł IoT Edge C lub C# i wypchnąć go z IoT Hub do urządzenia IoT zgodnie z konfiguracją manifestu wdrożenia.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2ae6d46198d979f91de5bf31d389f75961b4ab88
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437171"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Użyj programu Visual Studio 2019 do tworzenia i debugowania modułów dla Azure IoT Edge

Logikę biznesową można przekształcić w moduły Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio 2019 jako głównego narzędzia do tworzenia i debugowania modułów.

Narzędzia Azure IoT Edge Tools for Visual Studio oferują następujące korzyści:

- Twórz, Edytuj, Kompiluj, uruchamiaj i Debuguj Azure IoT Edge rozwiązania i moduły na lokalnym komputerze deweloperskim.
- Wdróż rozwiązanie Azure IoT Edge na Azure IoT Edge urządzeniu za pośrednictwem usługi Azure IoT Hub.
- Zakoduj moduły usługi Azure IoT w języku C lub C#, korzystając ze wszystkich korzyści związanych z programowaniem programu Visual Studio.
- Zarządzanie urządzeniami i modułami Azure IoT Edge za pomocą interfejsu użytkownika.

W tym artykule pokazano, jak za pomocą narzędzi Azure IoT Edge Tools for Visual Studio 2019 opracowywać moduły IoT Edge. Dowiesz się również, jak wdrożyć projekt na urządzeniu Azure IoT Edge. Obecnie program Visual Studio 2019 zapewnia obsługę modułów pisanych w językach C i C#. Obsługiwane architektury urządzeń to Windows x64 i Linux x64 lub ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz temat [Obsługa języków i architektury](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows jako komputera deweloperskiego. Na komputerach z systemem Windows można opracowywać moduły systemu Windows lub Linux. Aby opracowywać moduły systemu Windows, należy użyć komputera z systemem Windows w wersji 1809/Build 17763 lub nowszej. Aby utworzyć moduły systemu Linux, należy użyć komputera z systemem Windows, który spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Ponieważ ten artykuł używa programu Visual Studio 2019 jako głównego narzędzia deweloperskiego, zainstaluj program Visual Studio. Upewnij się, że w instalacji programu Visual Studio 2019 zawarto projektowanie i programowanie na **platformie Azure** **przy użyciu obciążeń języka C++** . Aby dodać wymagane obciążenia, można [zmodyfikować program Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) .

Po przygotowaniu programu Visual Studio 2019 potrzebne są również następujące narzędzia i składniki:

- Pobierz i zainstaluj [narzędzia Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z witryny Visual Studio Marketplace, aby utworzyć projekt IoT Edge w programie visual Studio 2019.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017, Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu vs 2017 z witryny Visual Studio Marketplace

- Pobierz i zainstaluj program [Docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim, aby skompilować i uruchomić obrazy modułu. Należy ustawić platformę Docker CE do uruchamiania w trybie kontenera systemu Linux lub w trybie kontenera Windows.

- Skonfiguruj lokalne środowisko programistyczne, aby debugować, uruchamiać i testować rozwiązanie IoT Edge przez zainstalowanie [narzędzia deweloperskiego usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Zainstaluj język [Python (2.7/3.6 +) i PIP](https://www.python.org/) , a następnie zainstaluj pakiet **iotedgehubdev** , uruchamiając następujące polecenie w terminalu. Upewnij się, że wersja narzędzia deweloperskiego usługi Azure IoT EdgeHub jest większa niż 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Sklonuj repozytorium i zainstaluj program Vcpkg Library Manager, a następnie zainstaluj **pakiet Azure-IoT-SDK-c** dla systemu Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](../container-registry/index.yml) lub [centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Możesz użyć lokalnego rejestru platformy Docker dla celów prototypu i testowania zamiast rejestru w chmurze.

- Aby przetestować moduł na urządzeniu, musisz mieć aktywne Centrum IoT z co najmniej jednym urządzeniem IoT Edge. Aby użyć komputera jako urządzenia IoT Edge, wykonaj kroki opisane w przewodniku szybki start dla systemu [Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli uruchamiasz demona IoT Edge na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed rozpoczęciem tworzenia aplikacji w programie Visual Studio.

### <a name="check-your-tools-version"></a>Sprawdź wersję narzędzi

1. Z menu **rozszerzenia** wybierz pozycję **Zarządzaj rozszerzeniami**. Rozwiń węzeł **zainstalowane > narzędzia** i możesz znaleźć **narzędzia Azure IoT Edge Tools for Visual Studio** i **Cloud Explorer dla programu Visual Studio**.

1. Zanotuj zainstalowaną wersję. Tę wersję można porównać z najnowszą wersją na Visual Studio Marketplace ([Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Jeśli Twoja wersja jest starsza niż dostępna w Visual Studio Marketplace, zaktualizuj swoje narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W oknie **Zarządzanie rozszerzeniami** rozwiń węzeł **aktualizacje > Visual Studio Marketplace**, wybierz pozycję **narzędzia Azure IoT Edge** lub **Eksplorator chmury dla programu Visual Studio** , a następnie wybierz pozycję **Aktualizuj**.

1. Po pobraniu aktualizacji narzędzi Zamknij program Visual Studio, aby wyzwolić aktualizację narzędzi przy użyciu Instalatora VSIX.

1. W instalatorze wybierz **przycisk OK** , aby rozpocząć, a następnie **zmodyfikuj** , aby zaktualizować narzędzia.

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij** i uruchom ponownie program Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Tworzenie projektu Azure IoT Edge

Szablon projektu Azure IoT Edge w programie Visual Studio tworzy projekt, który można wdrożyć na urządzeniach Azure IoT Edge na platformie Azure IoT Hub. Najpierw należy utworzyć rozwiązanie Azure IoT Edge, a następnie wygenerować pierwszy moduł w tym rozwiązaniu. Każde rozwiązanie IoT Edge może zawierać więcej niż jeden moduł.

> [!TIP]
> Struktura projektu IoT Edge utworzona przez program Visual Studio nie jest taka sama jak w programie Visual Studio Code.

1. W oknie dialogowym Nowy projekt programu Visual Studio Wyszukaj i wybierz pozycję **Azure IoT Edge** projekt, a następnie kliknij przycisk **dalej**. W oknie Konfiguracja projektu wprowadź nazwę projektu i określ lokalizację, a następnie wybierz pozycję **Utwórz**. Domyślna nazwa projektu to **AzureIoTEdgeApp1**.

   ![Tworzenie nowego projektu](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. W oknie **dodawanie IoT Edge aplikacji i modułu** wybierz pozycję **moduł C#** lub **moduł C** , a następnie określ nazwę modułu i repozytorium obrazu modułu. Program Visual Studio automatycznie wypełnia nazwę modułu nazwą **localhost: 5000/<nazwą \> modułu**. Zastąp go własnymi informacjami rejestru. Jeśli do testowania używasz lokalnego rejestru platformy Docker, **hosty localhost** jest w prawidłowym zakresie. Jeśli używasz Azure Container Registry, Użyj serwera logowania z ustawień rejestru. Serwer logowania wygląda jak **_\<registry name\>_ . azurecr.IO**. Zastąp tylko część **localhost: 5000** ciągu, aby wynik końcowy wyglądał jak **\<*registry name*\> . azurecr.IO/ _\<your module name\>_**. Domyślna nazwa modułu to **IotEdgeModule1**

   ![Dodaj aplikację i moduł](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Wybierz **przycisk OK** , aby utworzyć rozwiązanie Azure IoT Edge przy użyciu modułu korzystającego z języka C# lub C.

Teraz masz projekt **AzureIoTEdgeApp1. Linux. amd64** lub projekt **AzureIoTEdgeApp1. Windows. amd64** , a także projekt **IotEdgeModule1** w rozwiązaniu. Każdy projekt **AzureIoTEdgeApp1** ma `deployment.template.json` plik, który definiuje moduły, które mają zostać skompilowane i wdrożone dla rozwiązania IoT Edge, a także definiuje trasy między modułami. Domyślne rozwiązanie ma moduł **SimulatedTemperatureSensor** i moduł **IotEdgeModule1** . Moduł **SimulatedTemperatureSensor** generuje symulowane dane do modułu **IotEdgeModule1** , podczas gdy domyślny kod w module **IotEdgeModule1** bezpośrednio potoki odbierają komunikaty do IoT Hub platformy Azure.

Aby zobaczyć, jak działa symulowany czujnik temperatury, Wyświetl [kod źródłowy SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Projekt **IotEdgeModule1** jest aplikacją konsolową programu .net Core 2,1, jeśli jest to moduł języka C#. Zawiera wymagane pliki platformy Docker, które są potrzebne w przypadku urządzenia IoT Edge działającego z kontenerem kontenera systemu Windows lub Linux. `module.json`Plik opisuje metadane modułu. Rzeczywisty kod modułu, który pobiera zestaw SDK urządzenia usługi Azure IoT jako zależność, znajduje się w `Program.cs` pliku lub `main.c` .

## <a name="develop-your-module"></a>Opracowywanie modułu

Domyślny kod modułu, który jest dostarczany z rozwiązaniem, znajduje się w lokalizacji **IotEdgeModule1**  >  **program.cs** (dla języka C#) lub **Main. c** (c). Moduł i `deployment.template.json` plik są skonfigurowane tak, aby można było skompilować rozwiązanie, wypchnąć je do rejestru kontenerów i wdrożyć je na urządzeniu w celu uruchomienia testowania bez dotykania kodu. Moduł jest zbudowany z myślą o wejściu ze źródła (w tym przypadku modułu **SimulatedTemperatureSensor** , który symuluje dane) i potoku do IoT Hub platformy Azure.

Gdy wszystko jest gotowe do dostosowania szablonu modułu przy użyciu własnego kodu, użyj [zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md) do kompilowania modułów, które zaspokoją kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Zainicjuj iotedgehubdev z parametrami połączenia urządzenia IoT Edge

1. Skopiuj parametry połączenia dowolnego IoT Edge urządzenia z **podstawowych parametrów połączenia** w programie Visual Studio Cloud Explorer. Nie należy kopiować parametrów połączenia urządzenia niegranicznego, ponieważ ikona urządzenia IoT Edge różni się od ikony urządzenia niebędącego krawędzią.

   ![Kopiuj parametry połączenia urządzenia brzegowego](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. W menu **Narzędzia** wybierz kolejno opcje **Azure IoT Edge narzędzia**  >  **Konfiguracja IoT Edge symulator**, wklej parametry połączenia i kliknij przycisk **OK**.

   ![Otwórz okno Określanie parametrów połączenia Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Wprowadź parametry połączenia z pierwszego kroku, a następnie wybierz przycisk **OK**.

> [!NOTE]
> Należy wykonać te czynności tylko raz na komputerze deweloperskim, ponieważ wyniki są automatycznie stosowane do wszystkich kolejnych rozwiązań Azure IoT Edge. Po wykonaniu tej procedury można zmienić na inne parametry połączenia.

## <a name="build-and-debug-single-module"></a>Kompiluj i Debuguj pojedynczy moduł

Zazwyczaj należy przetestować i debugować każdy moduł przed uruchomieniem go w całym rozwiązaniu z wieloma modułami.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **IotEdgeModule1** i wybierz pozycję **Ustaw jako projekt startowy** z menu kontekstowego.

   ![Ustaw projekt startowy](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Naciśnij klawisz **F5** lub kliknij poniższy przycisk, aby uruchomić moduł; po raz pierwszy może upłynąć 10 &ndash; sekund.

   ![Uruchom moduł](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Jeśli moduł został zainicjowany pomyślnie, powinna zostać wyświetlona Aplikacja konsolowa platformy .NET Core.

   ![Uruchomiono moduł](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. W przypadku programowania w języku C# Ustaw punkt przerwania w `PipeMessage()` funkcji w **program.cs**; Jeśli używasz C, ustaw punkt przerwania w `InputQueue1Callback()` funkcji w **Main. C**. Następnie można go przetestować, wysyłając komunikat, uruchamiając następujące polecenie w powłoce **git bash** lub **WSL bash** . (Nie można uruchomić `curl` polecenia z programu PowerShell lub wiersza polecenia).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debuguj pojedynczy moduł](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Punkt przerwania powinien zostać wyzwolony. Możesz obejrzeć zmienne w oknie zmiennych **lokalnych** programu Visual Studio.

   > [!TIP]
   > Możesz również użyć narzędzia do [publikowania](https://www.getpostman.com/) lub innych narzędzi API do wysyłania wiadomości zamiast `curl` .

1. Naciśnij **klawisze CTRL + F5** lub kliknij przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Kompiluj i Debuguj IoT Edge rozwiązanie z wieloma modułami

Po zakończeniu opracowywania jednego modułu możesz chcieć uruchomić i debugować całe rozwiązanie z wieloma modułami.

1. W **Eksplorator rozwiązań** Dodaj drugi moduł do rozwiązania, klikając prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** i wybierając pozycję **Dodaj**  >  **Nowy moduł IoT Edge**. Domyślną nazwą drugiego modułu jest **IotEdgeModule2** i będzie działać jako inny moduł potoku.

1. Otwórz plik `deployment.template.json` i zobaczysz, że w sekcji **modułów** został dodany **IotEdgeModule2** . Zastąp sekcję **Routes** poniższymi. Jeśli dostosowano nazwy modułów, upewnij się, że te nazwy zostały zaktualizowane tak, aby były zgodne.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kliknij prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** , a następnie wybierz pozycję **Ustaw jako projekt startowy** z menu kontekstowego.

1. Utwórz punkty przerwania, a następnie naciśnij klawisz **F5** , aby uruchomić i debugować jednocześnie wiele modułów. Powinna zostać wyświetlona wiele okien aplikacji konsoli .NET Core, których każde okno reprezentuje inny moduł.

   ![Debuguj wiele modułów](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Naciśnij **klawisze CTRL + F5** lub wybierz przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-push-images"></a>Kompilowanie i wypychanie obrazów

1. Upewnij się, że **AzureIoTEdgeApp1** jest projektem startowym. Wybierz opcję **Debuguj** lub **Zwolnij** jako konfigurację, aby skompilować obrazy modułu.

    > [!NOTE]
    > Podczas wybierania **debugowania** program Visual Studio używa `Dockerfile.(amd64|windows-amd64).debug` do tworzenia obrazów platformy Docker. Obejmuje to debuger wiersza polecenia platformy .NET Core VSDBG w obrazie kontenera podczas jego tworzenia. W przypadku modułów IoT Edge gotowych do produkcji zalecamy użycie konfiguracji **wydania** , która używa `Dockerfile.(amd64|windows-amd64)` bez VSDBG.

1. Jeśli używasz prywatnego rejestru, takiego jak Azure Container Registry (ACR), użyj następującego polecenia platformy Docker, aby zalogować się do niego.  Nazwę użytkownika i hasło można uzyskać ze strony **klucze dostępu** w rejestrze w Azure Portal. W przypadku korzystania z rejestru lokalnego można [uruchomić rejestr lokalny](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Jeśli używasz prywatnego rejestru, takiego jak Azure Container Registry, musisz dodać informacje o logowaniu do rejestru w ustawieniach środowiska uruchomieniowego, które znajdują się w pliku `deployment.template.json` . Zastąp symbole zastępcze rzeczywistą nazwą użytkownika administratora ACR, hasło i nazwę rejestru.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** i wybierz pozycję **kompiluj i wypychanie modułów IoT Edge** , aby skompilować i wypchnąć obraz platformy Docker dla każdego modułu.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać za pomocą programu Cloud Explorer dla programu Visual Studio. Istnieje już manifest wdrożenia przygotowany dla danego scenariusza, `deployment.json` plik i wszystko, co należy zrobić, to wybierz urządzenie, które ma zostać odebrane.

1. Otwórz program **Cloud Explorer** , klikając pozycję **Wyświetl** program  >  **Cloud Explorer**. Upewnij się, że zalogowano się do programu Visual Studio 2019.

1. W programie **Cloud Explorer** Rozwiń swoją subskrypcję, Znajdź IoT Hub platformy Azure i urządzenie Azure IoT Edge, które chcesz wdrożyć.

1. Kliknij prawym przyciskiem myszy urządzenie IoT Edge, aby utworzyć dla niego wdrożenie. Przejdź do manifestu wdrożenia skonfigurowanego dla Twojej platformy znajdującej się w folderze **konfiguracyjnym** w rozwiązaniu programu Visual Studio, na przykład `deployment.arm32v7.json` .

1. Kliknij przycisk Odśwież, aby zobaczyć nowe moduły z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować komunikat D2C dla określonego urządzenia IoT-Edge, wybierz go w centrum IoT w programie **Cloud Explorer** , a następnie kliknij pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia** w oknie **akcji** .

1. Aby zatrzymać monitorowanie danych, wybierz pozycję **Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzenia** w oknie **akcji** .

## <a name="next-steps"></a>Następne kroki

Aby opracowywać niestandardowe moduły dla IoT Edge urządzeń, należy zapoznać się z zestawami [sdk IoT Hub platformy Azure i korzystać](../iot-hub/iot-hub-devguide-sdks.md)z nich.