---
title: Samouczek — niestandardowy moduł Java przy użyciu usługi Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka Java i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: d8ea58dca8235b6dfc49c14c519dd44dabdf0592
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733088"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Samouczek: Opracowanie modułu Java IoT Edge dla urządzeń z systemem Linux

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego w usłudze Wdrażanie usługi Azure IoT Edge na symulowanym urządzeniu w przewodniku Szybki start [systemu Linux.](quickstart-linux.md) Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Używanie programu Visual Studio Code do tworzenia modułu usługi IoT Edge w języku Java w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT dla języka Java.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku pokazano, jak opracować moduł w **języku Java** przy użyciu programu Visual **Studio Code**i jak wdrożyć go na urządzeniu z **systemem Linux.** Usługa IoT Edge nie obsługuje modułów Java dla urządzeń z systemem Windows.

Poniższa tabela służy do zapoznania się z opcjami tworzenia i wdrażania modułów Java:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Użyj kodu VS dla modułów Java w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Użyj kodu VS dla modułów Java na Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne dla tworzenia kontenerów Systemu Linux: [Opracowanie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wypełniając jeden z tych samouczków, należy mieć następujące wymagania wstępne w miejscu:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany za pomocą [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge w języku Java, zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Pakiet rozszerzenia języka Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) dla programu Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [ustaw zmienną środowiskową `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby wskazywała instalację zestawu JDK.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

W poniższych krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge w oparciu o pakiet szablonów programu Maven usługi Azure IoT Edge oraz zestaw SDK urządzeń Azure IoT z obsługą języka Java. Utworzysz projekt za pomocą programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku Java, który można dostosować przy użyciu własnego kodu.

1. W programie Visual Studio Code wybierz pozycję **Wyświetl** > **paletę poleceń,** aby otworzyć paletę poleceń programu VS Code.

2. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **Java Module**. |
   | Podaj wartość identyfikatora grupy groupId | Podaj wartość identyfikatora grupy lub zaakceptuj wartość domyślną **com.edgemodule**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **JavaModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/javamodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-java-module/repository.png)

Jeśli po raz pierwszy tworzysz moduł Java, pobranie pakietów maven może potrwać kilka minut. Gdy rozwiązanie jest gotowe, okno kod VS ładuje obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu:

* Folder **modułów** zawiera kod Java dla modułu i pliki platformy Docker do tworzenia modułu jako obrazu kontenera.
* Plik ** \.env** przechowuje poświadczenia rejestru kontenera.
* Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu.
* Plik **deployment.debug.template.json** kontenery debugowania wersji modułów.
* Nie będzie edytować folder ** \.vscode** lub ** \.plik gitignore** w tym samouczku.

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybierz architekturę docelową

Obecnie Visual Studio Code może tworzyć moduły Java dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, która jest przeznaczona dla każdego rozwiązania, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Domyślnie jest Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj **usługę Azure IoT Edge: Set Default Target Platform for Edge Solution**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, więc zachowamy domyślny **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze kodu VS otwórz **moduły** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

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

## <a name="build-and-push-your-module"></a>Zbuduj i wypchnij swój moduł

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **JavaModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego limitu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz terminal zintegrowany vs code, wybierając **view** > **terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w witrynie Azure portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, który jest zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj Eksploratora kodu programu Visual Studio i rozszerzenia Narzędzia Azure IoT, aby wdrożyć projekt modułu na urządzeniu usługi IoT Edge. Masz już manifest wdrożenia przygotowany dla twojego scenariusza, plik **deployment.json** w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze kodu programu Visual Studio rozwiń sekcję **Urządzenia usługi Azure IoT Hub,** aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy **JavaModule** działa wraz z **simulatedTemperatureSensor** moduł i **$edgeAgent** i **$edgeHub**.  

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze kodu programu Visual Studio kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetlanie wiadomości docierających do Centrum IoT. Może upłynąć trochę czasu, aby wiadomości dotarły. Urządzenie IoT Edge musi otrzymać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie JavaModule czekać, aż temperatura komputera osiągnie 25 stopni przed wysłaniem wiadomości. Dodaje również typ komunikatu **Alert** do wszystkich komunikatów, które osiągają ten próg temperatury.

## <a name="edit-the-module-twin"></a>Edycja bliźniaczej reprezentacji modułu

Użyliśmy bliźniaczej reprezentacji modułu JavaModule w manifeście wdrażania, aby ustawić próg temperatury na poziomie 25 stopni. Bliźniaczej reprezentacji modułu można zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia IoT Edge, aby wyświetlić uruchomione moduły.

2. Kliknij prawym przyciskiem myszy **opcję JavaModule** i wybierz polecenie **Edytuj bliźniaczą modułu**.

3. Znajdź **TemperatureThreshold** w żądanych właściwościach. Zmień jego wartość na nową temperaturę o 5 stopni do 10 stopni wyższą niż ostatnio zgłoszona temperatura.

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy dowolne miejsce w okienku edycji bliźniaczej reprezentacji modułu i wybierz opcję **Aktualizuj bliźniaczej reprezentacji modułu**.

6. Monitorowanie przychodzących komunikatów z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono moduł usługi IoT Edge, który filtruje nieprzetworzone dane generowane przez urządzenie usługi IoT Edge. Gdy będziesz gotowy do tworzenia własnych modułów, możesz dowiedzieć się więcej o [tworzeniu modułów IoT Edge](module-development.md) lub o tym, jak [tworzyć moduły za pomocą programu Visual Studio Code.](how-to-vs-code-develop-module.md) Zobacz [przykłady modułów IoT Edge,](https://github.com/Azure/iotedge/tree/master/edge-modules) aby zapoznać się z przykładami kodu, w tym symulowanym modułem temperatury.

Przejdź do następnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge pomaga wdrażać usługi w chmurze platformy Azure w celu przetwarzania i analizowania danych na serwerze brzegowym.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Strumienia Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
