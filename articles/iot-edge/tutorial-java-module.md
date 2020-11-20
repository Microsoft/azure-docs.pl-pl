---
title: Samouczek — samouczek niestandardowego modułu Java używający Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Java i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: 51b7f6e814a9fad286a934466daeb1ffced225c1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968064"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Samouczek: opracowywanie modułu IoT Edge Java dla urządzeń z systemem Linux

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Będziesz używać symulowanego urządzenia IoT Edge utworzonego w ramach wdrażania Azure IoT Edge na symulowanym urządzeniu w systemie [Linux](quickstart-linux.md) — Szybki Start. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Używanie programu Visual Studio Code do tworzenia modułu usługi IoT Edge w języku Java w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT dla języka Java.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób tworzenia modułu w **języku Java** przy użyciu **Visual Studio Code** i sposobu wdrażania go na **urządzeniu z systemem Linux**. IoT Edge nie obsługuje modułów języka Java dla urządzeń z systemem Windows.

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów Java:

| Java | Visual Studio Code | Program Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Używanie VS Code dla modułów języka Java w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Używanie VS Code dla modułów języka Java w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Linux: [Tworzenie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wykonując jeden z tych samouczków, należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenerów, taki jak [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

Aby utworzyć moduł IoT Edge w języku Java, Zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Pakiet rozszerzenia języka Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) dla programu Visual Studio Code.
* [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) i [ustaw zmienną środowiskową `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby wskazywała instalację zestawu JDK.
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >Procesy instalacji języka Java i Maven dodają zmienne środowiskowe do systemu. Po zakończeniu instalacji uruchom ponownie wszystkie otwarte Visual Studio Code Terminal, PowerShell lub wiersza polecenia. Ten krok zapewnia, że te narzędzia mogą rozpoznać polecenia języka Java i Maven, które przechodzą do przodu.

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

W poniższych krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT z obsługą języka Java. Utworzysz projekt za pomocą programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku Java, który można dostosować przy użyciu własnego kodu.

1. W Visual Studio Code wybierz pozycję **Widok**  >  **paleta poleceń** , aby otworzyć paletę poleceń vs Code.

2. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Java Module**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **JavaModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br>Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/javamodule. |
   | Podaj wartość identyfikatora grupy groupId | Podaj wartość identyfikatora grupy lub zaakceptuj wartość domyślną **com.edgemodule**. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-java-module/repository.png)

Jeśli tworzysz moduł Java po raz pierwszy, pobranie pakietów Maven może potrwać kilka minut. Gdy rozwiązanie jest gotowe, okno VS Code ładuje obszar roboczy rozwiązania IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu:

* Folder **modułów** zawiera kod Java dla modułu i plików platformy Docker, aby skompilować moduł jako obraz kontenera.
* Plik **\. ENV** przechowuje poświadczenia rejestru kontenerów.
* Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu.
* **deployment.debug.template.jsw** kontenerach plików jest debugowana wersja modułów.
* W tym samouczku nie będziesz edytować folderu **\. programu vscode** ani pliku **\. GITIGNORE** .

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

Rozszerzenie IoT Edge podejmuje próbę ściągnięcia poświadczeń rejestru kontenera z platformy Azure i wypełniania ich w pliku środowiska. Sprawdź, czy Twoje poświadczenia zostały już uwzględnione. Jeśli nie, Dodaj je teraz:

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać moduły Java dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego** lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W Eksploratorze vs Code Otwórz pozycję **moduły**  >  **JavaModule**  >  **src**  >  **Main**  >  **Java**  >  **com**  >  **edgemodule**  >  **App. Java**.

2. Dodaj następujący kod w górnej części pliku, aby zaimportować nowe przywoływane klasy.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Dodaj następującą definicję w klasie **App**. Ten zestaw zmiennych określa wartość progową temperatury. Zmierzona temperatura maszyny nie będzie zgłaszana w usłudze IoT Hub, dopóki nie przekroczy tej wartości.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Zastąp metodę execute elementu **MessageCallbackMqtt** następującym kodem. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat MQTT z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Dodaj następujące dwie statyczne klasy wewnętrzne w klasie **App**. Te klasy aktualizują zmienną tempThreshold, gdy zmieni się określona właściwość bliźniaczej reprezentacji modułu. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Dodaj następujące wiersze do metody **main** po metodzie **client.open()**, aby subskrybować aktualizacje bliźniaczej reprezentacji modułu.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Zapisz plik App.java.

8. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

9. Dodaj bliźniaczą reprezentację modułu **JavaModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **moduleContent**, po bliźniaczej reprezentacji modułu **$edgeHub**:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-java-module/module-twin.png)

10. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **JavaModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego limitu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz vs Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **Terminal**.

2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. W Eksploratorze VS Code kliknij prawym przyciskiem myszy **deployment.template.jsna** pliku i wybierz polecenie **Kompiluj i wypchnij IoT Edge rozwiązanie**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrażania, który jest skompilowany w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Aby wdrożyć projekt modułu na urządzeniu IoT Edge, użyj rozszerzenia Eksploratora Visual Studio Code i narzędzia Azure IoT Tools. Istnieje już manifest wdrożenia przygotowany dla danego scenariusza, **deployment.amd64.jsna** pliku w folderze konfiguracyjnym. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code w sekcji **IoT Hub platformy Azure** rozwiń węzeł **urządzenia** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz **deployment.amd64.jsw** pliku w folderze **config** , a następnie kliknij pozycję **Wybierz manifest wdrożenia Edge**. Nie używaj pliku deployment.template.json.

4. W obszarze urządzenia rozwiń węzeł **moduły** , aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinna zostać wyświetlona nowa **JavaModule** z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.  

    Uruchomienie modułów może potrwać kilka minut. Środowisko uruchomieniowe IoT Edge musi otrzymać nowy manifest wdrożenia, ściągnąć obrazy modułów z środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać. Urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie JavaModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

W manifeście wdrożenia użyto sznurka modułu JavaModule w celu ustawienia progu temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń Szczegóły na urządzeniu IoT Edge, aby zobaczyć uruchomione moduły.

2. Kliknij prawym przyciskiem myszy pozycję **JavaModule** i wybierz pozycję **Edytuj sznurek modułu**.

3. Znajdź **TemperatureThreshold** w odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni do 10 stopni wyższych niż Najnowsza zgłoszona temperatura.

4. Zapisz plik splotu modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku Edycja sznurka modułu i wybierz polecenie **Aktualizuj sznurek modułu**.

6. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono moduł IoT Edge, który filtruje dane pierwotne wygenerowane przez urządzenie IoT Edge.

Przejdź do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge pomaga wdrożyć usługi Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)