---
title: Samouczek — opracowywanie modułu dla urządzeń z systemem Linux przy użyciu Azure IoT Edge
description: Ten samouczek zawiera instrukcji konfigurowania komputera dewelopera i zasobów w chmurze w celu opracowywania modułów IoT Edge przy użyciu kontenerów systemu Linux dla urządzeń z systemem Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 10742ec277f5742067c432a2823cbb7592e6a752
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874701"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Samouczek: tworzenie modułów IoT Edge za pomocą kontenerów systemu Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Użyj Visual Studio Code, aby opracowywać i wdrażać kod na urządzeniach z IoT Edge.

W tym przewodniku Szybki start utworzono IoT Edge i wdrożono moduł z Azure Marketplace. Ten samouczek zawiera instrukcji tworzenia i wdrażania własnego kodu na IoT Edge urządzeniu. Ten artykuł jest przydatnym wymaganiem wstępnym w przypadku innych samouczków, które bardziej szczegółowo opisano w określonych językach programowania lub usługach platformy Azure.

W tym samouczku użyto przykładu wdrażania modułu **języka C# na urządzeniu z systemem Linux.** Ten przykład został wybrany, ponieważ jest to najbardziej powszechny scenariusz dewelopera dla IoT Edge rozwiązań. Nawet jeśli zamierzasz używać innego języka lub wdrażać usługę platformy Azure, ten samouczek jest nadal przydatny do poznania narzędzi programisttycznych i pojęć. Ukończ to wprowadzenie do procesu programowania, a następnie wybierz preferowany język lub usługę platformy Azure, aby uzyskać szczegółowe informacje.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj maszynę dewelopera.
> * Użyj IoT Edge narzędzi Visual Studio Code, aby utworzyć nowy projekt.
> * Skompilowanie projektu jako kontenera i przechowywanie go w rejestrze kontenerów platformy Azure.
> * Wdrażanie kodu na urządzeniu IoT Edge aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Maszyna dewelopera:

* W zależności od preferencji programistyki możesz użyć własnego komputera lub maszyny wirtualnej.
  * Upewnij się, że maszyna deweloperka obsługuje wirtualizację zagnieżdżone. Ta funkcja jest niezbędna do uruchomienia aparatu kontenera, który zostanie zainstalowany w następnej sekcji.
