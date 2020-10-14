---
title: Opracowywanie i debugowanie modułów dla Azure IoT Edge | Microsoft Docs
description: Używanie Visual Studio Code do tworzenia, kompilowania i debugowania modułu dla Azure IoT Edge przy użyciu języków C#, Python, Node.js, Java lub C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: ebc12e6d64d015267497497bebc22c8586adf999
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043735"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Użyj Visual Studio Code do tworzenia i debugowania modułów dla Azure IoT Edge

Logikę biznesową można przekształcić w moduły Azure IoT Edge. W tym artykule przedstawiono sposób użycia Visual Studio Code jako głównego narzędzia do tworzenia i debugowania modułów.

Istnieją dwa sposoby debugowania modułów pisanych w języku C#, Node.js lub Java w Visual Studio Code: można dołączyć proces do kontenera modułów lub uruchomić kod modułu w trybie debugowania. Aby debugować moduły w języku Python lub C, można dołączyć tylko do procesu w kontenerach z systemem Linux amd64.

Jeśli nie znasz możliwości debugowania Visual Studio Code, przeczytaj informacje o [debugowaniu](https://code.visualstudio.com/Docs/editor/debugging).

Ten artykuł zawiera instrukcje dotyczące projektowania i debugowania modułów w wielu językach w przypadku wielu architektur. Obecnie Visual Studio Code zapewnia obsługę modułów pisanych w językach C#, C, Python, Node.js i Java. Obsługiwane architektury urządzeń to x64 i ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz temat [Obsługa języków i architektury](module-development.md#language-and-architecture-support).

>[!NOTE]
>Obsługa programowania i debugowania dla urządzeń z systemem Linux ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Wymagania wstępne

Możesz użyć komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux jako komputera deweloperskiego. Na komputerach z systemem Windows można opracowywać moduły systemu Windows lub Linux. Aby opracowywać moduły systemu Windows, należy użyć komputera z systemem Windows w wersji 1809/Build 17763 lub nowszej. Aby utworzyć moduły systemu Linux, należy użyć komputera z systemem Windows, który spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Najpierw zainstaluj [Visual Studio Code](https://code.visualstudio.com/) a następnie Dodaj następujące rozszerzenia:

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozszerzenie platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Rozszerzenia programu Visual Studio specyficzne dla języka, w którym tworzysz program:
  - C#, w tym Azure Functions: [rozszerzenie c#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [pakiet rozszerzeń Java dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [rozszerzenie c/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Należy również zainstalować kilka dodatkowych narzędzi specyficznych dla języka w celu opracowania modułu:

- C#, w tym Azure Functions: [zestaw SDK programu .NET Core 2,1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) i [PIP](https://pip.pypa.io/en/stable/installing/#installation) do instalowania pakietów języka Python (zwykle dołączonych do instalacji języka Python).

- Node.js: [Node.js](https://nodejs.org). Warto również zainstalować [Narzędzia Yeoman](https://www.npmjs.com/package/yo) oraz [Generator modułów Node.js Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) i [Maven](https://maven.apache.org/). Należy [ustawić `JAVA_HOME` zmienną środowiskową tak,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) aby wskazywała instalację JDK.

Do kompilowania i wdrażania obrazu modułu potrzebna jest platforma Docker umożliwiająca utworzenie obrazu modułu i rejestru kontenerów w celu przechowywania obrazu modułu:

- Platforma [Docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim.

- [Azure Container Registry](../container-registry/index.yml) lub [centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Możesz użyć lokalnego rejestru platformy Docker dla celów prototypu i testowania zamiast rejestru w chmurze.

Jeśli nie tworzysz modułu w języku C, potrzebujesz także [narzędzia deweloperskiego usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) opartego na języku Python, aby skonfigurować lokalne środowisko programistyczne do debugowania, uruchamiania i testowania rozwiązania IoT Edge. Jeśli jeszcze tego nie zrobiono, zainstaluj język [Python (2.7/3.6/3.7) i polecenie PIP](https://www.python.org/) , a następnie zainstaluj **iotedgehubdev** , uruchamiając to polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Obecnie iotedgehubdev używa biblioteki Docker-PR, która nie jest zgodna z językiem Python 3,8.
>
> Jeśli masz wiele języków Python, w tym wstępnie zainstalowane środowisko Python 2,7 (na przykład na Ubuntu lub macOS), upewnij się, że używasz poprawnego programu `pip` lub `pip3` do zainstalowania **iotedgehubdev**

Aby przetestować moduł na urządzeniu, musisz mieć aktywne Centrum IoT z co najmniej jednym urządzeniem IoT Edge. Aby użyć komputera jako urządzenia IoT Edge, wykonaj kroki opisane w przewodniku szybki start dla systemu [Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli używasz demona IoT Edge na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed przejściem do następnego kroku.

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższe kroki pokazują, jak utworzyć moduł IoT Edge w preferowanym języku programistycznym (w tym Azure Functions, zapisanym w języku C#) przy użyciu Visual Studio Code i narzędzi Azure IoT Tools. Zacznij od utworzenia rozwiązania, a następnie wygenerowania pierwszego modułu w tym rozwiązaniu. Każde rozwiązanie może zawierać wiele modułów.

1. Wybierz pozycję **Widok**  >  **paleta poleceń**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe IoT Edge rozwiązanie**.

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie wybierz pozycję **Wybierz folder**.

1. Wprowadź nazwę rozwiązania.

1. Wybierz szablon modułu dla preferowanego języka deweloperskiego jako pierwszy moduł w rozwiązaniu.

1. Wprowadź nazwę modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenerów.

1. Podaj nazwę repozytorium obrazu modułu. Visual Studio Code automatycznie wypełnia nazwę modułu nazwą **localhost: 5000/<nazwę \> modułu**. Zastąp go własnymi informacjami rejestru. Jeśli do testowania używasz lokalnego rejestru platformy Docker, **hosty localhost** jest w prawidłowym zakresie. Jeśli używasz Azure Container Registry, Użyj serwera logowania z ustawień rejestru. Serwer logowania wygląda jak ** _\<registry name\>_ . azurecr.IO**. Zastąp tylko część **localhost: 5000** ciągu, aby wynik końcowy wyglądał jak ** \<*registry name*\> . azurecr.IO/ _\<your module name\>_ **.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code pobiera podane informacje, tworzy rozwiązanie IoT Edge, a następnie ładuje je w nowym oknie.

W rozwiązaniu znajdują się cztery elementy:

- Folder **. programu vscode** zawiera konfiguracje debugowania.

- Folder **modułów** ma podfoldery dla każdego modułu.  W folderze dla każdego modułu istnieje plik, **module.json**, który kontroluje sposób kompilowania i wdrażania modułów.  Należy zmodyfikować ten plik, aby zmienić rejestr kontenerów wdrożenia modułu z hosta lokalnego na Rejestr zdalny. W tym momencie masz tylko jeden moduł.  Można jednak dodać więcej w palecie poleceń za pomocą polecenia **Azure IoT Edge: Dodaj moduł IoT Edge**.

- Plik **ENV** zawiera listę zmiennych środowiskowych. Jeśli Azure Container Registry jest rejestrem, będziesz mieć w niej Azure Container Registry nazwę użytkownika i hasło.

  > [!NOTE]
  > Plik środowiska jest tworzony tylko wtedy, gdy podajesz repozytorium obrazu dla modułu. Jeśli ustawienia domyślne hosta lokalnego zostały zaakceptowane do testowania i debugowania lokalnego, nie trzeba deklarować zmiennych środowiskowych.

- **deployment.template.jsw** pliku zawiera listę nowego modułu wraz z przykładowym modułem **SimulatedTemperatureSensor** , który symuluje dane, których można użyć do testowania. Aby uzyskać więcej informacji o działaniu manifestów wdrożenia, zobacz temat [jak używać manifestów wdrożenia do wdrażania modułów i ustanawiania tras](module-composition.md).

Aby zobaczyć, jak działa moduł symulowanej temperatury, Wyświetl [kod źródłowy SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Dodawanie dodatkowych modułów

Aby dodać dodatkowe moduły do rozwiązania, uruchom polecenie **Azure IoT Edge: Dodaj moduł IoT Edge** z palety poleceń. Możesz również kliknąć prawym przyciskiem myszy folder **moduły** lub `deployment.template.json` plik w widoku Eksploratora Visual Studio Code, a następnie wybrać polecenie **Dodaj moduł IoT Edge**.

## <a name="develop-your-module"></a>Opracowywanie modułu

Domyślny kod modułu dostarczany wraz z rozwiązaniem znajduje się w następującej lokalizacji:

- Funkcja platformy Azure (C#): **moduły > * &lt; nazwą &gt; *modułu Nazwa  >  * &lt; modułu &gt; *. cs**
- C#: **moduły > * &lt; nazwą &gt; modułu* > program.cs**
- Python: **moduły > * &lt; nazwą &gt; modułu* > Main.py**
- Node.js: **moduły > * &lt; nazwą &gt; modułu* > app.js**
- Java: **moduły > * &lt; &gt; nazwą modułu* > src > main > java > com > edgemodulemodules > App. Java**
- C: **moduły > * &lt; nazwą &gt; modułu* > Main. c**

Moduł i deployment.template.jsw pliku są skonfigurowane tak, aby można było skompilować rozwiązanie, wypchnąć je do rejestru kontenerów i wdrożyć je na urządzeniu w celu uruchomienia testowania bez dotykania kodu. Moduł jest zbudowany, aby po prostu przejąć dane wejściowe ze źródła (w tym przypadku moduł SimulatedTemperatureSensor, który symuluje dane) i potok do IoT Hub.

Gdy wszystko jest gotowe do dostosowania szablonu przy użyciu własnego kodu, użyj [zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md) do kompilowania modułów, które zaspokoją kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Debugowanie modułu bez kontenera (C#, Node.js, Java)

Jeśli tworzysz w języku C#, Node.js lub Java, moduł wymaga użycia obiektu **ModuleClient** w domyślnym kodzie modułu, aby można było uruchamiać, uruchamiać i routować komunikaty. Użyjesz również domyślnego kanału wejściowego **INPUT1** , aby wykonać akcję, gdy moduł odbiera komunikaty.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla IoT Edge rozwiązania

Na komputerze deweloperskim można uruchomić symulator IoT Edge zamiast instalować demona IoT Edge Security, aby można było uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia IoT Edge, a następnie wybierz pozycję **instalator IoT Edge symulator** , aby uruchomić symulator z parametrami połączenia urządzenia.
1. Można sprawdzić, czy IoT Edge symulator został pomyślnie skonfigurowany, odczytując szczegóły postępu w zintegrowanym terminalu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie symulatora IoT Edge dla aplikacji pojedynczego modułu

Aby skonfigurować i uruchomić symulator, uruchom polecenie **Azure IoT Edge: Uruchom symulator centrum IoT Edge dla jednego modułu** z palety poleceń Visual Studio Code. Po wyświetleniu monitu użyj wartości **INPUT1** z domyślnego kodu modułu (lub równoważnej wartości z kodu) jako nazwy wejściowej dla aplikacji. Polecenie wyzwala interfejs wiersza polecenia **iotedgehubdev** , a następnie uruchamia symulator IoT Edge i kontener modułu narzędzia do testowania. Poniższe dane wyjściowe można zobaczyć w zintegrowanym terminalu, jeśli symulator został uruchomiony pomyślnie w trybie pojedynczego modułu. Możesz również zobaczyć polecenie, `curl` Aby ułatwić wysyłanie komunikatów. Użyjesz jej później.

   ![Konfigurowanie symulatora IoT Edge dla aplikacji pojedynczego modułu](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Możesz użyć widoku programu Docker Explorer w Visual Studio Code, aby wyświetlić stan uruchomienia modułu.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   Kontener **edgeHubDev** jest rdzeńm symulatora IoT Edge lokalnego. Można go uruchamiać na komputerze deweloperskim bez demona Security IoT Edge i udostępnia ustawienia środowiska dla aplikacji modułu macierzystego lub kontenerów modułów. Kontener **wejściowy** uwidacznia interfejsy API REST, aby pomóc w mostku komunikatów do docelowego kanału wejściowego w module.

### <a name="debug-module-in-launch-mode"></a>Debuguj moduł w trybie uruchamiania

1. Przygotuj środowisko do debugowania zgodnie z wymaganiami języka deweloperskiego, ustaw punkt przerwania w module i wybierz konfigurację debugowania do użycia:
   - **C#**
     - W Visual Studio Code zintegrowanym terminalu Zmień katalog na folder *** &lt; nazwa &gt; modułu*** , a następnie uruchom następujące polecenie, aby skompilować aplikację .NET Core.

       ```cmd
       dotnet build
       ```

     - Otwórz plik `Program.cs` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania Visual Studio Code, wybierając pozycję **wyświetl > Debuguj**. Wybierz konfigurację debugowania ** * &lt; nazwa &gt; modułu* debugowanie lokalne (.NET Core)** z listy rozwijanej.

        > [!NOTE]
        > Jeśli platforma .NET Core `TargetFramework` nie jest spójna z ścieżką programu w programie `launch.json` , należy ręcznie zaktualizować ścieżkę programu w programie, `launch.json` aby była zgodna z `TargetFramework` plikiem. csproj, dzięki czemu Visual Studio Code może pomyślnie uruchomić ten program.

   - **Node.js**
     - W Visual Studio Code zintegrowanym terminalu Zmień katalog na folder *** &lt; nazwa &gt; modułu*** , a następnie uruchom następujące polecenie, aby zainstalować pakiety węzłów

       ```cmd
       npm install
       ```

     - Otwórz plik `app.js` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania Visual Studio Code, wybierając pozycję **wyświetl > Debuguj**. Wybierz konfigurację debugowania ** * &lt; nazwa &gt; modułu* debugowanie lokalne (Node.js)** z listy rozwijanej.
   - **Java**
     - Otwórz plik `App.java` i Dodaj punkt przerwania.

     - Przejdź do widoku debugowania Visual Studio Code, wybierając pozycję **wyświetl > Debuguj**. Wybierz konfigurację debugowania ** * &lt; nazwa &gt; modułu* debugowanie lokalne (Java)** z listy rozwijanej.

1. Kliknij przycisk **Rozpocznij debugowanie** lub naciśnij klawisz **F5** , aby rozpocząć sesję debugowania.

1. W Visual Studio Code zintegrowanym terminalu uruchom następujące polecenie, aby wysłać komunikat **Hello World** do modułu. Jest to polecenie przedstawione w poprzednich krokach podczas konfigurowania symulatora IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz systemu Windows, upewnij się, że powłoka Visual Studio Code zintegrowanym terminalem jest **git bash** lub **WSL bash**. Nie można uruchomić `curl` polecenia z programu PowerShell lub wiersza polecenia.
   > [!TIP]
   > Możesz również użyć narzędzia do [publikowania](https://www.getpostman.com/) lub innych narzędzi API do wysyłania wiadomości zamiast `curl` .

1. W widoku debugowanie Visual Studio Code widoczne są zmienne w lewym panelu.

1. Aby zatrzymać sesję debugowania, wybierz przycisk Zatrzymaj lub naciśnij klawisze **Shift + F5**, a następnie uruchom **Azure IoT Edge: Zatrzymaj IoT Edge symulator** w palecie poleceń, aby zatrzymać symulator i oczyścić go.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debugowanie w trybie dołączania za pomocą symulatora IoT Edge (C#, Node.js, Java, Azure Functions)

Twoje domyślne rozwiązanie zawiera dwa moduły — jeden to moduł symulowanej czujnika temperatury, a drugi to moduł potoku. Symulowany czujnik temperatury wysyła komunikaty do modułu potoku, a następnie komunikaty są potoku do IoT Hub. W utworzonym folderze modułu istnieje kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego z plików, które kończą się rozszerzeniem **. Debuguj** , aby skompilować moduł do testowania.

Obecnie debugowanie w trybie dołączania jest obsługiwane tylko w następujący sposób:

- Moduły języka C#, w tym te dla Azure Functions, obsługują debugowanie w kontenerach z systemem Linux amd64
- Moduły Node.js obsługują debugowanie w kontenerach z systemem Linux amd64 i arm32v7 oraz w kontenerach Windows amd64
- Moduły Java obsługują debugowanie w kontenerach systemu Linux amd64 i arm32v7

> [!TIP]
> Możesz przełączać się między opcjami domyślnej platformy dla rozwiązania IoT Edge, klikając element na Visual Studio Code pasku stanu.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla IoT Edge rozwiązania

Na komputerze deweloperskim można uruchomić symulator IoT Edge zamiast instalować demona IoT Edge Security, aby można było uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia IoT Edge, a następnie wybierz pozycję **instalator IoT Edge symulator** , aby uruchomić symulator z parametrami połączenia urządzenia.

1. Można sprawdzić, czy IoT Edge symulator został pomyślnie skonfigurowany, odczytując szczegóły postępu w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Kompilowanie i uruchamianie kontenera do debugowania i debugowania w trybie dołączania

1. Otwórz plik modułu ( `Program.cs` , `app.js` , `App.java` lub `<your module name>.cs` ) i Dodaj punkt przerwania.

1. W widoku Eksploratora Visual Studio Code kliknij prawym przyciskiem myszy `deployment.debug.template.json` plik rozwiązania, a następnie wybierz polecenie **Kompiluj i uruchom rozwiązanie IoT Edge w symulatorze**. Wszystkie dzienniki kontenerów modułów można obejrzeć w tym samym oknie. Możesz również przejść do widoku platformy Docker, aby obserwować stan kontenera.

   ![Obserwuj zmienne](media/how-to-vs-code-develop-module/view-log.png)

1. Przejdź do widoku debugowania Visual Studio Code i wybierz plik konfiguracyjny debugowania dla modułu. Nazwa opcji debugowania powinna być podobna do ** * &lt; nazwy &gt; modułu* debugowanie zdalne**

1. Wybierz pozycję **Rozpocznij debugowanie** lub naciśnij klawisz **F5**. Wybierz proces, do którego chcesz dołączyć.

1. W Visual Studio Code widoku debugowania widoczne są zmienne w lewym panelu.

1. Aby zatrzymać sesję debugowania, najpierw wybierz przycisk Zatrzymaj lub naciśnij klawisze **Shift + F5**, a następnie wybierz **Azure IoT Edge: Zatrzymaj IoT Edge symulator** z palety poleceń.

> [!NOTE]
> W powyższym przykładzie przedstawiono sposób debugowania modułów IoT Edge w kontenerach. Dodano dostępne porty do ustawień kontenera modułu `createOptions` . Po zakończeniu debugowania modułów zalecamy usunięcie tych dostępnych portów dla modułów IoT Edge gotowych do produkcji.
>
> W przypadku modułów pisanych w języku C#, w tym Azure Functions, ten przykład jest oparty na wersji debugowania programu `Dockerfile.amd64.debug` , która obejmuje Debuger wiersza polecenia platformy .NET Core (VSDBG) w obrazie kontenera podczas jego tworzenia. Po przeprowadzeniu debugowania modułów języka C# zalecamy bezpośrednie użycie pliku dockerfile bez VSDBG dla modułów IoT Edge gotowych do produkcji.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Debugowanie modułu przy użyciu środowiska uruchomieniowego IoT Edge

W każdym folderze modułów istnieje kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego z plików, które kończą się rozszerzeniem **. Debuguj** , aby skompilować moduł do testowania.

W przypadku debugowania modułów przy użyciu tej metody moduły są uruchamiane na podstawie IoT Edge środowiska uruchomieniowego. Urządzenie IoT Edge i Visual Studio Code mogą znajdować się na tym samym komputerze lub zwykle, Visual Studio Code znajduje się na komputerze deweloperskim, a moduły uruchomieniowe IoT Edge i działają na innym komputerze fizycznym. Aby debugować z Visual Studio Code, musisz:

- Skonfiguruj urządzenie IoT Edge, skompiluj moduły IoT Edge za pomocą narzędzia **. Debug** pliku dockerfile, a następnie wdróż je na urządzeniu IoT Edge.
- Uwidocznij adres IP i port modułu, aby można było dołączyć debuger.
- Zaktualizuj, `launch.json` aby Visual Studio Code mógł dołączyć do procesu w kontenerze na maszynie zdalnej. Ten plik znajduje się w `.vscode` folderze w obszarze roboczym i aktualizuje się za każdym razem, gdy dodajesz nowy moduł obsługujący debugowanie.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Kompilowanie i wdrażanie modułu na urządzeniu IoT Edge

1. W Visual Studio Code Otwórz `deployment.debug.template.json` plik, który zawiera wersję debugowaną obrazów modułu z `createOptions` ustawionymi prawidłowymi wartościami.

1. Jeśli tworzysz moduł w języku Python, wykonaj następujące czynności przed kontynuowaniem:
   - Otwórz plik `main.py` i Dodaj ten kod po sekcji import:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Dodaj następujący wiersz kodu do wywołania zwrotnego, które chcesz debugować:

      ```python
      ptvsd.break_into_debugger()
      ```

     Na przykład, jeśli chcesz debugować `receive_message_listener` funkcję, należy wstawić ten wiersz kodu, jak pokazano poniżej:

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

1. W palecie poleceń Visual Studio Code:
   1. Uruchom polecenie **Azure IoT Edge: Kompiluj i wypchnij rozwiązanie IoT Edge**.

   1. Wybierz `deployment.debug.template.json` plik rozwiązania.

1. W sekcji **urządzenia IoT Hub Azure** w widoku Eksploratora Visual Studio Code:
   1. Kliknij prawym przyciskiem myszy IoT Edge identyfikator urządzenia, a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

      > [!TIP]
      > Aby upewnić się, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie IoT Edge obejmuje te dwa moduły.

   1. Przejdź do folderu **konfiguracji** rozwiązania, wybierz `deployment.debug.amd64.json` plik, a następnie wybierz pozycję **Wybierz manifest wdrożenia Edge**.

Wdrożenie zostało pomyślnie utworzone przy użyciu identyfikatora wdrożenia w zintegrowanym terminalu.

Stan kontenera można sprawdzić, uruchamiając `docker ps` polecenie w terminalu. Jeśli Visual Studio Code i środowisko uruchomieniowe IoT Edge są uruchomione na tym samym komputerze, można również sprawdzić stan w widoku Visual Studio Code Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Uwidacznianie adresu IP i portu modułu debugera

Możesz pominąć tę sekcję, jeśli moduły są uruchomione na tym samym komputerze co Visual Studio Code, ponieważ używasz localhost do dołączania do kontenera i masz już poprawne ustawienia portu w pliku **. Debug** pliku dockerfile, ustawienia kontenera modułu `createOptions` i `launch.json` plik. Jeśli moduły i Visual Studio Code są uruchomione na oddzielnych maszynach, postępuj zgodnie z instrukcjami dla języka deweloperskiego.

- **C#, w tym Azure Functions**

  [Skonfiguruj kanał SSH na komputerze deweloperskim, a następnie IoT Edge urządzenie](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) , a następnie Edytuj `launch.json` plik do dołączenia.

- **Node.js**

  - Upewnij się, że moduł na komputerze, który ma być debugowany, jest uruchomiony i gotowy do debugera do dołączenia, a port 9229 jest dostępny zewnętrznie. Można to sprawdzić, otwierając `http://<target-machine-IP>:9229/json` na komputerze debugera. Ten adres URL powinien zawierać informacje o module Node.js, który ma być debugowany.
  
  - Na komputerze deweloperskim Otwórz Visual Studio Code a następnie Edytuj `launch.json` tak, aby wartość adresu w ** * &lt; &gt; * profilu debugowania zdalnego (Node.js)** (lub ** * &lt; &gt; Nazwa modułu* debugowania zdalnego (Node.js w kontenerze systemu Windows)** , jeśli moduł jest uruchomiony jako kontener systemu Windows), jest adresem IP debugowanej maszyny.

- **Java**

  - Skompiluj tunel SSH do maszyny w celu debugowania przez uruchomienie `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Na komputerze deweloperskim Otwórz Visual Studio Code i Zmień ** * &lt; nazwę &gt; modułu* Debugowanie kodu zdalnego (Java)** w programie, `launch.json` Aby umożliwić dołączenie do maszyny docelowej. Aby dowiedzieć się więcej na temat edytowania `launch.json` i debugowania środowiska Java za pomocą Visual Studio Code, zobacz sekcję dotyczącą [konfigurowania debugera](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Upewnij się, że port 5678 na komputerze, który ma być debugowany, jest otwarty i dostępny.

  - W kodzie, `ptvsd.enable_attach(('0.0.0.0', 5678))` który został wcześniej wstawiony `main.py` , Zmień wartość **0.0.0.0** na adres IP maszyny, która ma być debugowana. Kompiluj, wypchnij i Wdróż ponownie moduł IoT Edge.

  - Na komputerze deweloperskim Otwórz Visual Studio Code a następnie Edytuj `launch.json` tak, aby `host` wartość ** * &lt; &gt; Nazwa modułu* debugowanie zdalne debugowania (Python)** używa adresu IP maszyny docelowej zamiast `localhost` .

### <a name="debug-your-module"></a>Debugowanie modułu

1. W widoku debugowanie Visual Studio Code wybierz plik konfiguracyjny debugowania dla modułu. Nazwa opcji debugowania powinna być podobna do ** * &lt; nazwy &gt; modułu* debugowanie zdalne**

1. Otwórz plik modułu dla języka deweloperskiego i Dodaj punkt przerwania:

   - **Funkcja platformy Azure (C#)**: Dodaj punkt przerwania do pliku `<your module name>.cs` .
   - **C#**: Dodaj punkt przerwania do pliku `Program.cs` .
   - **Node.js**: Dodaj punkt przerwania do pliku `app.js` .
   - **Java**: Dodaj punkt przerwania do pliku `App.java` .
   - **Python**: Dodaj punkt przerwania do pliku `main.py` w metodzie wywołania zwrotnego, do której dodano `ptvsd.break_into_debugger()` wiersz.
   - **C**: Dodaj punkt przerwania do pliku `main.c` .

1. Wybierz pozycję **Rozpocznij debugowanie** lub wybierz pozycję **F5**. Wybierz proces, do którego chcesz dołączyć.

1. W widoku debugowanie Visual Studio Code widoczne są zmienne w lewym panelu.

> [!NOTE]
> W powyższym przykładzie przedstawiono sposób debugowania modułów IoT Edge w kontenerach. Dodano dostępne porty do ustawień kontenera modułu `createOptions` . Po zakończeniu debugowania modułów zalecamy usunięcie tych dostępnych portów dla modułów IoT Edge gotowych do produkcji.

## <a name="build-and-debug-a-module-remotely"></a>Zdalne Kompilowanie i debugowanie modułu

Dzięki najnowszym zmianom w aparatach Docker i Moby do obsługi połączeń SSH oraz nowym ustawieniu w narzędziach Azure IoT Tools, które umożliwiają wprowadzanie ustawień środowiska do Visual Studio Code palety poleceń i terminali Azure IoT Edge, można teraz kompilować i debugować moduły na urządzeniach zdalnych.

Aby uzyskać więcej informacji i instrukcje krok po kroku, zobacz ten [wpis na blogu dla deweloperów IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) .

## <a name="next-steps"></a>Następne kroki

Po skompilowaniu modułu należy dowiedzieć się, jak [wdrożyć moduły Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Aby opracowywać moduły dla urządzeń IoT Edge, należy zapoznać się z zestawami [sdk IoT Hub platformy Azure i korzystać](../iot-hub/iot-hub-devguide-sdks.md)z nich.