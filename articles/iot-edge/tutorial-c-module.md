---
title: Samouczek projektowanie modułu C dla systemu Linux — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge przy użyciu kodu C i wdrożyć go na urządzeniu z systemem Linux IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: acedf0c5437ce0b4f1106cac4d1878c7a49e8a36
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463327"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Samouczek: opracowywanie modułu języka C IoT Edge przy użyciu kontenerów systemu Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Użyj Visual Studio Code, aby opracować kod C i wdrożyć go na urządzeniu z systemem Azure IoT Edge.

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi IoT Edge w języku C przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu Visual Studio Code i platformy Docker
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób tworzenia modułu w języku **C** przy użyciu **Visual Studio Code** i sposobu wdrażania go na urządzeniu IoT Edge. Jeśli tworzysz moduły przy użyciu kontenerów systemu Windows, przejdź do [opracowania modułu C IoT Edge przy użyciu kontenerów systemu Windows](tutorial-c-module-windows.md) .

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów C przy użyciu kontenerów systemu Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Używanie VS Code dla modułów C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Korzystanie z modułów VS for C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Używanie VS Code dla modułów C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Korzystanie z modułów VS for C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Linux: [opracowywanie modułów IoT Edge przy użyciu kontenerów systemu Linux](tutorial-develop-for-linux.md). Wykonując ten samouczek, należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Urządzenie, na którym działa Azure IoT Edge. Korzystając z przewodników Szybki Start, można skonfigurować urządzenie z systemem [Linux](quickstart-linux.md) lub [urządzenie systemu Windows](quickstart.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany przy użyciu [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Platforma [Docker ce](https://docs.docker.com/install/) skonfigurowana do uruchamiania kontenerów systemu Linux.

Aby utworzyć moduł IoT Edge w języku C, Zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [Rozszerzenie języka C/C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) dla programu Visual Studio Code.

Instalowanie zestawu SDK języka C usługi Azure IoT nie jest wymagane w tym samouczku, ale może zapewnić przydatne funkcje, takie jak IntelliSense i odczytywanie definicji programów. Aby uzyskać informacje o instalacji, zobacz [zestawy SDK i biblioteki usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki dotyczą tworzenia projektu modułu IoT Edge dla języka C przy użyciu Visual Studio Code i rozszerzenia narzędzi Azure IoT Tools. Po utworzeniu szablonu projektu Dodaj nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Wybierz pozycję **Widok**  >  **paleta poleceń** , aby otworzyć paletę poleceń vs Code.

2. W palecie poleceń wpisz i uruchom polecenie **Azure: zaloguj**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **moduł C**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/cmodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

Rozszerzenie IoT Edge podejmuje próbę ściągnięcia poświadczeń rejestru kontenera z platformy Azure i wypełniania ich w pliku środowiska. Sprawdź, czy Twoje poświadczenia zostały już uwzględnione. Jeśli nie, Dodaj je teraz:

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie Visual Studio Code mogą opracowywać moduły C dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać, która architektura ma być ukierunkowana na każde rozwiązanie, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i Wyszukaj **Azure IoT Edge: Ustaw domyślną platformę docelową dla rozwiązania brzegowego** lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, co spowoduje zachowanie domyślnego **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Kod modułu domyślnego odbiera komunikaty w kolejce wejściowej i przekazuje je za pomocą kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetworzył komunikaty na brzegu przed przekazaniem ich do IoT Hub. Zaktualizuj moduł, aby przeanalizować dane dotyczące temperatury w każdym komunikacie i wysłać komunikat do IoT Hub, jeśli temperatura przekroczy określony próg.

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   1. Pobierz [repozytorium GitHub Parson](https://github.com/kgabis/parson). Skopiuj pliki **parson.c** i **parson.h** do folderu **CModule**.

   2. Otwórz   >  **CModule** modułów  >  **CMakeLists.txt**. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Dodaj `my_parson` do listy bibliotek w funkcji **target_link_libraries** CMakeLists.txt.

   4. Zapisz plik **CMakeLists.txt**.

   5. Otwórz **moduł**  >  **CModule**  >  **Main. c**. W dolnej części listy instrukcji include Dodaj nowy element, który ma zostać dołączony `parson.h` do obsługi JSON:

      ```c
      #include "parson.h"
      ```

1. W pliku **main.c** dodaj zmienną globalną o nazwie `temperatureThreshold` po sekcji include. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Znajdź `CreateMessageInstance` funkcję w Main. c. Zastąp wewnętrzną instrukcję if-else następującym kodem, który dodaje kilka wierszy funkcjonalności:

   ```c
       if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
       {
           free(messageInstance);
           messageInstance = NULL;
       }
       else
       {
           messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
           MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
           if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
           {
              printf("ERROR: Map_AddOrUpdate Failed!\r\n");
           }
       }
   ```

   Nowe wiersze kodu w instrukcji else dodają do wiadomości nową właściwość, która oznacza komunikat jako alert. Ten kod przedstawia wszystkie komunikaty jako alerty, ponieważ dodamy funkcję, która będzie wysyłać komunikaty do IoT Hub tylko wtedy, gdy zgłaszają wysokie temperatury.

1. Zastąp całą funkcję `InputQueue1Callback` poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Po odebraniu komunikatu sprawdza, czy zgłoszona temperatura przekracza wartość progową. Jeśli tak, przesyła komunikat do kolejki wyjściowej. W przeciwnym razie ignoruje komunikat.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Znajdź `SetupCallbacksForModule` funkcję. Zastąp funkcję następującym kodem, który dodaje instrukcję **else if** , aby sprawdzić, czy jest on aktualizowany.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Zapisz plik main.c.

1. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

1. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu CModule do szablonu wdrożenia](./media/tutorial-c-module/module-twin.png)

1. Zapisz plik **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do CModule, który będzie odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny mieści się w dopuszczalnym limicie. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz Terminal vs Code, wybierając pozycję **Wyświetl**  >  **Terminal**.

2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. W Eksploratorze VS Code kliknij prawym przyciskiem myszy **deployment.template.jsna** pliku i wybierz polecenie **Kompiluj i wypchnij IoT Edge rozwiązanie**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Aby wdrożyć projekt modułu na urządzeniu IoT Edge, użyj rozszerzenia Eksploratora Visual Studio Code i narzędzia Azure IoT Tools. Istnieje już manifest wdrożenia przygotowany dla danego scenariusza, **deployment.amd64.jsna** pliku w folderze konfiguracyjnym. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W Eksploratorze Visual Studio Code w sekcji **IoT Hub platformy Azure** rozwiń węzeł **urządzenia** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz **deployment.amd64.jsw** pliku w folderze **config** , a następnie kliknij pozycję **Wybierz manifest wdrożenia Edge**. Nie używaj pliku deployment.template.json.

4. W obszarze urządzenia rozwiń węzeł **moduły** , aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinna zostać wyświetlona nowa **CModule** z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

    Uruchomienie modułów może potrwać kilka minut. Środowisko uruchomieniowe IoT Edge musi otrzymać nowy manifest wdrożenia, ściągnąć obrazy modułów z środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

1. W Eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

   ![Wyświetl komunikaty docierające do IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

W manifeście wdrożenia użyto sznurka modułu CModule w celu ustawienia progu temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń Szczegóły na urządzeniu IoT Edge, aby zobaczyć uruchomione moduły.

2. Kliknij prawym przyciskiem myszy pozycję **CModule** i wybierz pozycję **Edytuj sznurek modułu**.

3. Znajdź **TemperatureThreshold** w odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni do 10 stopni wyższych niż Najnowsza zgłoszona temperatura.

4. Zapisz plik splotu modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku Edycja sznurka modułu i wybierz polecenie **Aktualizuj sznurek modułu**.

6. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge.

Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge może pomóc w wdrażaniu usług Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)