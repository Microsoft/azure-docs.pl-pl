---
title: Samouczek — opracowywanie modułów C dla systemu Windows za pomocą Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduły IoT Edge przy użyciu kodu C i wdrożyć je na urządzeniach z systemem Windows, na których są uruchomione IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f346e1b737075fa79dc1146152125a6c5a3ec1a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704689"
---
# <a name="tutorial-develop-c-iot-edge-modules-for-windows-devices"></a>Samouczek: opracowywanie modułów IoT Edge C dla urządzeń z systemem Windows

W tym artykule pokazano, jak za pomocą programu Visual Studio opracować kod C i wdrożyć go na urządzeniu z systemem Windows, na którym działa Azure IoT Edge.

Za pomocą modułów Azure IoT Edge można wdrażać kod, który implementuje logikę biznesową bezpośrednio na urządzeniach IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na zestawie C SDK.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Moduł wysyła komunikaty do strumienia tylko wtedy, gdy temperatura przekracza określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób tworzenia modułu w języku C przy użyciu programu Visual Studio 2019, a następnie wdrażania go na urządzeniu z systemem Windows. Jeśli tworzysz moduły dla urządzeń z systemem Linux, przejdź do pozycji [opracowywanie modułów w języku C IoT Edge dla urządzeń z systemem Linux](tutorial-csharp-module.md) .

Aby poznać opcje tworzenia i wdrażania modułów C na urządzeniach z systemem Windows, zapoznaj się z poniższą tabelą:

| C | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; i &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Opracowywanie modułów C dla WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

Przed rozpoczęciem pracy z tym samouczkiem Skonfiguruj środowisko programistyczne, postępując zgodnie z instrukcjami podanymi w samouczku [programowanie IoT Edge modułów dla urządzeń z systemem Windows](tutorial-develop-for-windows.md) . Po jego zakończeniu środowisko będzie zawierać następujące wymagania wstępne:

* Bezpłatna lub Standardowa usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.
* [Urządzenie z systemem Windows, na którym działa Azure IoT Edge](quickstart.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](../container-registry/index.yml).
* [Program Visual Studio 2019](/visualstudio/install/install-visual-studio)został skonfigurowany przy użyciu rozszerzenia [narzędzi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* Program [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)skonfigurowany do uruchamiania kontenerów systemu Windows.

Zainstaluj zestaw SDK języka C usługi Azure IoT dla systemu Windows x64 za pomocą vcpkg, uruchamiając następujące polecenia:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), Pobierz i Zainstaluj Azure IoT Edge narzędzia dla programu Visual Studio 2017 z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

W tej sekcji utworzysz projekt modułu IoT Edge, który jest oparty na zestawie C SDK przy użyciu programu Visual Studio i rozszerzenia narzędzi Azure IoT Edge. Po utworzeniu szablonu projektu należy dodać nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Otwórz program Visual Studio 2019, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W okienku **Utwórz nowy projekt** Wyszukaj **IoT Edge** a następnie na liście wyników wybierz projekt **Azure IoT Edge (Windows amd64)** .

   ![Zrzut ekranu przedstawiający okienko IoT Edge "Utwórz nowy projekt".](./media/tutorial-c-module-windows/new-project.png)

1. Wybierz pozycję **Dalej**.

    Zostanie otwarte okienko **Konfiguruj nowy projekt** .

   ![Zrzut ekranu przedstawiający okienko "Konfigurowanie nowego projektu".](./media/tutorial-c-module-windows/configure-project.png)

1. W okienku **Konfiguruj nowy projekt** Zmień nazwę projektu i rozwiązania na bardziej opisową, taką jak **CTutorialApp**. 

1. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

   Zostanie otwarte okienko **Dodawanie modułu** .

   ![Zrzut ekranu przedstawiający okienko "Dodawanie modułu" w celu skonfigurowania projektu.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Na stronie **Konfiguruj nowy projekt** wykonaj następujące czynności:

   a. W okienku po lewej stronie wybierz szablon **modułu C** .  
   b. W polu **Nazwa modułu** wprowadź **CModule**.  
   c. W polu **adres URL repozytorium** Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry w następującym formacie: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie wartości Nazwa projektu modułu.  Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal.

1. Wybierz pozycję **Dodaj** , aby utworzyć projekt.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrożenia udostępnia poświadczenia dla rejestru kontenerów za pomocą środowiska uruchomieniowego IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **klucze dostępu** w rejestrze kontenerów platformy Azure.

1. W programie Visual Studio Eksplorator rozwiązań Otwórz *deployment.template.jsna* pliku.

1. Znajdź właściwość **registryCredentials** w $edgeAgent żądanych właściwościach. Adres rejestru właściwości powinien być wypełniany przy użyciu informacji podanych podczas tworzenia projektu. Pola Nazwa użytkownika i hasło powinny zawierać nazwy zmiennych. Na przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otwórz plik środowiska (ENV) w rozwiązaniu modułu. Domyślnie plik jest ukryty w Eksplorator rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki** , aby go wyświetlić. Plik ENV powinien zawierać te same zmienne nazwy użytkownika i hasła, które zostały podane w *deployment.template.js* pliku.

1. Dodaj wartości **Nazwa użytkownika** i **hasło** z usługi Azure Container Registry.

1. Zapisz zmiany w pliku ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Kod modułu domyślnego odbiera komunikaty w kolejce wejściowej i przekazuje je za pomocą kolejki wyjściowej. Dodajmy jeszcze więcej kodu, aby moduł przetworzył komunikaty na brzegu przed przekazaniem ich do centrum IoT. Zaktualizuj moduł, aby przeanalizować dane temperatury w każdym komunikacie i wysłać komunikat do centrum IoT tylko wtedy, gdy temperatura przekroczy określony próg.

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   a. Pobierz [repozytorium GitHub Parson](https://github.com/kgabis/parson).  
   b. Skopiuj pliki *Parson. c* i *Parson. h* do projektu CModule.  
   c. W programie Visual Studio Otwórz plik *CMakeLists.txt* z folderu projektu CModule.  
   d. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Dodaj `my_parson` do listy bibliotek w sekcji "target_link_libraries" pliku *CMakeLists.txt* .  
   f. Zapisz plik *CMakeLists.txt*.  
   przykład Wybierz pozycję **CModule**  >  **Main. c**. W dolnej części listy instrukcji include Dodaj nową instrukcję, która ma zostać uwzględniona `parson.h` w obsłudze JSON:

      ```c
      #include "parson.h"
      ```

1. W pliku *Main. c* Dodaj zmienną globalną o nazwie `temperatureThreshold` obok `messagesReceivedByInput1Queue` zmiennej. Ta zmienna ustawia wartość, którą pomiar temperatury musi przekroczyć, aby dane były wysyłane do centrum IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Znajdź `CreateMessageInstance` funkcję w *Main. c*. Zastąp wewnętrzną instrukcję *if-else* następującym kodem, który dodaje kilka wierszy funkcjonalności:

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

   Nowe wiersze kodu w instrukcji *else* dodają do wiadomości nową właściwość, która oznacza komunikat jako alert. Ten kod przedstawia wszystkie komunikaty jako alerty, ponieważ będziemy dodawać funkcje, które wysyłają komunikaty do centrum IoT Hub tylko wtedy, gdy zgłaszają wysokie temperatury.

1. Znajdź `InputQueue1Callback` funkcję i Zastąp całą funkcję poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Po odebraniu komunikatu sprawdza, czy zgłoszona temperatura przekracza wartość progową. Jeśli temperatura przekroczy wartość progową, funkcja przekazuje komunikat do kolejki wyjściowej. Jeśli nie przekroczy wartości progowej, funkcja zignoruje komunikat.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają własny sznurek modułu, który umożliwia skonfigurowanie kodu działającego w module bezpośrednio z chmury.

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

1. Wyszukaj `SetupCallbacksForModule` funkcję. Zastąp funkcję następującym kodem, który dodaje instrukcję *else-if* , aby sprawdzić, czy jest on aktualizowany.

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

1. Zapisz *główny plik c* .

1. Otwórz *deployment.template.js* pliku.

1. Dodaj splot modułu **CModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Zrzut ekranu przedstawiający dwuosiowy moduł dodawany do szablonu wdrożenia.](./media/tutorial-c-module-windows/module-twin.png)

1. Zapisz plik *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod **CModule** , aby odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny jest mniejsza niż akceptowalna wartość progowa. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Podaj poświadczenia rejestru kontenerów platformy Docker na komputerze deweloperskim, aby można było wypchnąć obraz kontenera do zapisania w rejestrze.

1. Otwórz program PowerShell lub okno wiersza polecenia.

1. Zaloguj się do platformy Docker przy użyciu poświadczeń usługi Azure Container Registry zapisanych po utworzeniu rejestru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie o zabezpieczeniach, które zaleca korzystanie z programu `--password-stdin` . Chociaż zalecamy to najlepsze rozwiązanie w zakresie scenariuszy produkcyjnych, to poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz [informacje dotyczące logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Kompiluj i wypchnij

Komputer deweloperski ma teraz dostęp do rejestru kontenerów, a Twoje urządzenia IoT Edge również. Czas, aby przekształcić kod projektu w obraz kontenera.

1. W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1**. Na potrzeby tego samouczka została wybrana nazwa **CTutorialApp** i, ponieważ tworzysz moduł systemu Windows, rozszerzenie powinno mieć wartość **Windows. amd64**.

1. Wybierz pozycję **kompilacje i moduły IoT Edge wypychania**.

   Polecenie Build i push uruchamia trzy operacje:
   * Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie *config*, który zawiera pełny manifest wdrożenia. Jest ona tworzona na podstawie informacji zawartych w szablonie wdrożenia i innych plikach rozwiązania. 
   * Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. 
   * Na koniec jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale będzie szybciej przy następnym uruchomieniu poleceń.

## <a name="deploy-modules-to-the-device"></a>Wdrażanie modułów na urządzeniu

Użyj programu Visual Studio Cloud Explorer i rozszerzenia narzędzi Azure IoT Edge, aby wdrożyć projekt modułu na urządzeniu IoT Edge. Manifest wdrożenia został już przygotowany dla danego scenariusza, *deployment.windows-amd64.js* pliku w folderze *konfiguracyjnym* . Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W programie Visual Studio Cloud Explorer rozwiń zasoby, aby wyświetlić listę urządzeń IoT.

1. Kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge, na którym chcesz odebrać wdrożenie.

1. Wybierz pozycję **Utwórz wdrożenie**.

1. W Eksploratorze plików programu Visual Studio wybierz *deployment.windows-amd64.jsw* pliku w folderze *config* Twojego rozwiązania.

1. Odśwież program Cloud Explorer, aby wyświetlić wdrożone moduły wymienione na urządzeniu.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Po zastosowaniu manifestu wdrożenia na urządzeniu IoT Edge środowisko uruchomieniowe IoT Edge na urządzeniu gromadzi informacje o nowych wdrożeniach i zacznie je wykonywać. Wszystkie moduły uruchomione na urządzeniu, ale nie zawarte w manifeście wdrożenia, są zatrzymane. Wszystkie moduły, których brakuje na urządzeniu, są uruchomione.

Możesz użyć rozszerzenia narzędzi IoT Edge, aby wyświetlić komunikaty w miarę ich przybycia do centrum IoT Hub.

1. W programie Visual Studio Cloud Explorer wybierz nazwę urządzenia IoT Edge.

1. Na liście **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

1. Wyświetlanie komunikatów, które docierają do centrum IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Zmiany w kodzie CModule muszą czekać na 25 stopni przed wysłaniem komunikatów. Kod dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

   ![Zrzut ekranu przedstawiający okno dane wyjściowe zawierające komunikaty docierające do centrum IoT Hub.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

Za pomocą sznurka modułu CModule można ustawić próg temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Otwórz *deployment.windows-amd64.js* pliku. 

   *Nie* otwieraj pliku *Deployment. Template* . Jeśli manifest wdrożenia nie jest widoczny w pliku *konfiguracji* w Eksplorator rozwiązań, zaznacz ikonę **Pokaż wszystkie pliki** na pasku narzędzi Eksplorator rozwiązań.

1. Poszukaj CModuleej przędzy i zmień wartość parametru **temperatureThreshold** na nową temperaturę od 5 do 10 stopni wyższą od najnowszej zgłoszonej temperatury.

1. Zapisz *deployment.windows-amd64.js* pliku.

1. Postępuj zgodnie z instrukcjami wdrażania ponownie, aby zastosować zaktualizowany manifest wdrożenia na urządzeniu.

1. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować w następnym zalecanym artykule, możesz zachować i ponownie użyć zasobów i konfiguracji utworzonych w tym samouczku. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie, aby uniknąć naliczania opłat, możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym miejscu.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge.

Aby dowiedzieć się, jak Azure IoT Edge może pomóc wdrożyć usługi Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Usługa Custom Vision](tutorial-deploy-custom-vision.md)