* Większość systemów operacyjnych, w których można uruchomić aparat kontenera, może służyć do tworzenia modułów IoT Edge dla urządzeń z systemem Linux. Ten samouczek korzysta z komputera z systemem Windows, ale wskazuje znane różnice w systemie macOS lub Linux.
* Zainstaluj [usługę Git](https://git-scm.com/), aby ściągnąć pakiety szablonów modułów w dalszej części tego samouczka.  
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

Urządzenie usługi Azure IoT Edge:

* Zalecamy, aby nie uruchamiać aplikacji na IoT Edge dewelopera, ale używać oddzielnego urządzenia. To rozróżnienie między maszyną dewelopera IoT Edge urządzeniem dokładniej odzwierciedla prawdziwy scenariusz wdrażania i pomaga zachować proste pojęcia.
* Jeśli nie masz drugiego dostępnego urządzenia, skorzystaj z artykułu Szybki start, aby utworzyć urządzenie IoT Edge na platformie Azure za pomocą maszyny [wirtualnej z systemem Linux.](quickstart-linux.md)

Zasoby w chmurze:

* Usługa IoT Hub w warstwie [Bezpłatna lub Standardowa na](../iot-hub/iot-hub-create-through-portal.md) platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Kluczowe pojęcia

Ten samouczek zawiera informacje na temat opracowywania IoT Edge moduł. Moduł *IoT Edge*, a czasami *tylko* moduł w skrócie, jest kontenerem z kodem wykonywalnym. Możesz wdrożyć co najmniej jeden moduł na urządzeniu IoT Edge urządzeniu. Moduły wykonują określone zadania, takie jak pozyskowanie danych z czujników, czyszczenie i analizowanie danych lub wysyłanie komunikatów do centrum IoT. Aby uzyskać więcej informacji, zobacz Understand Azure IoT Edge modules (Informacje [na temat Azure IoT Edge modułów).](iot-edge-modules.md)

Podczas tworzenia IoT Edge modułach należy zrozumieć różnicę między maszyną dewelopera a docelowym urządzeniem IoT Edge, na którym moduł zostanie ostatecznie wdrożony. Kontener, który tworzysz w celu przechowywania kodu modułu, musi być zgodne z systemem operacyjnym urządzenia *docelowego.* Na przykład najbardziej powszechnym scenariuszem jest tworzenie modułu na komputerze z systemem Windows, który ma być urządzeniem z systemem Linux z systemem IoT Edge. W takim przypadku systemem operacyjnym kontenera będzie Linux. Podczas pracy z tym samouczkiem należy pamiętać o różnicy między systemami *operacyjnymi* maszyny dewelopera a *kontenerem systemu operacyjnego*.

>[!TIP]
>Jeśli używasz maszyny wirtualnej IoT Edge dla systemu Linux  w systemie [Windows,](iot-edge-for-linux-on-windows.md)urządzeniem docelowym w twoim scenariuszu jest maszyna wirtualna z systemem Linux, a nie host systemu Windows.

Ten samouczek dotyczy urządzeń z systemem IoT Edge z kontenerami systemu Linux. Preferowanego systemu operacyjnego można używać, o ile na maszynie deweloperowej są uruchamiane kontenery systemu Linux. Zalecamy korzystanie z Visual Studio Code do tworzenia aplikacji przy użyciu kontenerów systemu Linux, więc tego użyjemy w tym samouczku. Można również użyć Visual Studio, chociaż istnieją różnice w obsłudze między tymi dwoma narzędziami.

W poniższej tabeli wymieniono obsługiwane scenariusze testowania dla kontenerów **systemu Linux** w Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura urządzeń z systemem Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Usługi platformy Azure** | Azure Functions <br> Usługa Azure Stream Analytics <br> Azure Machine Learning |   |
| **Języki** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Więcej informacji** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools z Visual Studio 2017 r.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools z Visual Studio 2019 r.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Obsługa urządzeń z systemem Linux ARM64 jest dostępna w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać więcej informacji, zobacz [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Tworzenie](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)i debugowanie modułów arm64 w Visual Studio Code (wersja zapoznawcza).

Ten samouczek zawiera instrukcje tworzenia aplikacji dla Visual Studio Code. Jeśli wolisz używać programu Visual Studio, zapoznaj się z instrukcjami w tece Use [Visual Studio 2019 to develop and debug modules for Azure IoT Edge](how-to-visual-studio-develop-module.md)(Używanie programu Visual Studio 2019 do tworzenia i debugowania modułów dla programu Azure IoT Edge).
## <a name="install-container-engine"></a>Instalowanie aparatu kontenera

IoT Edge są spakowane jako kontenery, więc do ich skompilowania i zarządzania potrzebny jest aparat kontenerów na komputerze dewelopera. Zalecamy tworzenie aplikacji Docker Desktop ze względu na jej obsługę funkcji i popularność. Program Docker Desktop w systemie Windows umożliwia przełączanie się między kontenerami systemu Linux i kontenerami systemu Windows, dzięki czemu można łatwo tworzyć moduły dla różnych typów IoT Edge mobilnych.

Skorzystaj z dokumentacji platformy Docker, aby zainstalować ją na komputerze dewelopera:

* [Instalowanie programu Docker Desktop dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

  * Podczas instalowania programu Docker Desktop dla systemu Windows zostanie pytanie, czy chcesz używać kontenerów systemu Linux, czy Windows. Tę decyzję można zmienić w dowolnym momencie przy użyciu prostego przełącznika. W tym samouczku używamy kontenerów systemu Linux, ponieważ nasze moduły są przeznaczone dla urządzeń z systemem Linux. Aby uzyskać więcej informacji, zobacz [Przełączanie między kontenerami systemu Windows i Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

* [Instalowanie programu Docker Desktop dla komputerów Mac](https://docs.docker.com/docker-for-mac/install/)

* Przeczytaj [informacje o platformie Docker CE,](https://docs.docker.com/install/) aby uzyskać informacje o instalacji na kilku platformach Linux.
  * W przypadku Podsystem Windows dla systemu Linux (WSL) zainstaluj program Docker Desktop dla systemu Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurowanie VS Code i narzędzi

Użyj rozszerzeń IoT dla Visual Studio Code, aby tworzyć IoT Edge modułów. Te rozszerzenia zapewniają szablony projektów, automatyzują tworzenie manifestu wdrożenia oraz umożliwiają monitorowanie i zarządzanie IoT Edge wdrożeniami. W tej sekcji zainstalujesz Visual Studio Code i rozszerzenie IoT, a następnie skonfigurujesz konto platformy Azure do zarządzania zasobami IoT Hub z poziomu Visual Studio Code.

1. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/) na komputerze dewelopera.

2. Po zakończeniu instalacji wybierz pozycję **Wyświetl**  >  **rozszerzenia.**

3. Wyszukaj **Azure IoT Tools**, która w rzeczywistości jest kolekcją rozszerzeń, które ułatwiają interakcję z urządzeniami IoT Hub i IoT, a także tworzenie IoT Edge modułów.

4. Wybierz pozycję **Zainstaluj**. Każde dołączone rozszerzenie jest instalowane indywidualnie.

5. Po zainstalowaniu rozszerzeń otwórz paletę poleceń, wybierając pozycję **Wyświetl**  >  **paletę poleceń.**

6. W palecie poleceń wyszukaj i wybierz pozycję **Azure: Zaloguj się.** Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

7. Na palecie poleceń ponownie wyszukaj i wybierz pozycję **Azure IoT Hub: Wybierz IoT Hub**. Postępuj zgodnie z monitami, aby wybrać subskrypcję platformy Azure i centrum IoT.

8. Otwórz sekcję eksploratora Visual Studio Code, wybierając ikonę na pasku działań po lewej stronie lub wybierając pozycję **Wyświetl Eksplorator.**  >  

9. W dolnej części sekcji eksploratora rozwiń zwinięte **menu Azure IoT Hub / Urządzenia.** Powinny zostać wyświetlony urządzenia i IoT Edge skojarzone z centrum IoT, które wybrano za pomocą palety poleceń.

   ![Wyświetlanie urządzeń w centrum IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Tworzenie nowego projektu modułu

Rozszerzenie Azure IoT Tools udostępnia szablony projektów dla wszystkich obsługiwanych języków IoT Edge programowania w Visual Studio Code. Te szablony mają wszystkie pliki i kod, które są potrzebne do wdrożenia modułu roboczego w celu przetestowania IoT Edge, lub zapewniają punkt wyjścia do dostosowywania szablonu przy użyciu własnej logiki biznesowej.

W tym samouczku użyjemy szablonu modułu C#, ponieważ jest to najczęściej używany szablon.

### <a name="create-a-project-template"></a>Tworzenie szablonu projektu

W palecie Visual Studio Code poleceń wyszukaj i wybierz pozycję **Azure IoT Edge: Nowe IoT Edge rozwiązanie.** Postępuj zgodnie z monitami i użyj następujących wartości, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Zaakceptuj domyślny tryb **SampleModule.** |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp **wartość localhost:5000** wartością **serwera logowania** z rejestru kontenerów platformy Azure. Wartość Serwera logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<registry name\> .azurecr.io/samplemodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-develop-for-linux/image-repository.png)

Po załaduj nowe rozwiązanie w oknie Visual Studio Code, poświęć chwilę, aby zapoznać się z utworzonymi plikami:

* Folder **.vscode** zawiera plik o nazwie **launch.js,** który jest używany do debugowania modułów.
* Folder **modules** zawiera folder dla każdego modułu w rozwiązaniu. W tej chwili powinien to być tylko **moduł SampleModule** lub nazwa nadana modułowi. Folder SampleModule zawiera główny kod programu, metadane modułu i kilka plików platformy Docker.
* Plik **env** zawiera poświadczenia do rejestru kontenerów. Te poświadczenia są udostępniane urządzeniu IoT Edge, aby mieć dostęp do ściągania obrazów kontenerów.
* Szablon **deployment.debug.template.jsw pliku** ideployment.template.jsw **pliku** to szablony, które ułatwiają tworzenie manifestu wdrożenia. Manifest *wdrożenia to* plik, który definiuje dokładnie, które moduły mają być wdrażane na urządzeniu, jak powinny być skonfigurowane oraz jak mogą komunikować się ze sobą i chmurą. Pliki szablonów używają wskaźników dla niektórych wartości. Po przekształceniu szablonu w prawdziwy manifest wdrożenia wskaźniki są zastępowane wartościami z innych plików rozwiązania. Znajdź dwa typowe symbole zastępcze w szablonie wdrożenia:

  * W sekcji poświadczeń rejestru adres jest automatycznie wypełniany na podstawie informacji podanych podczas tworzenia rozwiązania. Jednak nazwa użytkownika i hasło odwołują się do zmiennych przechowywanych w pliku env. Ta konfiguracja jest ze względów bezpieczeństwa, ponieważ plik env jest ignorowany w usłudze Git, ale szablon wdrożenia nie jest.
  * W sekcji SampleModule obraz kontenera nie jest wypełniany, mimo że podczas tworzenia rozwiązania zostało podane repozytorium obrazów. Ten symbol zastępczy wskazuje **module.jsw pliku** w folderze SampleModule. Po dojściu do tego pliku zobaczysz, że pole obrazu zawiera repozytorium, ale także wartość tagu, która składa się z wersji i platformy kontenera. Możesz iterować wersję ręcznie w ramach cyklu projektowania i wybrać platformę kontenera przy użyciu przełącznika, który wprowadzimy w dalszej części tej sekcji.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj poświadczenia rejestru agentowi IoT Edge agenta

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe potrzebuje tych poświadczeń, aby ściągnąć obrazy kontenerów na IoT Edge urządzenia.

>[!NOTE]
>Jeśli wartość **localhost:5000** nie została zastąpiona wartością serwera logowania z rejestru [](#create-a-project-template) kontenerów platformy Azure, w kroku Tworzenie szablonu projektu brakuje pliku **env** i sekcji registryCredentials manifestu wdrożenia. 

Rozszerzenie IoT Edge próbuje ściągnąć poświadczenia rejestru kontenerów z platformy Azure i wypełnić je w pliku środowiska. Sprawdź, czy twoje poświadczenia są już dołączone. Jeśli nie, dodaj je teraz:

1. Otwórz plik **env** w rozwiązaniu modułu.
2. Dodaj wartości **nazwy użytkownika** **i** hasła skopiowane z rejestru kontenerów platformy Azure.
3. Zapisz zmiany w pliku env.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie można Visual Studio Code modułów języka C# dla urządzeń z systemami Linux AMD64 i ARM32v7. Należy wybrać architekturę, która ma być wybierana dla każdego rozwiązania, ponieważ ma to wpływ na sposób budowania i uruchamiania kontenera. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj pozycję **Azure IoT Edge:** Ustaw domyślną platformę docelową dla rozwiązania edge lub wybierz ikonę skrótu na pasku bocznym w dolnej części okna.

   ![Ikona wyboru architektury na pasku bocznym](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej z systemem Ubuntu jako urządzenia IoT Edge, dlatego zachowaj domyślną **wartość amd64.**

### <a name="review-the-sample-code"></a>Przegląd przykładowego kodu

Utworzony szablon rozwiązania zawiera przykładowy kod dla IoT Edge modułu. Ten przykładowy moduł po prostu odbiera komunikaty, a następnie przekazuje je dalej. Funkcja potoku demonstruje ważną koncepcję w IoT Edge, czyli sposób, w jaki moduły komunikują się ze sobą.

Każdy moduł może mieć wiele *kolejek* wejściowych *i* wyjściowych zadeklarowanych w kodzie. Centrum IoT Edge uruchomione na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do danych wejściowych jednego lub większej liczby modułów. Określony kod deklarowania danych wejściowych i wyjściowych różni się w zależności od języka, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [Deklarowanie tras](module-composition.md#declare-routes).

Przykładowy kod języka C# dostarczany z szablonem projektu używa klasy [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) z zestawu IOT HUB SDK dla platformy .NET.

1. Otwórz **plik Program.cs,** który znajduje się w **folderze modules/SampleModule/.**

2. W program.cs znajdź metodę **SetInputMessageHandlerAsync.**

3. Metoda [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) konfiguruje kolejkę wejściową w celu odbierania komunikatów przychodzących. Przejrzyj tę metodę i zobacz, jak inicjuje kolejkę wejściową o nazwie **input1**.

   ![Znajdź nazwę wejściową w konstruktorze SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Następnie znajdź metodę **SendEventAsync.**

5. Metoda [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) przetwarza komunikaty i konfiguruje kolejkę wyjściową w celu ich przekazania. Przejrzyj tę metodę i zobacz, że inicjuje kolejkę wyjściową o nazwie **output1**.

   ![Znajdowanie nazwy danych wyjściowych w pliku SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otwórz plik **deployment.template.jspliku.**

7. Znajdź **właściwość modules** $edgeAgent żądanych właściwości.

   W tym miejscu powinny być wymienione dwa moduły. Jednym z nich jest moduł **SimulatedTemperatureSensor,** który jest domyślnie uwzględniony we wszystkich szablonach w celu zapewnienia symulowanych danych temperatury, których można użyć do testowania modułów. Drugi to moduł **SampleModule** utworzony w ramach tego rozwiązania.

8. W dolnej części pliku znajdź żądane właściwości modułu **$edgeHub** danych.

   Jedną z funkcji modułu centrum IoT Edge jest rozsyłanie komunikatów między wszystkimi modułami we wdrożeniu. Przejrzyj wartości we właściwości **routes.** Jedna trasa, **SampleModuleToIoTHub,** używa symbolu wieloznacznego (* _), aby wskazać wszystkie komunikaty pochodzące z dowolnych kolejek wyjściowych w *\** module SampleModule. Te komunikaty są wysyłane do grupy _$upstream*, która jest zarezerwowaną nazwą, która IoT Hub. Druga trasa, **sensorToSampleModule,** przyjmuje komunikaty pochodzące z modułu SimulatedTemperatureSensor i kieruje je do kolejki wejściowej *input1,* która została zainicjowana w kodzie sampleModule.

   ![Przejrzyj trasy w deployment.template.jsna](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Przejrzeno kod modułu i szablon wdrożenia, aby zrozumieć niektóre kluczowe pojęcia związane z wdrażaniem. Teraz możesz skompilować obraz kontenera SampleModule i wypchnąć go do rejestru kontenerów. Dzięki rozszerzeniu narzędzi IoT dla usługi Visual Studio Code ten krok generuje również manifest wdrożenia na podstawie informacji w pliku szablonu i informacji o module z plików rozwiązania.

### <a name="sign-in-to-docker"></a>Logowanie do platformy Docker

Podaj poświadczenia rejestru kontenerów do platformy Docker, aby można było wypchnąć obraz kontenera do zapisania w rejestrze.

1. Otwórz Visual Studio Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **terminal.**

2. Zaloguj się do platformy Docker przy użyciu poświadczeń usługi Azure Container Registry, które zostały zapisane po utworzeniu rejestru.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlony ostrzeżenie o zabezpieczeniach z rekomendacją użycia polecenia `--password-stdin` . Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje [dotyczące logowania do platformy Docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)
   
3. Logowanie w usłudze Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Kompilowanie i wypychanie

Visual Studio Code ma teraz dostęp do rejestru kontenerów, więc czas przekształcić kod rozwiązania w obraz kontenera.

1. W eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy plik **deployment.template.js** a następnie wybierz polecenie **Skompilowanie** i wypychanie IoT Edge rozwiązania.

   ![Kompilowanie i wypychanie IoT Edge modułów](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Polecenie kompilowania i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera pełny manifest wdrożenia, zbudowany na podstawie informacji w szablonie wdrożenia i innych plikach rozwiązania. Po drugie jest uruchamiany w `docker build` celu skompilowania obrazu kontenera na podstawie pliku dockerfile odpowiedniego dla architektury docelowej. Następnie zostanie uruchomiony `docker push` program , aby wypchnąć repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut za pierwszym razem, ale jest szybszy przy następnym uruchomieniu poleceń.

2. Otwórz plik **deployment.amd64.jsw** nowo utworzonym folderze konfiguracji. Nazwa pliku odzwierciedla architekturę docelową, więc będzie ona inna w przypadku wybrania innej architektury.

3. Zwróć uwagę, że dwa parametry, które miały teraz symbole zastępcze, są wypełnione ich prawidłowymi wartościami. Sekcja **registryCredentials** zawiera nazwę użytkownika i hasło rejestru ściągnięte z pliku env. **SampleModule zawiera** pełne repozytorium obrazów z nazwą, wersją i tagiem architektury z pliku module.jspliku.

4. Otwórz plik **module.js** w folderze SampleModule.

5. Zmień numer wersji obrazu modułu. (Wersja, a nie $schema wersji). Na przykład należy zwiększyć numer wersji poprawki do **0.0.2,** tak jakby w kodzie modułu w kodzie modułu wdała się niewielka poprawka.

   >[!TIP]
   >Wersje modułów umożliwiają kontrolę wersji i umożliwiają testowanie zmian na małym zestawie urządzeń przed wdrożeniem aktualizacji w środowisku produkcyjnym. Jeśli nie zwiększasz wersji modułu przed sbudowania i wypychania, zastąp repozytorium w rejestrze kontenerów.

6. Zapisz zmiany w pliku module.jspliku.

7. Ponownie kliknij prawym przyciskiem **myszydeployment.template.jspliku,** a następnie ponownie wybierz pozycję Build and Push IoT Edge Solution (Kompilowanie i **wypychanie IoT Edge rozwiązania).**

8. Otwórz ponownie **deployment.amd64.jspliku.** Zwróć uwagę, że nowy plik nie został utworzony po tym, jak ponownie uruchomiono polecenie kompilacji i wypychania. Zamiast tego ten sam plik został zaktualizowany w celu odzwierciedlenia zmian. Obraz SampleModule wskazuje teraz na wersję 0.0.2 kontenera.

9. Aby dokładniej sprawdzić, co zrobiło polecenie kompilowania i wypychania, przejdź do Azure Portal [i](https://portal.azure.com) przejdź do rejestru kontenerów.

10. W rejestrze kontenerów wybierz pozycję **Repozytoria,** a następnie **samplemodule**. Sprawdź, czy obie wersje obrazu zostały wypchniętą do rejestru.

    ![Wyświetlanie obu wersji obrazów w rejestrze kontenerów](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas budowania i wypychania obrazu modułu, często ma to czynienia z konfiguracją platformy Docker na komputerze dewelopera. Aby przejrzeć konfigurację, użyj następujących testów:

* Czy polecenie było `docker login` uruchamiane przy użyciu poświadczeń skopiowanych z rejestru kontenerów? Te poświadczenia różnią się od tych, których używasz do logowania się do platformy Azure.
* Czy używasz właściwego repozytorium kontenerów? Czy ma poprawną nazwę rejestru kontenerów i poprawną nazwę modułu? Otwórz plik **module.jsw** folderze SampleModule, aby to sprawdzić. Wartość repozytorium powinna wyglądać jak **\<registry name\> .azurecr.io/samplemodule**.
* Jeśli dla modułu używasz innej nazwy niż **SampleModule,** czy ta nazwa jest spójna w całym rozwiązaniu?
* Czy na maszynie są uruchomione kontenery tego samego typu, które są budowania? Ten samouczek dotyczy urządzeń z systemem Linux IoT Edge, dlatego w programie Visual Studio Code na pasku bocznym powinno być powiedzmy **amd64** lub **arm32v7,** a w aplikacji Docker Desktop powinny być uruchomione kontenery systemu Linux.  

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Sprawdzono, że wbudowane obrazy kontenerów są przechowywane w rejestrze kontenerów, więc na razie należy wdrożyć je na urządzeniu. Upewnij się, IoT Edge urządzenie jest uruchomione.

1. W eksploratorze Visual Studio Code w sekcji **Azure IoT Hub** rozwiń listę  Urządzeń, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy IoT Edge, na którym chcesz wdrożyć aplikację, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia.**

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-linux/create-deployment.png)

3. W Eksploratorze plików przejdź do folderu **config,** a następnie wybierz **deployment.amd64.jspliku.**

   Nie należy używać deployment.template.jspliku, który nie zawiera poświadczeń rejestru kontenerów ani wartości obrazu modułu. W przypadku urządzenia ARM32 z systemem Linux manifest wdrożenia będzie miał nazwę deployment.arm32v7.jsna.

4. W obszarze urządzenia **rozwiń listę Modułów,** aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinny zostać uruchomione na urządzeniu nowe moduły SimulatedTemperatureSensor i SampleModule.

   Uruchomienie modułów może potrwać kilka minut. Środowisko IoT Edge musi odebrać nowy manifest wdrożenia, ściągnąć obrazy modułów ze środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

   ![Wyświetlanie modułów uruchomionych na IoT Edge urządzeniu](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Wyświetlanie komunikatów z urządzenia

Kod SampleModule odbiera komunikaty za pośrednictwem kolejki wejściowej i przekazuje je przez kolejkę wyjściową. Manifest wdrożenia zadeklarował trasy, które przekazywali komunikaty do samplemodule z simulatedTemperatureSensor, a następnie przekazywali komunikaty z sampleModule do IoT Hub. Narzędzia usługi Azure IoT dla Visual Studio Code umożliwiają wyświetlanie komunikatów odbieranych na IoT Hub z poszczególnych urządzeń.

1. W eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy urządzenie IoT Edge, które chcesz monitorować, a następnie wybierz pozycję Rozpocznij monitorowanie **wbudowanego punktu końcowego zdarzeń.**

2. Obejrzyj okno danych wyjściowych w Visual Studio Code, aby zobaczyć komunikaty przychodzące do centrum IoT.

   ![Wyświetlanie komunikatów przychodzących z urządzenia do chmury](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetlanie zmian na urządzeniu

Jeśli chcesz zobaczyć, co dzieje się na samym urządzeniu, użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe IoT Edge i moduły uruchomione na urządzeniu.

Polecenia w tej sekcji są dostępne dla IoT Edge, a nie dla komputera dewelopera. Jeśli używasz maszyny wirtualnej dla swojego IoT Edge, połącz się z nim teraz. Na platformie Azure przejdź do strony przeglądu maszyny wirtualnej i wybierz pozycję **Połącz,** aby uzyskać dostęp do połączenia z bezpieczną powłoką.

* Wyświetl wszystko modułów wdrożonych na urządzeniu i sprawdź ich stan:

   ```bash
   iotedge list
   ```

   Powinny zostać wyświetlony cztery moduły: dwa moduły IoT Edge uruchomieniowego, SimulatedTemperatureSensor i SampleModule. Wszystkie cztery powinny być wymienione jako uruchomione.

* Sprawdź dzienniki dla określonego modułu:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge w modułach jest wielkość liter.

   Dzienniki SimulatedTemperatureSensor i SampleModule powinny pokazywać przetwarzane komunikaty. Moduł edgeAgent jest odpowiedzialny za uruchamianie innych modułów, dlatego jego dzienniki będą zawierały informacje na temat implementowania manifestu wdrożenia. Jeśli żadnego modułu nie ma na liście lub nie jest uruchomiony, dzienniki edgeAgent prawdopodobnie będą mieć błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i IoT Hub. Jeśli moduły są uruchomione, ale komunikaty nie docierają do centrum IoT, dzienniki edgeHub prawdopodobnie będą mieć błędy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure użyte w tym artykule, aby uniknąć nalicznych opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujemy Visual Studio Code dewelopera i wdrożymy z niego pierwszy moduł IoT Edge aplikacji. Teraz, gdy znasz już podstawowe pojęcia, spróbuj dodać funkcje do modułu, aby można było analizować dane, które przez niego przepływają. Wybierz preferowany język:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Język Python](tutorial-python-module.md)
