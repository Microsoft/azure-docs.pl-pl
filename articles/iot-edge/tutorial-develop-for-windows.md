---
title: Samouczek — opracowywanie modułu dla urządzeń z systemem Windows przy użyciu Azure IoT Edge
description: Ten samouczek przeprowadzi Cię przez proces konfigurowania maszyn deweloperskich i zasobów w chmurze w celu opracowywania modułów IoT Edge przy użyciu kontenerów systemu Windows dla urządzeń z systemem Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e46105f5889f4925be9873fd8613021fe5e8ac2d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920761"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Samouczek: opracowywanie modułów IoT Edge dla urządzeń z systemem Windows

Program Visual Studio umożliwia tworzenie i wdrażanie kodu na urządzeniach z systemem Windows, na których działa IoT Edge.

W tym przewodniku szybki start utworzono urządzenie IoT Edge przy użyciu maszyny wirtualnej z systemem Windows i wdrożono wstępnie skompilowany moduł z portalu Azure Marketplace. Ten samouczek przeprowadzi Cię przez proces tworzenia i wdrażania własnego kodu na urządzeniu IoT Edge. Ten samouczek to przydatne wymagania wstępne dla innych samouczków, które bardziej szczegółowo opisują określone Języki programowania lub usługi platformy Azure.

Ten samouczek używa przykładu wdrażania **modułu C# na urządzeniu z systemem Windows**. Ten przykład został wybrany, ponieważ jest to najbardziej typowy scenariusz programistyczny. Jeśli interesuje Cię programowanie w innym języku lub Zaplanowanie wdrożenia usług platformy Azure jako modułów, ten samouczek nadal będzie przydatny do poznania narzędzi programistycznych. Po zrozumieniu koncepcji związanych z programowaniem możesz wybrać preferowany język lub usługę platformy Azure, aby szczegółowe się ze szczegółowymi informacjami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj komputer deweloperski.
> * Użyj narzędzi IoT Edge Tools for Visual Studio, aby utworzyć nowy projekt.
> * Skompiluj projekt jako kontener i Zapisz go w usłudze Azure Container Registry.
> * Wdróż swój kod na urządzeniu IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Komputer deweloperski:

* System Windows 10 z aktualizacją 1809 lub nowszą.
* Możesz użyć własnego komputera lub maszyny wirtualnej, w zależności od preferencji programistycznych.
  * Upewnij się, że komputer deweloperski obsługuje wirtualizację zagnieżdżoną. Ta funkcja jest niezbędna do uruchomienia aparatu kontenera, który jest instalowany w następnej sekcji.
* Zainstaluj narzędzie [git](https://git-scm.com/).

Urządzenie Azure IoT Edge w systemie Windows:

* Zalecamy, aby nie uruchamiać IoT Edge na komputerze deweloperskim, ale zamiast tego użyć oddzielnego urządzenia. Takie rozróżnienie między maszynami deweloperskimi i urządzeniami IoT Edge bardziej precyzyjnie odzwierciedla scenariusz rzeczywistego wdrożenia i pomaga zachować różne koncepcje w prosty sposób.
* Jeśli nie masz dostępnego drugiego urządzenia, Skorzystaj z artykułu Szybki Start, aby utworzyć urządzenie IoT Edge na platformie Azure z [maszyną wirtualną z systemem Windows](quickstart.md).

Zasoby w chmurze:

* Bezpłatna lub Standardowa usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Kluczowe pojęcia

Ten samouczek przeprowadzi Cię przez proces tworzenia modułu IoT Edge. *Moduł IoT Edge* lub czasami tylko *moduł* dla krótkich, jest kontenerem zawierającym kod wykonywalny. Można wdrożyć jeden lub więcej modułów na urządzeniu IoT Edge. Moduły wykonują określone zadania, takie jak pozyskiwanie danych z czujników, wykonywanie analiz danych lub operacje czyszczenia danych lub wysyłanie komunikatów do centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md).

Podczas opracowywania modułów IoT Edge należy zrozumieć różnicę między maszyną deweloperskią a docelowym urządzeniem IoT Edge, w którym moduł zostanie ostatecznie wdrożony. Kontener, który zostanie skompilowany do przechowywania kodu modułu, musi być zgodny z systemem operacyjnym (OS) *urządzenia docelowego*. W przypadku tworzenia kontenerów systemu Windows pojęcie to jest prostsze, ponieważ kontenery systemu Windows są uruchamiane tylko w systemach operacyjnych Windows. Możesz na przykład użyć komputera deweloperskiego z systemem Windows do kompilowania modułów dla urządzeń z systemem Linux IoT Edge. W tym scenariuszu należy upewnić się, że na komputerze deweloperskim działają kontenery systemu Linux. Korzystając z tego samouczka, należy pamiętać o różnicy między *systemem operacyjnym maszyny deweloperskiej* a *systemem operacyjnym kontenera*.

