---
title: Tworzenie i debugowanie modułów dla Azure IoT Edge | Microsoft Docs
description: Używanie Visual Studio Code do tworzenia, kompilowania i debugowania modułu dla aplikacji Azure IoT Edge języka C#, Python, Node.js, Java lub C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 7cc5061911ffc2cbc91dfa8c0d2fd1a6e59ff1a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875669"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Używanie Visual Studio Code do tworzenia i debugowania modułów dla Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Logikę biznesową można przekształcić w moduły dla Azure IoT Edge. W tym artykule pokazano, jak używać Visual Studio Code jako głównego narzędzia do tworzenia i debugowania modułów.

Istnieją dwa sposoby debugowania modułów napisanych w języku C#, Node.js lub Java w języku Visual Studio Code: możesz dołączyć proces w kontenerze modułów lub uruchomić kod modułu w trybie debugowania. Aby debugować moduły napisane w języku Python lub C, można dołączyć do procesu tylko w kontenerach linux amd64.

Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj o [debugowaniu](https://code.visualstudio.com/Docs/editor/debugging).

Ten artykuł zawiera instrukcje dotyczące tworzenia i debugowania modułów w wielu językach dla wielu architektur. Obecnie program Visual Studio Code obsługę modułów napisanych w językach C#, C, Python, Node.js i Java. Obsługiwane architektury urządzeń to X64 i ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz [Obsługa języka i architektury.](module-development.md#language-and-architecture-support)

>[!NOTE]
>Obsługa opracowywania i debugowania dla urządzeń z systemem Linux ARM64 jest w [publicznej wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać więcej informacji, zobacz [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Tworzenie](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)i debugowanie modułów arm64 w Visual Studio Code (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Jako maszyny dewelopera możesz użyć komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux. Na komputerach z systemem Windows można tworzyć moduły systemu Windows lub Linux. Aby tworzyć moduły systemu Windows, użyj komputera z systemem Windows w wersji 1809/kompilacji 17763 lub nowszej. Aby opracowywać moduły systemu Linux, użyj komputera z systemem Windows, który [spełnia wymagania dotyczące programu Docker Desktop.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Najpierw [Visual Studio Code,](https://code.visualstudio.com/) a następnie dodaj następujące rozszerzenia:

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozszerzenie platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio rozszerzenia specyficzne dla języka, w jakim programujesz:
  - C#, w tym Azure Functions: [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [pakiet rozszerzeń Java dla języka Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [rozszerzenie C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Aby opracować moduł, musisz również zainstalować kilka dodatkowych narzędzi specyficznych dla języka:

- C#, w tym Azure Functions: [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: [python i](https://www.python.org/downloads/) pip [do](https://pip.pypa.io/en/stable/installing/#installation) instalowania pakietów języka Python (zwykle dołączonych do instalacji języka Python).

- Node.js: [Node.js](https://nodejs.org). Należy również zainstalować program [Yeoman](https://www.npmjs.com/package/yo) i generator [Azure IoT Edge Node.js Module Generator.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) i [Maven.](https://maven.apache.org/) Należy ustawić [zmienną środowiskową `JAVA_HOME` tak,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) aby wskazać instalację zestawu JDK.

Aby skompilować i wdrożyć obraz modułu, potrzebujesz platformy Docker do skompilowania obrazu modułu i rejestru kontenerów do przechowywania obrazu modułu:

- [Docker Community Edition](https://docs.docker.com/install/) na komputerze dewelopera.

- [Azure Container Registry](../container-registry/index.yml) lub [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Zamiast rejestru w chmurze można użyć lokalnego rejestru platformy Docker do celów prototypowych i testowych.

Jeśli nie opracowujesz modułu w języku C, potrzebujesz również narzędzia deweloperskiego [usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) opartego na języku Python w celu skonfigurowania lokalnego środowiska deweloperskiego do debugowania, uruchamiania i testowania IoT Edge deweloperskiego. Jeśli jeszcze tego nie zrobiono, zainstaluj język [Python (2.7/3.6/3.7)](https://www.python.org/) i program Pip, a następnie zainstaluj program **iotedgehubdev,** uruchamiając to polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Obecnie iotedgehubdev używa biblioteki docker-py, która nie jest zgodna z platformą Python 3.8.
>
> Jeśli masz wiele języka Python, w tym wstępnie zainstalowanego języka Python 2.7 (na przykład w systemie Ubuntu lub macOS), upewnij się, że używasz poprawnego środowiska lub , aby zainstalować program `pip` `pip3` **iotedgehubdev**

Aby przetestować moduł na urządzeniu, potrzebne będzie aktywne centrum IoT z co najmniej jednym IoT Edge urządzeniem. Aby używać komputera jako urządzenia IoT Edge, wykonaj kroki opisane w przewodniku Szybki start dla systemu [Linux](quickstart-linux.md) lub [Windows.](quickstart.md) Jeśli używasz demona IoT Edge deweloperskim, może być konieczne zatrzymanie edgeHub i EdgeAgent przed przejściem do następnego kroku.

## <a name="create-a-new-solution-template"></a>Tworzenie nowego szablonu rozwiązania

Poniższe kroki pokazują, jak utworzyć moduł IoT Edge w preferowanym języku programowania (w tym w języku Azure Functions napisanym w języku C#) przy użyciu języka Visual Studio Code i Azure IoT Tools. Zaczniesz od utworzenia rozwiązania, a następnie wygenerowania pierwszego modułu w tym rozwiązaniu. Każde rozwiązanie może zawierać wiele modułów.

1. Wybierz **pozycję View** Command Palette  >  **(Wyświetl paletę poleceń).**

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: Nowe IoT Edge rozwiązanie.**

   ![Uruchamianie nowego IoT Edge rozwiązania](./media/how-to-develop-csharp-module/new-solution.png)

1. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie wybierz **pozycję Wybierz folder**.

1. Wprowadź nazwę rozwiązania.

1. Wybierz szablon modułu dla preferowanego języka programowania jako pierwszy moduł w rozwiązaniu.

1. Wprowadź nazwę modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenerów.

1. Podaj nazwę repozytorium obrazów modułu. Visual Studio Code automatycznie zasypuje nazwę modułu nazwą **modułu localhost:5000/<\> nazwę modułu.** Zastąp go własnymi informacjami rejestru. Jeśli do testowania używasz lokalnego rejestru platformy Docker, **localhost** jest w porządku. Jeśli używasz Azure Container Registry, użyj serwera logowania z ustawień rejestru. Serwer logowania wygląda następująco: **_\<registry name\>_ .azurecr.io**. Zastąp tylko **część localhost:5000** ciągu, aby wynik końcowy wyglądał następująco: **\<*registry name*\> .azurecr.io/ _\<your module name\>_**.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code przyjmuje podane informacje, tworzy IoT Edge, a następnie ładuje je w nowym oknie.

W rozwiązaniu znajdują się cztery elementy:

- Folder **.vscode** zawiera konfiguracje debugowania.

- Folder **modules** zawiera podfoldery dla każdego modułu.  W folderze dla każdego modułu znajduje się plik o **nazwiemodule.js**, który kontroluje sposób zbudowania i wdrożenia modułów.  Ten plik należy zmodyfikować, aby zmienić rejestr kontenerów wdrażania modułu z localhost na rejestr zdalny. W tym momencie masz tylko jeden moduł.  Możesz jednak dodać więcej w palecie poleceń za pomocą polecenia **Azure IoT Edge: Add IoT Edge Module**(Dodaj IoT Edge Module).

- Plik **env** zawiera listę zmiennych środowiskowych. Jeśli Azure Container Registry rejestrem, będziesz mieć w nim Azure Container Registry użytkownika i hasło.

  > [!NOTE]
  > Plik środowiska jest tworzony tylko wtedy, gdy udostępnisz repozytorium obrazów dla modułu. Jeśli zaakceptowaliśmy domyślne ustawienia hosta localhost do lokalnego testowania i debugowania, nie trzeba deklarować zmiennych środowiskowych.

- Sekcja **deployment.template.js** zawiera nowy moduł wraz z przykładowym modułem **SimulatedTemperatureSensor,** który symuluje dane, których można użyć do testowania. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia, zobacz Dowiedz się, jak używać manifestów wdrożenia do [wdrażania modułów i ustanawiania tras.](module-composition.md)

Aby zobaczyć, jak działa moduł symulowanej temperatury, wyświetl kod źródłowy [SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Dodawanie kolejnych modułów

Aby dodać kolejne moduły do rozwiązania, uruchom polecenie **Azure IoT Edge: Add IoT Edge Module** (Dodaj moduł IoT Edge) z palety poleceń. Możesz również kliknąć prawym przyciskiem myszy folder **modules** lub plik w widoku Eksploratora Visual Studio Code, a następnie wybrać pozycję `deployment.template.json` Dodaj moduł IoT Edge **Module.**

## <a name="develop-your-module"></a>Opracowywanie modułu

Domyślny kod modułu dostarczany z rozwiązaniem znajduje się w następującej lokalizacji:

- Funkcja platformy Azure (C#): **moduły >  >  *&lt; &gt; modułowi nazwę* *&lt; &gt;* modułu .cs**
- C#: **moduły > nazwę modułu *&lt; > &gt;* Program.cs**
- Python: **moduły > *&lt; nazwę modułu &gt; > main.py***
- Node.js: moduły **> *&lt; nazwę modułu > app.js&gt;***
- Java: **moduły > *&lt; nazwę &gt;* modułu > src > main > java > com > edgemodulemodules > App.java**
- C: **moduły > nazwę modułu *&lt; > &gt;* main.c**

Moduł i plik deployment.template.jssą tak skonfigurować, aby można było skompilować rozwiązanie, wypchnąć je do rejestru kontenerów i wdrożyć na urządzeniu, aby rozpocząć testowanie bez dotykania żadnego kodu. Moduł został zbudowany tak, aby po prostu przyjmować dane wejściowe ze źródła (w tym przypadku modułu SimulatedTemperatureSensor, który symuluje dane) i potokować je do IoT Hub.

Gdy wszystko będzie gotowe do dostosowania szablonu przy użyciu własnego kodu, użyj zestawów SDK usługi [Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów, które zasygną kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Debugowanie modułu bez kontenera (C#, Node.js, Java)

W przypadku programowania w języku C#, Node.js lub Java moduł wymaga użycia obiektu **ModuleClient** w domyślnym kodzie modułu, aby można było uruchamiać, uruchamiać i rozsyłać komunikaty. Użyjesz również domyślnego kanału wejściowego **input1,** aby podjąć akcję, gdy moduł odbierze komunikaty.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla IoT Edge rozwiązania

Na komputerze dewelopera można uruchomić symulator IoT Edge zamiast instalować demona zabezpieczeń IoT Edge, aby można było uruchomić IoT Edge rozwiązania.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia IoT Edge, a następnie wybierz pozycję Setup IoT Edge Simulator (Skonfiguruj IoT Edge **Simulator),** aby uruchomić symulator przy użyciu parametrów połączenia urządzenia.
1. Możesz zobaczyć, IoT Edge symulator został pomyślnie ustawiony, odczytując szczegóły postępu w zintegrowanym terminalu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie IoT Edge dla aplikacji z jednym modułem

Aby skonfigurować i uruchomić symulator, uruchom polecenie **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** (Uruchom symulator IoT Edge Hub dla pojedynczego modułu) z palety Visual Studio Code poleceń. Po wyświetleniu monitu użyj wartości **input1** z domyślnego kodu modułu (lub równoważnej wartości z kodu) jako nazwy wejściowej aplikacji. Polecenie wyzwala interfejs wiersza **polecenia iotedgehubdev,** a następnie uruchamia IoT Edge i kontener modułu narzędzia do testowania. Jeśli symulator został pomyślnie uruchomiony w trybie jednego modułu, w zintegrowanym terminalu możesz zobaczyć poniższe dane wyjściowe. Możesz również wyświetlić `curl` polecenie, które pomaga wysyłać wiadomości. Użyjesz jej później.

   ![Konfigurowanie IoT Edge dla aplikacji z jednym modułem](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Możesz użyć widoku Eksplorator platformy Docker w Visual Studio Code, aby zobaczyć stan uruchomienia modułu.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   Kontener **edgeHubDev** jest rdzeniem lokalnego IoT Edge symulatora. Może działać na komputerze dewelopera bez demona IoT Edge zabezpieczeń i zapewnia ustawienia środowiska dla natywnej aplikacji modułu lub kontenerów modułów. Kontener **wejściowy** uwidacznia interfejsy API REST, aby ułatwić mostkowanie komunikatów do docelowego kanału wejściowego w module.

### <a name="debug-module-in-launch-mode"></a>Debugowanie modułu w trybie uruchamiania

1. Przygotuj środowisko do debugowania zgodnie z wymaganiami języka programowania, ustaw punkt przerwania w module i wybierz konfigurację debugowania do użycia:
   - **C#**
     - W Visual Studio Code terminalu zmień katalog ***&lt; &gt;*** na folder nazwy modułu, a następnie uruchom następujące polecenie, aby skompilować aplikację .NET Core.

       ```cmd
       dotnet build
       ```

     - Otwórz plik `Program.cs` i dodaj punkt przerwania.

     - Przejdź do widoku Visual Studio Code debugowania, wybierając pozycję **Wyświetl > debugowania.** Wybierz z listy **_&lt; rozwijanej konfigurację &gt;_ debugowania o nazwie Debuguj lokalny (.NET Core).**

        > [!NOTE]
        > Jeśli twoja ścieżka programu .NET Core nie jest zgodna ze ścieżką programu w programie , musisz ręcznie zaktualizować ścieżkę programu w pliku , aby była zgodna z wartością w pliku `TargetFramework` `launch.json` `launch.json` csproj, Visual Studio Code pomyślnie uruchomić `TargetFramework` ten program.

   - **Node.js**
     - W Visual Studio Code terminalu ***&lt; &gt;*** zmień katalog na folder nazwy modułu, a następnie uruchom następujące polecenie, aby zainstalować pakiety Node

       ```cmd
       npm install
       ```

     - Otwórz plik `app.js` i dodaj punkt przerwania.

     - Przejdź do widoku Visual Studio Code debugowania, wybierając pozycję **Wyświetl > debugowania.** Wybierz z listy **_&lt; rozwijanej &gt;_** konfigurację debugowania o nazwie Node.js modułu.
   - **Java**
     - Otwórz plik `App.java` i dodaj punkt przerwania.

     - Przejdź do widoku Visual Studio Code debugowania, wybierając pozycję **Wyświetl > debugowania.** Wybierz z listy **_&lt; rozwijanej konfigurację &gt;_ debugowania o nazwie lokalnego debugowania (Java).**

1. Kliknij **przycisk Rozpocznij debugowanie** lub naciśnij **klawisz F5,** aby rozpocząć sesję debugowania.

1. W Visual Studio Code terminalu uruchom następujące polecenie, aby wysłać **komunikat Hello world** do modułu. Jest to polecenie pokazane w poprzednich krokach podczas IoT Edge symulatora.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz systemu Windows, upewnij się, że powłoka Visual Studio Code zintegrowanym terminalem to **Git Bash** lub **WSL Bash.** Nie można uruchomić polecenia `curl` z programu PowerShell lub wiersza polecenia.
   > [!TIP]
   > Możesz również użyć narzędzia [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API do wysyłania komunikatów za pośrednictwem usługi zamiast `curl` .

1. W Visual Studio Code debugowania zobaczysz zmienne w panelu po lewej stronie.

1. Aby zatrzymać sesję debugowania, wybierz przycisk Zatrzymaj lub naciśnij klawisze **Shift + F5,** a następnie uruchom polecenie **Azure IoT Edge: Zatrzymaj** symulator IoT Edge na palecie poleceń, aby zatrzymać symulator i wyczyścić go.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debugowanie w trybie dołączania za pomocą IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Rozwiązanie domyślne zawiera dwa moduły, jeden jest modułem symulowanego czujnika temperatury, a drugi modułem potoku. Symulowany czujnik temperatury wysyła komunikaty do modułu potoku, a następnie są one potokowane do IoT Hub. W utworzonym folderze modułu znajduje się kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego pliku, który kończy się **rozszerzeniem .debug,** aby skompilować moduł do testowania.

Obecnie debugowanie w trybie dołączania jest obsługiwane tylko w następujący sposób:

- Moduły języka C#, w tym moduły dla Azure Functions, obsługują debugowanie w kontenerach amd64 systemu Linux
- Node.js obsługują debugowanie w kontenerach linux amd64 i arm32v7 oraz kontenerach systemu Windows amd64
- Moduły Języka Java obsługują debugowanie w kontenerach linux amd64 i arm32v7

> [!TIP]
> Możesz przełączać się między opcjami domyślnej platformy dla rozwiązania IoT Edge, klikając element na pasku Visual Studio Code stanu.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla IoT Edge rozwiązania

Na komputerze dewelopera można uruchomić symulator IoT Edge zamiast instalować demona zabezpieczeń IoT Edge, aby można było uruchomić IoT Edge rozwiązania.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia IoT Edge, a następnie wybierz pozycję Skonfiguruj symulator **IoT Edge,** aby uruchomić symulator przy użyciu parametrów połączenia urządzenia.

1. Możesz zobaczyć, IoT Edge symulator został pomyślnie ustawiony, odczytując szczegóły postępu w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Kompilowanie i uruchamianie kontenera na potrzeby debugowania i debugowania w trybie dołączania

1. Otwórz plik modułu ( `Program.cs` , , lub ) i dodaj punkt `app.js` `App.java` `<your module name>.cs` przerwania.

1. W widoku Visual Studio Code Explorer kliknij prawym przyciskiem myszy plik rozwiązania, a następnie wybierz polecenie Skompilowanie i IoT Edge `deployment.debug.template.json` **w symulatorze**. Wszystkie dzienniki kontenerów modułów można obserwować w tym samym oknie. Możesz również przejść do widoku platformy Docker, aby obserwować stan kontenera.

   ![Zmienne obserwowania](media/how-to-vs-code-develop-module/view-log.png)

1. Przejdź do widoku Visual Studio Code Debugowanie i wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinna być podobna do ***&lt; nazwy modułu debugowania &gt;* zdalnego**

1. Wybierz **pozycję Rozpocznij debugowanie** lub naciśnij **klawisz F5.** Wybierz proces, do który chcesz dołączyć.

1. W Visual Studio Code debugowania zobaczysz zmienne w panelu po lewej stronie.

1. Aby zatrzymać sesję debugowania, najpierw wybierz przycisk Zatrzymaj lub naciśnij klawisze **Shift + F5,** a następnie wybierz pozycję **Azure IoT Edge: Zatrzymaj** IoT Edge Symulator poleceń z palety poleceń.

> [!NOTE]
> W poprzednim przykładzie pokazano, jak debugować moduły IoT Edge kontenerach. Dodano ujawnione porty do ustawień kontenera `createOptions` modułu. Po zakończeniu debugowania modułów zalecamy usunięcie tych ujawnionych portów dla modułów IoT Edge produkcyjnych.
>
> W przypadku modułów napisanych w języku C#, w tym Azure Functions, ten przykład jest oparty na wersji debugowania programu , która zawiera debuger wiersza polecenia .NET Core (VSDBG) w obrazie kontenera podczas jego `Dockerfile.amd64.debug` budowania. Po debugowaniu modułów języka C# zalecamy bezpośrednie użycie pliku Dockerfile bez usługi VSDBG na potrzeby modułów IoT Edge produkcyjnych.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Debugowanie modułu za pomocą IoT Edge uruchomieniowego

W każdym folderze modułu istnieje kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego z plików, które kończą się **rozszerzeniem .debug,** aby skompilować moduł do testowania.

Podczas debugowania modułów przy użyciu tej metody moduły są uruchomione na IoT Edge uruchomieniowym. Urządzenie IoT Edge i urządzenie Visual Studio Code mogą być na tym samym komputerze lub, częściej, Visual Studio Code znajduje się na maszynie dewelopera, a środowisko uruchomieniowe IoT Edge i moduły są uruchomione na innej maszynie fizycznej. Aby debugować z Visual Studio Code, należy:

- Skonfiguruj urządzenie IoT Edge, skompilowanie modułu IoT Edge przy użyciu pliku **.debug** Dockerfile, a następnie wdrożenie na urządzeniu IoT Edge danych.
- Uwidocznij adres IP i port modułu, aby można było dołączyć debuger.
- Zaktualizuj plik , aby Visual Studio Code do procesu w `launch.json` kontenerze na maszynie zdalnej. Ten plik znajduje się w folderze w obszarze roboczym i jest aktualizowany za każdym razem, gdy dodajesz nowy `.vscode` moduł obsługujący debugowanie.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Kompilowanie i wdrażanie modułu na urządzeniu IoT Edge aplikacji

1. W Visual Studio Code otwórz plik zawierający wersję debugowania obrazów modułów z `deployment.debug.template.json` ustawionymi `createOptions` prawidłowymi wartościami.

1. Jeśli opracowujesz moduł w języku Python, przed przystąpieniem wykonaj następujące kroki:
   - Otwórz plik i `main.py` dodaj ten kod po sekcji importowania:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Dodaj następujący pojedynczy wiersz kodu do wywołania zwrotnego, które chcesz debugować:

      ```python
      ptvsd.break_into_debugger()
      ```

     Jeśli na przykład chcesz debugować funkcję, wstaw ten wiersz kodu, `receive_message_listener` jak pokazano poniżej:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. W palecie Visual Studio Code poleceń:
   1. Uruchom polecenie **Azure IoT Edge: Kompilowanie i wypychanie IoT Edge rozwiązania**.

   1. Wybierz `deployment.debug.template.json` plik rozwiązania.

1. W sekcji **Azure IoT Hub Urządzenia** w widoku Visual Studio Code Explorer:
   1. Kliknij prawym przyciskiem myszy identyfikator IoT Edge, a następnie wybierz pozycję Create Deployment for Single Device (Utwórz **wdrożenie dla pojedynczego urządzenia).**

      > [!TIP]
      > Aby upewnić się, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność $edgeHub **i** **$edgeAgent**. Każde IoT Edge zawiera te dwa moduły.

   1. Przejdź do folderu  konfiguracji rozwiązania, wybierz plik, a następnie wybierz pozycję `deployment.debug.amd64.json` Wybierz manifest wdrożenia usługi **Edge.**

Wdrożenie pomyślnie utworzone z identyfikatorem wdrożenia zostanie wyświetlony w zintegrowanym terminalu.

Stan kontenera możesz sprawdzić, uruchamiając `docker ps` polecenie w terminalu. Jeśli twoje Visual Studio Code i IoT Edge uruchomieniowe działają na tym samym komputerze, możesz również sprawdzić stan w widoku Visual Studio Code Platformy Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Uwidocznij adres IP i port modułu dla debugera

Możesz pominąć tę sekcję, jeśli moduły są uruchomione na tej samej maszynie co program Visual Studio Code, ponieważ używasz hosta lokalnego do dołączania do kontenera i masz już poprawne ustawienia portu w pliku **.debug** Dockerfile, ustawieniach kontenera modułu i `createOptions` `launch.json` pliku. Jeśli moduły i Visual Studio Code są uruchomione na oddzielnych maszynach, wykonaj kroki dla języka programowania.

- **C#, w tym Azure Functions**

  [Skonfiguruj kanał SSH na komputerze dewelopera](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) i IoT Edge, a następnie edytuj `launch.json` plik, aby dołączyć.

- **Node.js**

  - Upewnij się, że moduł na debugowanych maszynach jest uruchomiony i gotowy do dołączenia debugerów, a port 9229 jest dostępny zewnętrznie. Możesz to sprawdzić, otwierając `http://<target-machine-IP>:9229/json` polecenie na maszynie debugera. Ten adres URL powinien pokazywać informacje o Node.js modułu do debugowania.
  
  - Na komputerze dewelopera otwórz program Visual Studio Code, a następnie `launch.json` **_&lt; &gt;_** ***&lt; &gt;*** edytuj go, tak aby wartość adresu profilu debugowania zdalnego (Node.js) nazwy modułu (lub nazwa modułu Debugowanie zdalne (Node.js w kontenerze systemu Windows), jeśli moduł jest uruchomiony jako kontener systemu Windows) był adresem IP debugowanych maszyn.

- **Java**

  - Skompilowanie tunelu SSH do maszyny do debugowania przez uruchomienie polecenia `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Na komputerze dewelopera otwórz Visual Studio Code i ***&lt; &gt;*** edytuj nazwę swojego profilu zdalnego debugowania (Java) w programie , aby można było `launch.json` dołączyć go do maszyny docelowej. Aby dowiedzieć się więcej na temat `launch.json` edytowania i debugowania języka Java Visual Studio Code, zobacz sekcję na temat [konfigurowania debugera](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Upewnij się, że port 5678 na debugowanych maszynach jest otwarty i dostępny.

  - W kodzie, który został wcześniej wstawiony do , zmień `ptvsd.enable_attach(('0.0.0.0', 5678))` `main.py` adres **0.0.0.0** na adres IP maszyny do debugowania. Skompilować, wypchnąć i wdrożyć IoT Edge moduł.

  - Na komputerze dewelopera otwórz Visual Studio Code, a `launch.json` `host` ***&lt; &gt;*** następnie edytuj polecenie , aby wartość profilu debugowania zdalnego (Python) nazwy modułu zawierała adres IP maszyny docelowej, a nie `localhost` .

### <a name="debug-your-module"></a>Debugowanie modułu

1. W widoku Visual Studio Code Debugowanie wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinna być podobna do ***&lt; nazwy modułu Debugowanie &gt;* zdalne**

1. Otwórz plik modułu dla języka programowania i dodaj punkt przerwania:

   - **Azure Function (C#):** Dodaj punkt przerwania do pliku `<your module name>.cs` .
   - **C#:** Dodaj punkt przerwania do pliku `Program.cs` .
   - **Node.js:** Dodaj punkt przerwania do pliku `app.js` .
   - **Java:** Dodaj punkt przerwania do pliku `App.java` .
   - **Python:** dodaj punkt przerwania do pliku w `main.py` metodzie wywołania zwrotnego, do której dodano `ptvsd.break_into_debugger()` wiersz.
   - **C:** Dodaj punkt przerwania do pliku `main.c` .

1. Wybierz **pozycję Rozpocznij debugowanie** lub wybierz **klawisz F5.** Wybierz proces, do który chcesz dołączyć.

1. W Visual Studio Code Debugowanie zobaczysz zmienne w panelu po lewej stronie.

> [!NOTE]
> W poprzednim przykładzie pokazano, jak debugować moduły IoT Edge kontenerach. W ten sposób dodano widoczne porty do ustawień kontenera `createOptions` modułu. Po zakończeniu debugowania modułów zalecamy usunięcie tych ujawnionych portów dla modułów IoT Edge produkcyjnych.

## <a name="build-and-debug-a-module-remotely"></a>Zdalne kompilowanie i debugowanie modułu

Dzięki ostatnim zmianom w aparatach platform Docker i Moby w celu obsługi połączeń SSH oraz nowemu ustawieniu usługi Azure IoT Tools, które umożliwia iniekcja ustawień środowiska w palecie poleceń platformy Visual Studio Code i terminalach usługi Azure IoT Edge, można teraz kompilować i debugować moduły na urządzeniach zdalnych.

Zobacz ten [wpis w blogu dewelopera IoT,](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) aby uzyskać więcej informacji i instrukcje krok po kroku.

## <a name="next-steps"></a>Następne kroki

Po s zbudowaniu modułu dowiedz się, jak [wdrażać Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Aby tworzyć moduły dla urządzeń IoT Edge, [opis i używanie Azure IoT Hub SDK.](../iot-hub/iot-hub-devguide-sdks.md)