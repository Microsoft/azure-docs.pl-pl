---
title: Samouczek projektowanie modułu C dla systemu Windows — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge przy użyciu kodu C i wdrożyć go na urządzeniu z systemem Windows IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 99e00ebc8034e77b95986d3062578548bbe32df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857507"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: opracowywanie modułu IoT Edge C dla urządzeń z systemem Windows

Użyj programu Visual Studio, aby opracować kod C i wdrożyć go na urządzeniu z systemem Windows, na którym działa Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na zestawie C SDK.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku przedstawiono sposób tworzenia modułu w języku **C** przy użyciu **programu Visual Studio 2019** i wdrażania go na **urządzeniu z systemem Windows**. Jeśli tworzysz moduły dla urządzeń z systemem Linux, przejdź do [obszaru opracowywanie modułu C IoT Edge dla urządzeń z systemem Linux](tutorial-c-module.md) .

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów C na urządzeniach z systemem Windows:

| C | Visual Studio Code | Program Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Opracowywanie modułów C dla WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska deweloperskiego do tworzenia kontenerów systemu Windows: [programowanie IoT Edge modułów dla urządzeń z systemem Windows](tutorial-develop-for-windows.md). Po ukończeniu tego samouczka należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Windows, na którym działa Azure IoT Edge](quickstart.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Program Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) został skonfigurowany z rozszerzeniem [narzędzi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Pulpit platformy Docker](https://docs.docker.com/docker-for-windows/install/) skonfigurowany do uruchamiania kontenerów systemu Windows.
* Zainstaluj zestaw SDK usługi Azure IoT C dla systemu Windows x64 za pomocą vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu vs 2017 z witryny Visual Studio Marketplace

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu IoT Edge, który jest oparty na zestawie C SDK przy użyciu programu Visual Studio i rozszerzenia narzędzi Azure IoT Edge. Po utworzeniu szablonu projektu Dodaj nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Uruchom program Visual Studio 2019 i wybierz pozycję **Utwórz nowy projekt**.

2. Wyszukaj **IoT Edge** i wybierz projekt **Azure IoT Edge (Windows amd64)** . Kliknij przycisk **Dalej**.

   ![Utwórz nowy projekt Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Zmień nazwę projektu i rozwiązania na coś, takiego jak **CTutorialApp**. Kliknij przycisk **Utwórz** , aby utworzyć projekt.

   ![Skonfiguruj nowy projekt Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Skonfiguruj projekt przy użyciu następujących wartości:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz szablon | Wybierz **moduł C**. |
   | Nazwa projektu modułu | Nadaj modułowi nazwę **CModule**. |
   | Repozytorium obrazów platformy Docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie wartości nazwy projektu modułu. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/cmodule. |

   ![Skonfiguruj projekt dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Wybierz pozycję **Dodaj** , aby utworzyć projekt.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrożenia udostępnia poświadczenia dla rejestru kontenerów za pomocą środowiska uruchomieniowego IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **klucze dostępu** w rejestrze kontenerów platformy Azure.

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz **deployment.template.js** plik.

2. Znajdź właściwość **registryCredentials** w $edgeAgent żądanych właściwościach. Powinien on mieć swój adres rejestru autowypełniany na podstawie informacji podanych podczas tworzenia projektu. Pola Nazwa użytkownika i hasło powinny zawierać nazwy zmiennych. Na przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otwórz plik **ENV** w rozwiązaniu modułu. (Jest ona domyślnie ukryta w Eksplorator rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki** , aby go wyświetlić). Plik ENV powinien zawierać te same zmienne nazwy użytkownika i hasła, które zostały podane w deployment.template.jspliku.

4. Dodaj wartości **Nazwa użytkownika** i **hasło** z usługi Azure Container Registry.

5. Zapisz zmiany w pliku ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Kod modułu domyślnego odbiera komunikaty w kolejce wejściowej i przekazuje je za pomocą kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetworzył komunikaty na brzegu przed przekazaniem ich do IoT Hub. Zaktualizuj moduł, aby przeanalizować dane dotyczące temperatury w każdym komunikacie i wysłać komunikat do IoT Hub, jeśli temperatura przekroczy określony próg.

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   1. Pobierz [repozytorium GitHub Parson](https://github.com/kgabis/parson). Skopiuj pliki **Parson. c** i **Parson. h** do projektu **CModule** .

   2. W programie Visual Studio Otwórz plik **CMakeLists.txt** z folderu projektu CModule. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Dodaj `my_parson` do listy bibliotek w **target_link_libraries** sekcji pliku CMakeLists.txt.

   4. Zapisz plik **CMakeLists.txt**.

   5. Otwórz **CModule**  >  **Main. c**. W dolnej części listy instrukcji include Dodaj nowy element, który ma zostać dołączony `parson.h` do obsługi JSON:

      ```c
      #include "parson.h"
      ```

2. W pliku **Main. c** Dodaj zmienną globalną o nazwie `temperatureThreshold` obok zmiennej messagesReceivedByInput1Queue. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Znajdź `CreateMessageInstance` funkcję w Main. c. Zastąp wewnętrzną instrukcję if-else następującym kodem, który dodaje kilka wierszy funkcjonalności:

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

4. Znajdź `InputQueue1Callback` funkcję i Zastąp całą funkcję poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Po odebraniu komunikatu sprawdza, czy zgłoszona temperatura przekracza wartość progową. Jeśli tak, przesyła komunikat do kolejki wyjściowej. W przeciwnym razie ignoruje komunikat.

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

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

6. Znajdź `SetupCallbacksForModule` funkcję. Zastąp funkcję następującym kodem, który dodaje instrukcję **else if** , aby sprawdzić, czy jest on aktualizowany.

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

7. Zapisz plik main.c.

8. Otwórz **deployment.template.js** pliku.

9. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu CModule do szablonu wdrożenia](./media/tutorial-c-module-windows/module-twin.png)

10. Zapisz plik **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do **CModule** , aby odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny jest niższa niż akceptowalna wartość progowa. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

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

1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1**. Dla tego samouczka została wybrana nazwa **CTutorialApp** . Ze względu na to, że tworzysz moduł systemu Windows, rozszerzenie powinno mieć wartość **Windows. amd64**.

2. Wybierz pozycję **kompilacje i moduły IoT Edge wypychania**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, utworzony poza informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj programu Visual Studio Cloud Explorer i rozszerzenia narzędzi Azure IoT Edge, aby wdrożyć projekt modułu na urządzeniu IoT Edge. Istnieje już manifest wdrożenia przygotowany dla danego scenariusza, **deployment.windows-amd64.jsna** pliku w folderze konfiguracyjnym. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.


Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W programie Visual Studio Cloud Explorer rozwiń zasoby, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge, na którym chcesz odebrać wdrożenie.

3. Wybierz pozycję **Utwórz wdrożenie**.

4. W Eksploratorze plików wybierz plik **Deployment. Windows-amd64** w folderze config Twojego rozwiązania.

5. Odśwież program Cloud Explorer, aby zobaczyć wdrożone moduły wymienione na urządzeniu.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz użyć rozszerzenia narzędzi IoT Edge, aby wyświetlić komunikaty w miarę ich nadejścia do IoT Hub.

1. W programie Visual Studio Cloud Explorer wybierz nazwę urządzenia IoT Edge.

2. Na liście **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Wyświetl komunikaty docierające do IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

   ![Wyświetl komunikaty docierające do IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

Do ustawiania progu temperatury na 25 stopni użyto dwuosiowego modułu CModule. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Otwórz **deployment.windows-amd64.js** pliku. (Nie jest to plik wdrożenia. Template. Jeśli manifest wdrożenia nie jest widoczny w pliku konfiguracji w Eksploratorze rozwiązań, wybierz ikonę **Pokaż wszystkie pliki** na pasku narzędzi Eksploratora.

2. Znajdź splot CModule i zmień wartość parametru **temperatureThreshold** na nową temperaturę o 5 stopni do 10 stopni wyższych od najnowszej zgłoszonej temperatury.

3. Zapisz **deployment.windows-amd64.js** pliku.

4. Wykonaj ponownie kroki wdrażania, aby zastosować zaktualizowany manifest wdrożenia na urządzeniu.

5. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

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
