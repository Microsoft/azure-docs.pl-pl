---
title: Samouczek opracowywania modułu Node. js dla systemu Linux — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu Node.js i wdrożyć go na urządzeniu brzegowym
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, tracking-python
ms.openlocfilehash: 7e17da94ba124c3b20fdede93ad6b4716247c6ba
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610121"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Samouczek: Tworzenie i wdrażanie modułu IoT Edge Node. js dla urządzeń z systemem Linux

Użyj Visual Studio Code, aby opracować kod Node. js i wdrożyć go na urządzeniu z systemem Linux z Azure IoT Edge.

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego w ramach przewodnika Szybki start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku przedstawiono sposób tworzenia modułu w programie **Node. js** przy użyciu **Visual Studio Code**i sposobu wdrażania go na **urządzeniu z systemem Linux**. IoT Edge nie obsługuje modułów Node. js dla urządzeń z systemem Windows.

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów Node. js:

| Node.js | Visual Studio Code | Program Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Używanie VS Code dla modułów Node. js w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Używanie VS Code dla modułów Node. js w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Linux: [Tworzenie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując jeden z tych samouczków, należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

Aby utworzyć moduł IoT Edge w programie Node. js, Zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [Node. js i npm](https://nodejs.org). Pakiet npm jest dystrybuowany wraz ze środowiskiem Node.js, co oznacza, że podczas pobierania środowiska Node.js program npm jest automatycznie instalowany na komputerze.

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge w środowisku Node.js przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Użyj programu **npm**, aby utworzyć szablon rozwiązania Node.js, na podstawie którego można dalej tworzyć rozwiązanie.

1. W Visual Studio Code wybierz pozycję **Wyświetl**  >  **zintegrowany terminal** , aby otworzyć vs Code zintegrowany terminal.

2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować narzędzie **yeoman** oraz generator dla modułu Node.js usługi Azure IoT Edge:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Wybierz pozycję **Widok**  >  **paleta poleceń** , aby otworzyć paletę poleceń vs Code.

4. W palecie poleceń wpisz i uruchom polecenie **Azure: zaloguj**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

5. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Node.js Module**. |
   | Podaj nazwę modułu | Nazwij moduł **NodeModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/nodemodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia repozytorium kontenera, udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik **env**.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać Moduły Node. js dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Każdy szablon zawiera przykładowy dołączony kod, który wykonuje symulowane dane czujników z modułu **SimulatedTemperatureSensor** i kieruje go do IoT Hub. W tej sekcji dodasz kod, aby umożliwić modułowi NodeModule analizowanie komunikatów przed ich wysłaniem.

1. W Eksploratorze vs Code Otwórz **moduł modules**  >  **NodeModule**  >  **App. js**.

2. Dodaj zmienną „temperature_threshold” poniżej wymaganych modułów węzła. Zmienna „temperature_threshold” określa wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Zastąp całą funkcję `PipeMessage` funkcją `FilterMessage`.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Zastąp nazwę funkcji `pipeMessage` nazwą `filterMessage` w funkcji `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Skopiuj poniższy fragment kodu do wywołania funkcji `client.open()`, po funkcji `client.on()` wewnątrz instrukcji `else`. Ta funkcja jest wywoływana w przypadku zaktualizowania odpowiednich właściwości.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Zapisz plik app.js.

7. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

8. Dodaj bliźniaczą reprezentację modułu NodeModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-node-module/module-twin.png)

9. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do NodeModule, który będzie odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny mieści się w dopuszczalnym limicie. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz vs Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Aby wdrożyć projekt modułu na urządzeniu IoT Edge, użyj rozszerzenia Eksploratora Visual Studio Code i narzędzia Azure IoT Tools. Masz już manifest wdrożenia przygotowany dla danego scenariusza, plik **Deployment. JSON** w folderze config. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinna zostać wyświetlona nowa **NodeModule** z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać. Urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie NodeModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

W manifeście wdrożenia użyto sznurka modułu NodeModule w celu ustawienia progu temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń Szczegóły na urządzeniu IoT Edge, aby zobaczyć uruchomione moduły.

2. Kliknij prawym przyciskiem myszy pozycję **NodeModule** i wybierz pozycję **Edytuj sznurek modułu**.

3. Znajdź **TemperatureThreshold** w odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni do 10 stopni wyższych niż Najnowsza zgłoszona temperatura.

4. Zapisz plik splotu modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku Edycja sznurka modułu i wybierz polecenie **Aktualizuj sznurek modułu**.

6. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat [opracowywania własnych modułów IoT Edge](module-development.md) lub tworzenia [modułów z Visual Studio Code](how-to-vs-code-develop-module.md). Aby zapoznać się z przykładami modułów IoT Edge, z uwzględnieniem modułu symulowanej temperatury, zapoznaj się z przykładami modułów [IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge może pomóc w wdrażaniu usług Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