Ten samouczek dotyczy urządzeń z systemem Windows, na których działa IoT Edge. Urządzenia z systemem Windows IoT Edge używają kontenerów systemu Windows. Zalecamy korzystanie z programu Visual Studio do tworzenia aplikacji dla urządzeń z systemem Windows, co oznacza, że ten samouczek będzie używany. Możesz również użyć Visual Studio Code, chociaż istnieją różnice między tymi dwoma narzędziami.

W poniższej tabeli przedstawiono obsługiwane scenariusze programowania dla **kontenerów systemu Windows** w Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Program Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Usługi platformy Azure** | Azure Functions <br> Usługa Azure Stream Analytics |   |
| **Języki** | C# (debugowanie nie jest obsługiwane) | C <br> C# |
| **Więcej informacji** | [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Narzędzia Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Narzędzia Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>Zainstaluj aparat kontenera

Moduły IoT Edge są spakowane jako kontenery, więc potrzebujesz aparatu kontenera na komputerze deweloperskim do kompilowania kontenerów i zarządzania nimi. Zalecamy korzystanie z programu Docker Desktop na potrzeby programowania ze względu na wiele funkcji i popularność jako aparat kontenerów. Za pomocą programu Docker Desktop na komputerze z systemem Windows można przełączać się między kontenerami systemu Linux i kontenerami Windows, aby można było łatwo opracowywać moduły dla różnych typów IoT Edge urządzeń.

Użyj dokumentacji platformy Docker, aby zainstalować program na komputerze deweloperskim:

* [Instalowanie programu Docker Desktop dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

  * Gdy instalujesz program Docker Desktop dla systemu Windows, zostanie wyświetlony monit z pytaniem, czy chcesz użyć kontenerów systemu Linux czy Windows. W tym samouczku Użyj **kontenerów systemu Windows**. Aby uzyskać więcej informacji, zobacz [przełączanie między kontenerami systemów Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Konfigurowanie programu Visual Studio i narzędzi

Rozszerzenia IoT dla programu Visual Studio ułatwiają opracowywanie modułów IoT Edge. Te rozszerzenia zapewniają szablony projektu, automatyzują tworzenie manifestu wdrożenia i umożliwiają monitorowanie IoT Edge urządzeń i zarządzanie nimi. W tej sekcji zainstalujesz program Visual Studio i rozszerzenie IoT Edge, a następnie skonfigurujesz swoje konto platformy Azure do zarządzania zasobami IoT Hub z poziomu programu Visual Studio.

W tym samouczku przedstawiono kroki deweloperskie dla programu Visual Studio 2019. Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), kroki są podobne. Jeśli wolisz używać Visual Studio Code, zapoznaj się z instrukcjami w temacie [use Visual Studio Code, aby opracowywać i debugować moduły Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Przygotuj program Visual Studio 2019 na komputerze deweloperskim.

   * Jeśli nie masz jeszcze programu Visual Studio na komputerze deweloperskim, [Zainstaluj program Visual studio 2019](/visualstudio/install/install-visual-studio) z następującymi obciążeniami:

      * Tworzenie aplikacji na platformie Azure
      * Programowanie aplikacji klasycznych w języku C++
      * Programowanie dla wielu platform w środowisku .NET Core

   * Jeśli masz już program Visual Studio 2019 na komputerze deweloperskim, postępuj zgodnie z instrukcjami w sekcji [modyfikowanie programu Visual Studio](/visualstudio/install/modify-visual-studio) , aby dodać wymagane obciążenia.

2. Pobierz i zainstaluj rozszerzenie [narzędzi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) dla programu Visual Studio 2019.

   Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), Pobierz i zainstaluj [narzędzia Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Po zakończeniu instalacji Otwórz program Visual Studio 2019 i wybierz pozycję **Kontynuuj bez kodu**.

4. Wybierz pozycję **Wyświetl**  >  **Eksplorator chmury**.

5. Wybierz ikonę profilu w Eksploratorze chmury i zaloguj się do konta platformy Azure, jeśli jeszcze nie zalogowano się.

6. Po zalogowaniu się zostaną wyświetlone subskrypcje platformy Azure. Rozwiń subskrypcję zawierającą Centrum IoT Hub.

7. W ramach subskrypcji rozwiń węzeł usługi **IoT** Hub, a następnie Centrum IoT. Powinna zostać wyświetlona lista urządzeń IoT i można użyć tego Eksploratora do zarządzania nimi.

   ![Dostęp do zasobów IoT Hub w programie Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Utwórz nowy projekt modułu

Rozszerzenie narzędzi Azure IoT Edge zawiera szablony projektów dla wszystkich obsługiwanych języków modułu IoT Edge w programie Visual Studio. Te szablony mają wszystkie pliki i kod, które są potrzebne do wdrożenia modułu roboczego w celu przetestowania IoT Edge, lub umożliwiają rozpoczęcie dostosowywania szablonu przy użyciu własnej logiki biznesowej.

1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt..** .

2. W oknie Nowy projekt Wyszukaj **IoT Edge** a następnie wybierz projekt **Azure IoT Edge (Windows amd64)** . Kliknij przycisk **Dalej**.

   ![Utwórz nowy projekt Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. W oknie Konfigurowanie nowego projektu Zmień nazwę projektu i rozwiązania na coś, takiego jak **CSharpTutorialApp**. Kliknij przycisk **Utwórz** , aby utworzyć projekt.

   ![Skonfiguruj nowy projekt Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. W oknie Dodawanie modułu Skonfiguruj projekt przy użyciu następujących wartości:

   | Pole | Wartość |
   | ----- | ----- |
   | Szablon programu Visual Studio | Wybierz **moduł C#**. |
   | Nazwa modułu | Zaakceptuj domyślny **IotEdgeModule1**. |
   | Adres URL repozytorium | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie wartości nazwy projektu modułu. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Wartość serwera logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/iotedgemodule1. |

      ![Skonfiguruj projekt dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Wybierz pozycję **Dodaj** , aby utworzyć moduł.

Po załadowaniu nowego projektu w oknie programu Visual Studio Poświęć chwilę na zapoznanie się z utworzonymi plikami:

* Projekt IoT Edge o nazwie **CSharpTutorialApp**.
  * Folder **modułów** zawiera wskaźniki do modułów zawartych w projekcie. W takim przypadku powinna to być tylko IotEdgeModule1.
  * Ukryty plik **ENV** zawiera poświadczenia do rejestru kontenerów. Te poświadczenia są udostępniane na urządzeniu IoT Edge, dzięki czemu mają dostęp do ściągania obrazów kontenerów.
  * **deployment.template.jsw** pliku to szablon ułatwiający utworzenie manifestu wdrożenia. *Manifest wdrożenia* to plik, który definiuje dokładnie moduły, które mają zostać wdrożone na urządzeniu, jak powinny być skonfigurowane oraz jak mogą komunikować się ze sobą i chmurą.
    > [!TIP]
    > W sekcji poświadczenia rejestru adres jest wypełniany na podstawie informacji podanych podczas tworzenia rozwiązania. Jednak zmienne odwołania nazwy użytkownika i hasła przechowywane w pliku ENV. Jest to związane z bezpieczeństwem, ponieważ plik ENV został zignorowany przez Git, ale szablon wdrożenia nie jest.
* Projekt modułu IoT Edge o nazwie **IotEdgeModule1**.
  * Plik **program.cs** zawiera domyślny kod modułu C#, który jest dostarczany z szablonem projektu. Moduł domyślny pobiera dane wejściowe ze źródła i przekazuje je do IoT Hub.
  * **module.jsw** pliku zawiera szczegółowe informacje o module, w tym pełne repozytorium obrazów, wersję obrazu i pliku dockerfile do użycia dla każdej z obsługiwanych platform.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj poświadczenia rejestru dla agenta IoT Edge

Środowisko uruchomieniowe IoT Edge wymaga poświadczeń rejestru do ściągania obrazów kontenera na urządzenie IoT Edge. Rozszerzenie IoT Edge próbuje ściągnąć informacje rejestru kontenera z platformy Azure i wypełnić je szablonem wdrożenia.

1. Otwórz **deployment.template.jsw** pliku w rozwiązaniu modułu.

1. Znajdź właściwość **registryCredentials** w $edgeAgent żądanych właściwościach. Powinien mieć adres rejestru autowypełniany na podstawie informacji podanych podczas tworzenia projektu, a następnie pola username i Password powinny zawierać nazwy zmiennych. Na przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otwórz plik **ENV** w rozwiązaniu modułu. (Jest ona domyślnie ukryta w Eksplorator rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki** , aby go wyświetlić).

1. Dodaj wartości **nazwy użytkownika** i **hasła** , które zostały skopiowane z usługi Azure Container Registry.

1. Zapisz zmiany w pliku ENV.

### <a name="review-the-sample-code"></a>Zapoznaj się z przykładowym kodem

Utworzony szablon rozwiązania zawiera przykładowy kod dla modułu IoT Edge. Ten przykładowy moduł po prostu odbiera komunikaty, a następnie przekazuje je. Funkcje potoku przedstawiają ważne koncepcje w IoT Edge, które są zgodne z tym, jak moduły komunikują się ze sobą.

Każdy moduł może mieć wiele kolejek *wejściowych* i *wyjściowych* zadeklarowanych w ich kodzie. Centrum IoT Edge uruchomione na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do danych wejściowych jednego lub kilku modułów. Konkretny kod służący do deklarowania danych wejściowych i wyjściowych różni się między językami, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [DECLARE Routes](module-composition.md#declare-routes).

Przykładowy kod w języku C#, który jest dostarczany z szablonem projektu, używa [klasy ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) z zestawu SDK IoT Hub dla platformy .NET.

1. W pliku **program.cs** Znajdź metodę **SetInputMessageHandlerAsync** .

2. Metoda [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) konfiguruje kolejkę wejściową do odbierania wiadomości przychodzących. Przejrzyj tę metodę i zobacz, jak inicjuje kolejkę wejściową o nazwie **INPUT1**.

   ![Znajdź nazwę wejściową w konstruktorze SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Następnie znajdź metodę **SendEventAsync** .

4. Metoda [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) przetwarza odebrane komunikaty i konfiguruje kolejkę wyjściową w celu przekazywania ich razem. Przejrzyj tę metodę i sprawdź, czy inicjuje kolejkę wyjściową o nazwie **output1**.

   ![Znajdź nazwę wyjściową w konstruktorze SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Otwórz **deployment.template.js** pliku.

6. Znajdź właściwość **modules** $edgeAgent żądanych właściwości.

   W tym miejscu powinny znajdować się dwa moduły. Jednym z nich jest moduł **SimulatedTemperatureSensor** , który jest domyślnie zawarty we wszystkich szablonach w celu zapewnienia symulowanych danych temperatury, których można użyć do testowania modułów. Druga to moduł **IotEdgeModule1** , który został utworzony w ramach tego projektu.

   Ta właściwość modułów deklaruje, które moduły mają być uwzględnione we wdrożeniu na urządzeniu lub urządzeniach.

7. Znajdź właściwość **routes** $edgeHub żądanych właściwości.

   Jedną z funkcji modułu IoT Edge Hub jest kierowanie komunikatów między wszystkimi modułami w ramach wdrożenia. Sprawdź wartości we właściwości trasy. Jeden z tras, **IotEdgeModule1ToIoTHub**, używa symbolu wieloznacznego (* *\** _) do uwzględnienia komunikatu przychodzącego z dowolnej kolejki wyjściowej w module IotEdgeModule1. Te komunikaty przechodzą do _ $ $ strumienia *, która jest nazwą zastrzeżoną, która wskazuje IoT Hub. Inna trasa, **sensorToIotEdgeModule1**, pobiera komunikaty pochodzące z modułu SimulatedTemperatureSensor i kieruje je do kolejki wejściowej *INPUT1* modułu IotEdgeModule1.

   ![Przejrzyj trasy w deployment.template.jsna](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Sprawdzono kod modułu i szablon wdrożenia w celu zrozumienia pewnych koncepcji związanych z wdrażaniem. Teraz możesz utworzyć obraz kontenera IotEdgeModule1 i wypchnąć go do rejestru kontenerów. Dzięki rozszerzeniu narzędzi IoT Tools dla programu Visual Studio ten krok generuje również manifest wdrożenia na podstawie informacji zawartych w pliku szablonu i informacje o module z plików rozwiązania.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Podaj poświadczenia rejestru kontenerów platformy Docker na komputerze deweloperskim, aby można było wypchnąć obraz kontenera do zapisania w rejestrze.

1. Otwórz program PowerShell lub wiersz polecenia.

2. Zaloguj się do platformy Docker przy użyciu poświadczeń usługi Azure Container Registry zapisanych po utworzeniu rejestru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Kompiluj i wypchnij

Komputer deweloperski ma teraz dostęp do rejestru kontenerów, a Twoje urządzenia IoT Edge również. Czas, aby przekształcić kod projektu w obraz kontenera.

1. Kliknij prawym przyciskiem myszy folder projektu **CSharpTutorialApp** , a następnie wybierz pozycję **kompilacje i wypychanie modułów IoT Edge**.

   ![Kompilowanie i wypychanie modułów IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

2. Otwórz **deployment.windows-amd64.js** pliku w nowo utworzonym folderze konfiguracyjnym. (Folder konfiguracji może nie być wyświetlany w Eksplorator rozwiązań w programie Visual Studio. W takim przypadku wybierz ikonę **Pokaż wszystkie pliki** na pasku zadań Eksplorator rozwiązań.)

3. Znajdź parametr **Image** w sekcji IotEdgeModule1. Zauważ, że obraz zawiera pełne repozytorium obrazu z nazwą, wersją i tagiem architektury z module.jsw pliku.

4. Otwórz **module.jsw** pliku w folderze IotEdgeModule1.

5. Zmień numer wersji obrazu modułu. (Wersja, a nie wersja $schema.) Na przykład Zwiększ numer wersji poprawki do **0.0.2** , ponieważ wprowadziliśmy małą poprawkę w kodzie modułu.

   >[!TIP]
   >Wersje modułów umożliwiają kontrolę wersji i umożliwiają testowanie zmian na małym zestawie urządzeń przed wdrożeniem aktualizacji w środowisku produkcyjnym. Jeśli wersja modułu nie zostanie zwiększona przed rozpoczęciem kompilowania i wypychania, należy zastąpić repozytorium w rejestrze kontenerów.

6. Zapisz zmiany w module.jspliku.

7. Ponownie kliknij prawym przyciskiem myszy folder projektu **CSharpTutorialApp** , a następnie wybierz ponownie polecenie **Kompiluj i wypchnij moduły IoT Edge** .

8. Otwórz ponownie **deployment.windows-amd64.js** pliku. Należy zauważyć, że nowy plik nie został utworzony po ponownym uruchomieniu polecenia Build i push. Zamiast tego ten sam plik został zaktualizowany w celu odzwierciedlenia zmian. Obraz IotEdgeModule1 teraz wskazuje wersję 0.0.2 kontenera. Ta zmiana w manifeście wdrożenia ma na celu poinformowanie o urządzeniu IoT Edge, że jest dostępna nowa wersja modułu do ściągnięcia.

9. Aby dowiedzieć się więcej, jak wykonać polecenie Build i push, przejdź do [Azure Portal](https://portal.azure.com) i przejdź do rejestru kontenerów.

10. W rejestrze kontenerów wybierz pozycję **repozytoria** , a następnie **iotedgemodule1**. Sprawdź, czy obie wersje obrazu zostały wypchnięte do rejestru.

    ![Wyświetl obie wersje obrazów w rejestrze kontenerów](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas kompilowania i wypychania obrazu modułu, często musimy to zrobić przy użyciu konfiguracji platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujących testów:

* Czy uruchomiono `docker login` polecenie przy użyciu poświadczeń skopiowanych z rejestru kontenerów? Te poświadczenia są inne niż te, które są używane do logowania się na platformie Azure.
* Czy używasz właściwego repozytorium kontenerów? Czy ma poprawną nazwę rejestru kontenerów i poprawną nazwę modułu? Otwórz **module.jsw** pliku w folderze IotEdgeModule1, aby sprawdzić. Wartość repozytorium powinna wyglądać jak **\<registry name\> . azurecr.IO/iotedgemodule1**.
* Jeśli użyto innej nazwy niż **IotEdgeModule1** dla modułu, czy nazwa jest spójna w całym rozwiązaniu?
* Czy na maszynie działa ten sam typ kontenerów, które są kompilowane? Ten samouczek dotyczy urządzeń z systemem Windows IoT Edge, więc pliki programu Visual Studio powinny mieć rozszerzenie **Windows-amd64** , a na pulpicie platformy Docker powinny być uruchomione kontenery systemu Windows.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Sprawdzono, że skompilowane obrazy kontenerów są przechowywane w rejestrze kontenerów, dzięki czemu można wdrożyć je na urządzeniu. Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. Otwórz program Cloud Explorer w programie Visual Studio i rozwiń Szczegóły dotyczące centrum IoT.

2. Wybierz nazwę urządzenia, które chcesz wdrożyć. Na liście **Akcje** wybierz pozycję **Utwórz wdrożenie**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-windows/create-deployment.png)

3. W Eksploratorze plików przejdź do folderu config projektu i wybierz **deployment.windows-amd64.jsw** pliku. Ten plik często znajduje się w lokalizacji `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Nie należy używać deployment.template.jsw pliku, który nie ma pełnych wartości obrazu modułu.

4. Rozwiń szczegóły urządzenia IoT Edge w programie Cloud Explorer, aby wyświetlić moduły na urządzeniu.

5. Użyj przycisku **Odśwież** , aby zaktualizować stan urządzenia, aby zobaczyć, że moduły SimulatedTemperatureSensor i IotEdgeModule1 zostały wdrożone na urządzeniu.

   ![Wyświetlanie modułów uruchomionych na urządzeniu IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Wyświetl komunikaty z urządzenia

Kod IotEdgeModule1 odbiera wiadomości za pomocą swojej kolejki wejściowej i przekazuje je wraz z kolejką wyjściową. Manifest wdrożenia zadeklarowany tras, które przekazały komunikaty z SimulatedTemperatureSensor do IotEdgeModule1, a następnie przesyłają dalej komunikaty z IotEdgeModule1 do IoT Hub. Narzędzia Azure IoT Edge Tools for Visual Studio umożliwiają wyświetlanie komunikatów, które docierają do IoT Hub z poszczególnych urządzeń.

1. W programie Visual Studio Cloud Explorer wybierz nazwę urządzenia IoT Edge, które zostało wdrożone.

2. W menu **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Obejrzyj sekcję **dane wyjściowe** w programie Visual Studio, aby wyświetlić komunikaty docierające do centrum IoT.

   Uruchomienie obu modułów może potrwać kilka minut. Środowisko uruchomieniowe IoT Edge musi otrzymać nowy manifest wdrożenia, ściągnąć obrazy modułów z środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

   ![Wyświetlanie komunikatów przychodzących na urządzeniach w chmurze](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetl zmiany na urządzeniu

Jeśli chcesz zobaczyć, co się dzieje na urządzeniu, Użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe IoT Edge i moduły uruchomione na urządzeniu.

Polecenia w tej sekcji dotyczą urządzenia IoT Edge, a nie komputera deweloperskiego. Jeśli używasz maszyny wirtualnej dla urządzenia IoT Edge, Połącz się z nią teraz. Na platformie Azure przejdź do strony Przegląd maszyny wirtualnej i wybierz pozycję **Połącz** , aby uzyskać dostęp do połączenia pulpitu zdalnego. Na urządzeniu Otwórz polecenie lub okno programu PowerShell, aby uruchomić `iotedge` polecenia.

* Wyświetlanie wszystkich modułów wdrożonych na urządzeniu i sprawdzanie ich stanu:

   ```cmd
   iotedge list
   ```

   Powinny być widoczne cztery moduły: dwa IoT Edge moduły środowiska uruchomieniowego, SimulatedTemperatureSensor i IotEdgeModule1. Wszystkie cztery powinny być wymienione jako uruchomione.

* Inspekcja dzienników dla określonego modułu:

   ```cmd
   iotedge logs <module name>
   ```

   W modułach IoT Edge jest rozróżniana wielkość liter.

   Dzienniki SimulatedTemperatureSensor i IotEdgeModule1 powinny zawierać komunikaty, które są przetwarzane. Moduł edgeAgent jest odpowiedzialny za uruchamianie innych modułów, więc jego dzienniki zawierają informacje dotyczące implementowania manifestu wdrożenia. Jeśli którykolwiek moduł nie jest wyświetlany lub nie jest uruchomiony, dzienniki edgeAgent prawdopodobnie będą miały błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i IoT Hub. Jeśli moduły są uruchomione, ale komunikaty nie docierają do centrum IoT, dzienniki edgeHub prawdopodobnie będą miały błędy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz program Visual Studio 2019 na komputerze deweloperskim i wdrożono pierwszy moduł IoT Edge. Teraz, gdy znasz podstawowe pojęcia, spróbuj dodać funkcje do modułu, aby umożliwić przeanalizowanie danych. Wybierz preferowany język:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md) 
>  Język [C#](tutorial-csharp-module-windows.md)