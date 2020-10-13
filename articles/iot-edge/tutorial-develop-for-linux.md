---
title: Samouczek — opracowywanie modułu dla urządzeń z systemem Linux przy użyciu Azure IoT Edge
description: Ten samouczek przeprowadzi Cię przez proces konfigurowania maszyn deweloperskich i zasobów w chmurze w celu opracowywania modułów IoT Edge przy użyciu kontenerów systemu Linux dla urządzeń z systemem Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 23ca4be9387754c84dc256dd72b131bd5b76b458
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876468"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Samouczek: opracowywanie modułów IoT Edge dla urządzeń z systemem Linux

Użyj Visual Studio Code, aby opracowywać i wdrażać kod na urządzeniach z systemem Linux, na których działa IoT Edge.

W tym przewodniku szybki start utworzono urządzenie IoT Edge przy użyciu maszyny wirtualnej z systemem Linux i wdrożono moduł z poziomu portalu Azure Marketplace. Ten samouczek przeprowadzi Cię przez proces tworzenia i wdrażania własnego kodu na urządzeniu IoT Edge. Ten artykuł jest przydatnym warunkiem wstępnym dla innych samouczków, które bardziej szczegółowo opisują określone Języki programowania lub usługi platformy Azure.

Ten samouczek używa przykładu wdrażania **modułu C# na urządzeniu z systemem Linux**. Ten przykład został wybrany, ponieważ jest to najbardziej typowy scenariusz dla deweloperów rozwiązań IoT Edge. Nawet jeśli planujesz użycie innego języka lub wdrożenie usługi platformy Azure, ten samouczek jest nadal przydatny do poznania się z narzędziami i pojęciami programistycznymi. Wypełnij to wprowadzenie do procesu tworzenia, a następnie wybierz preferowany język lub usługę platformy Azure, aby szczegółowe się ze szczegółami.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Skonfiguruj komputer deweloperski.
> * Użyj IoT Edge narzędzia do Visual Studio Code, aby utworzyć nowy projekt.
> * Skompiluj projekt jako kontener i Zapisz go w usłudze Azure Container Registry.
> * Wdróż swój kod na urządzeniu IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Kluczowe pojęcia

Ten samouczek przeprowadzi Cię przez proces tworzenia modułu IoT Edge. *Moduł IoT Edge*lub czasami tylko *moduł* for Short, jest kontenerem z kodem wykonywalnym. Można wdrożyć jeden lub więcej modułów na urządzeniu IoT Edge. Moduły wykonują określone zadania, takie jak pozyskiwanie danych z czujników, czyszczenie i analizowanie danych lub wysyłanie komunikatów do centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md).

Podczas opracowywania modułów IoT Edge należy zrozumieć różnicę między maszyną deweloperskią a docelowym urządzeniem IoT Edge, w którym moduł zostanie ostatecznie wdrożony. Kontener, który zostanie skompilowany do przechowywania kodu modułu, musi być zgodny z systemem operacyjnym (OS) *urządzenia docelowego*. Na przykład najbardziej typowym scenariuszem jest tworzenie modułu na komputerze z systemem Windows, który ma kierować do urządzenia z systemem Linux z IoT Edge. W takim przypadku system operacyjny kontenera to Linux. Korzystając z tego samouczka, należy pamiętać o różnicy między *systemem operacyjnym maszyny deweloperskiej* i *systemem operacyjnym kontenera*.

Ten samouczek dotyczy urządzeń z systemem Linux, na których działa IoT Edge. Możesz użyć preferowanego systemu operacyjnego, o ile na komputerze deweloperskim są uruchamiane kontenery systemu Linux. Zalecamy używanie Visual Studio Code do tworzenia aplikacji dla urządzeń z systemem Linux, co jest potrzebne w tym samouczku. Możesz również użyć programu Visual Studio, chociaż istnieją różnice w obsłudze między tymi dwoma narzędziami.

W poniższej tabeli przedstawiono obsługiwane scenariusze programowania dla **kontenerów systemu Linux** w Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Program Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura urządzeń z systemem Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Usługi platformy Azure** | Azure Functions <br> Usługa Azure Stream Analytics <br> Azure Machine Learning |   |
| **Języki** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Więcej informacji** | [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Narzędzia Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Narzędzia Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Obsługa urządzeń z systemem Linux ARM64 jest dostępna w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

W tym samouczku przedstawiono procedurę tworzenia Visual Studio Code. Jeśli wolisz korzystać z programu Visual Studio, zapoznaj się z instrukcjami w temacie [Korzystanie z programu Visual studio 2019 do tworzenia i debugowania modułów dla Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Wymagania wstępne

Komputer deweloperski:

* Możesz użyć własnego komputera lub maszyny wirtualnej, w zależności od preferencji programistycznych.
  * Upewnij się, że komputer deweloperski obsługuje wirtualizację zagnieżdżoną. Ta funkcja jest niezbędna do uruchomienia aparatu kontenera, który jest instalowany w następnej sekcji.
* Większość systemów operacyjnych, które mogą uruchamiać aparat kontenerów, może służyć do opracowania modułów IoT Edge dla urządzeń z systemem Linux. Ten samouczek używa komputera z systemem Windows, ale wskazuje na znane różnice w systemie macOS lub Linux.
* Zainstaluj narzędzie [git](https://git-scm.com/), aby ściągnąć pakiety szablonów modułów w dalszej części tego samouczka.  
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Urządzenie Azure IoT Edge w systemie Linux:

* Zalecamy, aby nie uruchamiać IoT Edge na komputerze deweloperskim, ale zamiast tego użyć oddzielnego urządzenia. Takie rozróżnienie między maszynami deweloperskimi i urządzeniami IoT Edge bardziej precyzyjnie odzwierciedla scenariusz rzeczywistego wdrożenia i pomaga zachować różne koncepcje w prosty sposób.
* Jeśli nie masz dostępnego drugiego urządzenia, Skorzystaj z artykułu Szybki Start, aby utworzyć urządzenie IoT Edge na platformie Azure z [maszyną wirtualną z systemem Linux](quickstart-linux.md).

Zasoby w chmurze:

* Bezpłatna lub Standardowa usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.

## <a name="install-container-engine"></a>Zainstaluj aparat kontenera

Moduły IoT Edge są spakowane jako kontenery, więc potrzebujesz aparatu kontenera na komputerze deweloperskim, aby kompilować i zarządzać nimi. Zalecamy, aby program Docker Desktop został rozbudowany ze względu na jego obsługę i popularność. Pulpit Docker w systemie Windows pozwala przełączać się między kontenerami systemu Linux i kontenerami z systemem Windows, dzięki czemu można łatwo opracowywać moduły dla różnych typów IoT Edge urządzeń.

Użyj dokumentacji platformy Docker, aby zainstalować program na komputerze deweloperskim:

* [Instalowanie programu Docker Desktop dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

  * Gdy instalujesz program Docker Desktop dla systemu Windows, zostanie wyświetlony monit z pytaniem, czy chcesz użyć kontenerów systemu Linux czy Windows. Tę decyzję można zmienić w dowolnym momencie przy użyciu prostego przełącznika. W tym samouczku używamy kontenerów systemu Linux, ponieważ moduły te są przeznaczone dla urządzeń z systemem Linux. Aby uzyskać więcej informacji, zobacz [przełączanie między kontenerami systemów Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Zainstaluj program Docker Desktop dla komputerów Mac](https://docs.docker.com/docker-for-mac/install/)

* Informacje [o instalacji programu Docker ce](https://docs.docker.com/install/) można znaleźć na kilku platformach systemu Linux.
  * W przypadku podsystemu Windows dla systemu Linux (WSL) Zainstaluj program Docker Desktop dla systemu Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurowanie VS Code i narzędzi

Użyj rozszerzeń IoT dla Visual Studio Code, aby opracowywać moduły IoT Edge. Te rozszerzenia zapewniają szablony projektu, automatyzują tworzenie manifestu wdrożenia i umożliwiają monitorowanie IoT Edge urządzeń i zarządzanie nimi. W tej sekcji zainstalujesz Visual Studio Code i rozszerzenie IoT, a następnie skonfigurujesz konto platformy Azure do zarządzania zasobami IoT Hub z poziomu Visual Studio Code.

1. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim.

2. Po zakończeniu instalacji wybierz pozycję **Wyświetl**  >  **rozszerzenia**.

3. Wyszukaj **narzędzia Azure IoT Tools**, które jest w rzeczywistości zbiorem rozszerzeń, które ułatwiają korzystanie z IoT Hub i urządzeń IoT, a także opracowywanie modułów IoT Edge.

4. Wybierz pozycję **Zainstaluj**. Wszystkie dołączone rozszerzenia są instalowane pojedynczo.

5. Po zakończeniu instalacji rozszerzeń, Otwórz paletę poleceń, wybierając pozycję **Widok**  >  **paleta poleceń**.

6. W palecie poleceń Wyszukaj i wybierz pozycję **Azure: Zaloguj się**. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

7. W palecie poleceń ponownie Wyszukaj i wybierz pozycję **Azure IoT Hub: wybierz pozycję IoT Hub**. Postępuj zgodnie z monitami, aby wybrać subskrypcję platformy Azure i usługę IoT Hub.

8. Otwórz sekcję Eksploratora Visual Studio Code, wybierając ikonę na pasku działania po lewej stronie lub wybierając pozycję **Widok**  >  **Eksplorator**.

9. W dolnej części sekcji Eksplorator rozwiń menu zwinięte **IoT Hub/urządzenia platformy Azure** . Powinny być widoczne urządzenia i IoT Edge urządzenia skojarzone z Centrum IoT Hub wybrane za pomocą palety poleceń.

   ![Wyświetlanie urządzeń w centrum IoT Hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Utwórz nowy projekt modułu

Rozszerzenie narzędzi Azure IoT Tools udostępnia szablony projektów dla wszystkich obsługiwanych języków modułu IoT Edge w Visual Studio Code. Te szablony mają wszystkie pliki i kod, które są potrzebne do wdrożenia modułu roboczego w celu przetestowania IoT Edge, lub umożliwiają rozpoczęcie dostosowywania szablonu przy użyciu własnej logiki biznesowej.

W tym samouczku używamy szablonu modułu języka C#, ponieważ jest to najczęściej używany szablon.

### <a name="create-a-project-template"></a>Utwórz szablon projektu

W Visual Studio Code palecie poleceń Wyszukaj i wybierz **Azure IoT Edge: nowe rozwiązanie IoT Edge**. Postępuj zgodnie z monitami i użyj następujących wartości, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Zaakceptuj domyślny **SampleModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Wartość serwera logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/sampleModule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-develop-for-linux/image-repository.png)

Po załadowaniu nowego rozwiązania w oknie Visual Studio Code Poświęć chwilę na zapoznanie się z utworzonymi plikami:

* Folder **. programu vscode** zawiera plik o nazwie **launch.json**, który jest używany na potrzeby debugowania modułów.
* Folder **modułów** zawiera folder dla każdego modułu w rozwiązaniu. Teraz, które powinny być **SampleModule**lub niezależnie od nazwy nadana do modułu. Folder SampleModule zawiera kod programu głównego, metadane modułu i kilka plików platformy Docker.
* Plik **ENV** zawiera poświadczenia do rejestru kontenerów. Te poświadczenia są udostępniane na urządzeniu IoT Edge, dzięki czemu mają dostęp do ściągania obrazów kontenerów.
* **deployment.debug.template.js** plików i **deployment.template.jsw** pliku to szablony, które pomagają utworzyć manifest wdrożenia. *Manifest wdrożenia* to plik, który definiuje dokładnie moduły, które mają zostać wdrożone na urządzeniu, jak powinny być skonfigurowane oraz jak mogą komunikować się ze sobą i chmurą. Pliki szablonów używają wskaźników dla niektórych wartości. Podczas przekształcania szablonu do rzeczywistego manifestu wdrażania wskaźniki są zastępowane wartościami pobranymi z innych plików rozwiązania. Znajdź dwa popularne symbole zastępcze w szablonie wdrożenia:

  * W sekcji poświadczenia rejestru adres jest wypełniany na podstawie informacji podanych podczas tworzenia rozwiązania. Jednak nazwa użytkownika i hasło odwołują się do zmiennych przechowywanych w pliku ENV. Ta konfiguracja dotyczy zabezpieczeń, ponieważ plik ENV został zignorowany przez Git, ale szablon wdrożenia nie jest.
  * W sekcji SampleModule obraz kontenera nie jest wypełniany, nawet jeśli podczas tworzenia rozwiązania podano repozytorium obrazów. Ten symbol zastępczy wskazuje **module.jsw** pliku w folderze SampleModule. Jeśli przejdziesz do tego pliku, zobaczysz, że pole obrazu zawiera repozytorium, ale również wartość tagu, która składa się z wersji i platformy kontenera. Wersję można iteracji ręcznie w ramach cyklu tworzenia oprogramowania i wybrać platformę kontenera przy użyciu przełącznika wprowadzonego w dalszej części tej sekcji.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj poświadczenia rejestru dla agenta IoT Edge

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągania obrazów kontenerów na urządzenie IoT Edge.

>[!NOTE]
>Jeśli wartość **localhost: 5000** nie została zastąpiona wartością serwera logowania z usługi Azure Container Registry, w kroku [**Tworzenie szablonu projektu**](#create-a-project-template) brakuje pliku **. env** i sekcji registryCredentials manifestu wdrażania. 

Rozszerzenie IoT Edge podejmuje próbę ściągnięcia poświadczeń rejestru kontenera z platformy Azure i wypełniania ich w pliku środowiska. Sprawdź, czy Twoje poświadczenia zostały już uwzględnione. Jeśli nie, Dodaj je teraz:

1. Otwórz plik **ENV** w rozwiązaniu modułu.
2. Dodaj wartości **nazwy użytkownika** i **hasła** , które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz zmiany w pliku ENV.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać moduły C# dla urządzeń z systemem Linux AMD64 i ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ ma to wpływ na sposób kompilowania i uruchamiania kontenera. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

   ![Wybierz ikonę architektury na pasku bocznym](./media/tutorial-develop-for-linux/select-architecture.png)

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**.

### <a name="review-the-sample-code"></a>Zapoznaj się z przykładowym kodem

Utworzony szablon rozwiązania zawiera przykładowy kod dla modułu IoT Edge. Ten przykładowy moduł po prostu odbiera komunikaty, a następnie przekazuje je. Funkcje potoku przedstawiają ważne koncepcje w IoT Edge, które są zgodne z tym, jak moduły komunikują się ze sobą.

Każdy moduł może mieć wiele kolejek *wejściowych* i *wyjściowych* zadeklarowanych w ich kodzie. Centrum IoT Edge uruchomione na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do danych wejściowych jednego lub kilku modułów. Konkretny kod służący do deklarowania danych wejściowych i wyjściowych różni się między językami, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [DECLARE Routes](module-composition.md#declare-routes).

Przykładowy kod w języku C#, który jest dostarczany z szablonem projektu, używa [klasy ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) z zestawu SDK IoT Hub dla platformy .NET.

1. Otwórz plik **program.cs** , który znajduje się w **module/SampleModule/** folder.

2. W program.cs Znajdź metodę **SetInputMessageHandlerAsync** .

3. Metoda [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) konfiguruje kolejkę wejściową do odbierania wiadomości przychodzących. Przejrzyj tę metodę i zobacz, jak inicjuje kolejkę wejściową o nazwie **INPUT1**.

   ![Znajdź nazwę wejściową w konstruktorze SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Następnie znajdź metodę **SendEventAsync** .

5. Metoda [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) przetwarza odebrane komunikaty i konfiguruje kolejkę wyjściową w celu przekazywania ich razem. Przejrzyj tę metodę i sprawdź, czy inicjuje kolejkę wyjściową o nazwie **output1**.

   ![Znajdź nazwę wyjściową w SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otwórz **deployment.template.js** pliku.

7. Znajdź właściwość **modules** $edgeAgent żądanych właściwości.

   W tym miejscu powinny znajdować się dwa moduły. Jednym z nich jest moduł **SimulatedTemperatureSensor** , który jest domyślnie zawarty we wszystkich szablonach w celu zapewnienia symulowanych danych temperatury, których można użyć do testowania modułów. Druga to moduł **SampleModule** , który został utworzony w ramach tego rozwiązania.

8. W dolnej części pliku Znajdź żądane właściwości modułu **$edgeHub** .

   Jedną z funkcji modułu IoT Edge Hub jest kierowanie komunikatów między wszystkimi modułami w ramach wdrożenia. Sprawdź wartości we właściwości **trasy** . Jedna trasa, **SampleModuleToIoTHub**, używa znaku wieloznacznego ( **\*** ), aby wskazać komunikaty pochodzące z dowolnych kolejek wyjściowych w module SampleModule. Te komunikaty przechodzą do *$upstream*, która jest nazwą zastrzeżoną, która wskazuje IoT Hub. Inna trasa, **sensorToSampleModule**, pobiera komunikaty pochodzące z modułu SimulatedTemperatureSensor i kieruje je do kolejki wejściowej *INPUT1* , która została zainicjowana w kodzie SampleModule.

   ![Przejrzyj trasy w deployment.template.jsna](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Sprawdzono kod modułu i szablon wdrożenia w celu zrozumienia pewnych koncepcji związanych z wdrażaniem. Teraz możesz utworzyć obraz kontenera SampleModule i wypchnąć go do rejestru kontenerów. Dzięki rozszerzeniu narzędzi IoT Tools dla Visual Studio Code ten krok generuje również manifest wdrożenia na podstawie informacji zawartych w pliku szablonu i informacje o module z plików rozwiązania.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Podaj poświadczenia rejestru kontenerów dla platformy Docker, aby można było wypchnąć obraz kontenera, który ma być przechowywany w rejestrze.

1. Otwórz Visual Studio Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **Terminal**.

2. Zaloguj się do platformy Docker przy użyciu poświadczeń usługi Azure Container Registry zapisanych po utworzeniu rejestru.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .
   
3. Logowanie w usłudze Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Kompiluj i wypchnij

Visual Studio Code teraz ma dostęp do rejestru kontenerów, dzięki czemu można przekształcić kod rozwiązania w obraz kontenera.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy **deployment.template.jsna** pliku i wybierz polecenie **Kompiluj i wypchnij IoT Edge rozwiązanie**.

   ![Kompilowanie i wypychanie modułów IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

2. Otwórz **deployment.amd64.js** pliku w nowo utworzonym folderze konfiguracyjnym. Nazwa pliku odzwierciedla architekturę docelową, dlatego będzie się różnić w przypadku wybrania innej architektury.

3. Należy zauważyć, że dwa parametry, które mają symbole zastępcze, są teraz wypełniane odpowiednimi wartościami. Sekcja **registryCredentials** zawiera nazwę użytkownika i hasło z pliku. env. **SampleModule** ma pełne repozytorium obrazu z nazwą, wersją i tagiem architektury z module.jsna pliku.

4. Otwórz **module.jsw** pliku w folderze SampleModule.

5. Zmień numer wersji obrazu modułu. (Wersja, a nie wersja $schema.) Na przykład Zwiększ numer wersji poprawki do **0.0.2** , ponieważ wprowadziliśmy małą poprawkę w kodzie modułu.

   >[!TIP]
   >Wersje modułów umożliwiają kontrolę wersji i umożliwiają testowanie zmian na małym zestawie urządzeń przed wdrożeniem aktualizacji w środowisku produkcyjnym. Jeśli wersja modułu nie zostanie zwiększona przed rozpoczęciem kompilowania i wypychania, należy zastąpić repozytorium w rejestrze kontenerów.

6. Zapisz zmiany w module.jspliku.

7. Ponownie kliknij prawym przyciskiem myszy **deployment.template.jsw** pliku i ponownie wybierz **rozwiązanie Kompiluj i wypchnij IoT Edge**.

8. Otwórz ponownie **deployment.amd64.js** pliku. Należy zauważyć, że nowy plik nie został utworzony po ponownym uruchomieniu polecenia Build i push. Zamiast tego ten sam plik został zaktualizowany w celu odzwierciedlenia zmian. Obraz SampleModule teraz wskazuje wersję 0.0.2 kontenera.

9. Aby dowiedzieć się więcej, jak wykonać polecenie Build i push, przejdź do [Azure Portal](https://portal.azure.com) i przejdź do rejestru kontenerów.

10. W rejestrze kontenerów wybierz pozycję **repozytoria** , a następnie **sampleModule**. Sprawdź, czy obie wersje obrazu zostały wypchnięte do rejestru.

    ![Wyświetl obie wersje obrazów w rejestrze kontenerów](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas kompilowania i wypychania obrazu modułu, często musimy to zrobić przy użyciu konfiguracji platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujących testów:

* Czy uruchomiono `docker login` polecenie przy użyciu poświadczeń skopiowanych z rejestru kontenerów? Te poświadczenia są inne niż te, które są używane do logowania się na platformie Azure.
* Czy używasz właściwego repozytorium kontenerów? Czy ma poprawną nazwę rejestru kontenerów i poprawną nazwę modułu? Otwórz **module.jsw** pliku w folderze SampleModule, aby sprawdzić. Wartość repozytorium powinna wyglądać jak ** \<registry name\> . azurecr.IO/sampleModule**.
* Jeśli użyto innej nazwy niż **SampleModule** dla modułu, czy nazwa jest spójna w całym rozwiązaniu?
* Czy na maszynie działa ten sam typ kontenerów, które są kompilowane? Ten samouczek dotyczy urządzeń z systemem Linux IoT Edge, więc Visual Studio Code powinien powiedzieć **amd64** lub **arm32v7** na pasku bocznym, a pulpit Docker powinien działać w kontenerach systemu Linux.  

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Sprawdzono, że skompilowane obrazy kontenerów są przechowywane w rejestrze kontenerów, dzięki czemu można wdrożyć je na urządzeniu. Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code w sekcji **IoT Hub platformy Azure** rozwiń węzeł **urządzenia** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy urządzenie IoT Edge, które chcesz wdrożyć, a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-linux/create-deployment.png)

3. W Eksploratorze plików przejdź do folderu **config** , a następnie wybierz **deployment.amd64.jsna** pliku.

   Nie należy używać deployment.template.jsw pliku, który nie ma poświadczeń rejestru kontenera ani wartości obrazu modułu. Jeśli celem jest urządzenie z systemem Linux ARM32, manifest wdrożenia będzie nazwany deployment.arm32v7.json.

4. W obszarze urządzenia rozwiń węzeł **moduły** , aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinny być widoczne nowe moduły SimulatedTemperatureSensor i SampleModule uruchomione na urządzeniu.

   Uruchomienie modułów może potrwać kilka minut. Środowisko uruchomieniowe IoT Edge musi otrzymać nowy manifest wdrożenia, ściągnąć obrazy modułów z środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

   ![Wyświetlanie modułów uruchomionych na urządzeniu IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Wyświetl komunikaty z urządzenia

Kod SampleModule odbiera wiadomości za pomocą swojej kolejki wejściowej i przekazuje je wraz z kolejką wyjściową. Manifest wdrożenia zadeklarowany trasy, które przekazały komunikaty do SampleModule z SimulatedTemperatureSensor, a następnie przesyłają dalej komunikaty z SampleModule do IoT Hub. Narzędzia Azure IoT Tools for Visual Studio Code umożliwiają wyświetlanie komunikatów, które docierają do IoT Hub z poszczególnych urządzeń.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy urządzenie IoT Edge, które chcesz monitorować, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Obejrzyj okno dane wyjściowe w Visual Studio Code, aby zobaczyć komunikaty docierające do centrum IoT.

   ![Wyświetlanie komunikatów przychodzących na urządzeniach w chmurze](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetl zmiany na urządzeniu

Jeśli chcesz zobaczyć, co się dzieje na urządzeniu, Użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe IoT Edge i moduły uruchomione na urządzeniu.

Polecenia w tej sekcji dotyczą urządzenia IoT Edge, a nie komputera deweloperskiego. Jeśli używasz maszyny wirtualnej dla urządzenia IoT Edge, Połącz się z nią teraz. Na platformie Azure przejdź do strony Przegląd maszyny wirtualnej i wybierz pozycję **Połącz** , aby uzyskać dostęp do połączenia Secure Shell.

* Wyświetlanie wszystkich modułów wdrożonych na urządzeniu i sprawdzanie ich stanu:

   ```bash
   iotedge list
   ```

   Powinny być widoczne cztery moduły: dwa IoT Edge moduły środowiska uruchomieniowego, SimulatedTemperatureSensor i SampleModule. Wszystkie cztery powinny być wymienione jako uruchomione.

* Inspekcja dzienników dla określonego modułu:

   ```bash
   iotedge logs <module name>
   ```

   W modułach IoT Edge jest rozróżniana wielkość liter.

   Dzienniki SimulatedTemperatureSensor i SampleModule powinny zawierać komunikaty, które są przetwarzane. Moduł edgeAgent jest odpowiedzialny za uruchamianie innych modułów, więc jego dzienniki zawierają informacje dotyczące implementowania manifestu wdrożenia. Jeśli którykolwiek moduł nie jest wyświetlany lub nie jest uruchomiony, dzienniki edgeAgent prawdopodobnie będą miały błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i IoT Hub. Jeśli moduły są uruchomione, ale komunikaty nie docierają do centrum IoT, dzienniki edgeHub prawdopodobnie będą miały błędy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz Visual Studio Code na swoim komputerze deweloperskim i wdrożono swój pierwszy moduł IoT Edge. Teraz, gdy znasz podstawowe pojęcia, spróbuj dodać funkcje do modułu, aby umożliwić przeanalizowanie danych. Wybierz preferowany język:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  Język [C#](tutorial-csharp-module.md) 
>  [Język Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  Język [Python](tutorial-python-module.md)
