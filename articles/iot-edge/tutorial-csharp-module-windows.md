---
title: Samouczek — opracowywanie modułu C# dla systemu Windows za pomocą Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge przy użyciu kodu w języku C# i wdrożyć go na urządzeniu z systemem Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 24a63a167f640646b9e07126a038663f4da3e23b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449596"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: opracowywanie modułu IoT Edge C# dla urządzeń z systemem Windows

Użyj programu Visual Studio, aby opracować kod C# i wdrożyć go na urządzeniu z systemem Windows, na którym działa Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na zestawie C# SDK.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku przedstawiono sposób tworzenia modułu w **języku C#** przy użyciu **programu Visual Studio 2019** i wdrażania go na **urządzeniu z systemem Windows**. Jeśli tworzysz moduły dla urządzeń z systemem Linux, przejdź do [obszaru opracowywanie modułu IoT Edge C# dla urządzeń z systemem Linux](tutorial-csharp-module.md) .

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów języka C# na urządzeniach z systemem Windows:

| C# | Visual Studio Code | Program Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 — programowanie** | ![Opracowywanie modułów języka C# dla WinAMD64 w VS Code](./media/tutorial-c-module/green-check.png) | ![Opracowywanie modułów języka C# dla WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 Debug** |   | ![Debugowanie modułów języka C# dla programu WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy zapoznać się z poprzednim samouczkiem dotyczącym konfigurowania środowiska programistycznego, [opracowywania modułu IoT Edge dla urządzenia z systemem Windows](tutorial-develop-for-windows.md). Po ukończeniu tego samouczka należy spełnić następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Windows, na którym działa Azure IoT Edge](quickstart.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Program Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) został skonfigurowany z rozszerzeniem [narzędzi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Pulpit platformy Docker](https://docs.docker.com/docker-for-windows/install/) skonfigurowany do uruchamiania kontenerów systemu Windows.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), plrease Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu vs 2017 z witryny Visual Studio Marketplace

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu IoT Edge przy użyciu programu Visual Studio i rozszerzenia narzędzi Azure IoT Edge Tools. Po utworzeniu szablonu projektu Dodaj nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Narzędzia Azure IoT Edge zawierają szablony projektów dla wszystkich obsługiwanych języków modułów IoT Edge w programie Visual Studio. Te szablony mają wszystkie pliki i kod, które są potrzebne do wdrożenia modułu roboczego w celu przetestowania IoT Edge, lub umożliwiają rozpoczęcie dostosowywania szablonu przy użyciu własnej logiki biznesowej.

1. Uruchom program Visual Studio 2019 i wybierz pozycję **Utwórz nowy projekt**.

2. Wyszukaj **IoT Edge** i wybierz projekt **Azure IoT Edge (Windows amd64)** . Kliknij przycisk **Dalej**.

   ![Utwórz nowy projekt Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Zmień nazwę projektu i rozwiązania na coś, takiego jak **CSharpTutorialApp**. Kliknij przycisk **Utwórz** , aby utworzyć projekt.

   ![Skonfiguruj nowy projekt Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Skonfiguruj projekt przy użyciu następujących wartości:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz szablon | Wybierz **moduł C#**. |
   | Nazwa projektu modułu | Nazwij moduł **CSharpModule**. |
   | Repozytorium obrazów platformy Docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie wartości nazwy projektu modułu. Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br> Finalne repozytorium obrazów wygląda jak \<registry name\> . azurecr.IO/csharpmodule. |

   ![Skonfiguruj projekt dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Wybierz pozycję **Dodaj** , aby utworzyć projekt.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrożenia udostępnia poświadczenia dla rejestru kontenerów za pomocą środowiska uruchomieniowego IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **klucze dostępu** w rejestrze kontenerów platformy Azure.

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz **deployment.template.js** plik.

2. Znajdź właściwość **registryCredentials** w $edgeAgent żądanych właściwościach. Powinien mieć adres rejestru autowypełniany na podstawie informacji podanych podczas tworzenia projektu, a następnie pola username i Password powinny zawierać nazwy zmiennych. Na przykład:

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

1. W programie Visual Studio Otwórz **CSharpModule**  >  **program.cs**.

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj zmienną **temperatureThreshold** do klasy **program** po zmiennej licznika. Zmienna temperatureThreshold ustawia wartość, którą pomiar temperatury musi przekroczyć, aby dane były wysyłane do centrum IoT Hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj klasy **MessageBody**, **Machine**i **otoczenia** do klasy **program** po deklaracjach zmiennych. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Znajdź metodę **init** . Ta metoda tworzy i konfiguruje obiekt **ModuleClient** , który umożliwia modułowi łączenie się z lokalnymi Azure IoT Edge środowiska uruchomieniowego w celu wysyłania i odbierania komunikatów. Kod rejestruje również wywołanie zwrotne, aby odbierać komunikaty z Centrum IoT Edge za pośrednictwem punktu końcowego **INPUT1** .

   Zastąp całą metodę init następującym kodem:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Ta zaktualizowana metoda init nadal konfiguruje połączenie do środowiska uruchomieniowego IoT Edge z ModuleClient, ale również dodaje nowe funkcje. Odczytuje odpowiednie właściwości sznurka modułu, aby pobrać wartość **temperatureThreshold** . Następnie tworzy wywołanie zwrotne, które nasłuchuje wszelkich przyszłych aktualizacji odpowiednich właściwości w module. Za pomocą tego wywołania zwrotnego można zdalnie aktualizować próg temperatury w module, a zmiany zostaną włączone do modułu.

   Zaktualizowana metoda init również zmienia istniejącą metodę **SetInputMessageHandlerAsync** . W przykładowym kodzie komunikaty przychodzące na *INPUT1* są przetwarzane przy użyciu funkcji *PipeMessage* , ale chcemy zmienić to, aby użyć funkcji *FilterMessages* , którą utworzymy w poniższych krokach.

6. Dodaj nową metodę **onDesiredPropertiesUpdate** do klasy **program** . Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Usuń przykładową metodę **PipeMessage** i Zastąp ją nową metodą **FilterMessages** . Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Zapisz plik Program.cs.

9. Otwórz **deployment.template.js** pliku w rozwiązaniu IoT Edge. Ten plik zawiera informacje o agencie IoT Edge, które moduły wdrożyć, w tym przypadku **SimulatedTemperatureSensor** i **CSharpModule**i informuje Centrum IoT Edge o sposobie przesyłania komunikatów między nimi.

10. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **modulesContent** po bliźniaczej reprezentacji modułu **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-csharp-module-windows/module-twin.png)

11. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Użyj poniższego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Użyj nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie dotyczące zabezpieczeń zalecające użycie programu `--password-stdin` . Chociaż najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje dotyczące [logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1** , a ponieważ tworzysz moduł systemu Windows, rozszerzenie powinno mieć wartość **Windows. amd64**.

3. Wybierz pozycję **kompilacje i moduły IoT Edge wypychania**.

   Polecenie Build i push uruchamia trzy operacje. Po pierwsze tworzy nowy folder w rozwiązaniu o nazwie **config** , który zawiera pełny manifest wdrożenia, z wbudowanymi informacjami w szablonie wdrożenia i innych plikach rozwiązania. Następnie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie odpowiednich pliku dockerfile dla architektury docelowej. Następnie jest uruchamiany `docker push` w celu wypchnięcia repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj programu Visual Studio Cloud Explorer i rozszerzenia narzędzi Azure IoT Edge, aby wdrożyć projekt modułu na urządzeniu IoT Edge. Istnieje już manifest wdrożenia przygotowany dla danego scenariusza, **deployment.windows-amd64.jsna** pliku w folderze konfiguracyjnym. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge zostało uruchomione.

1. W programie Visual Studio Cloud Explorer rozwiń zasoby, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge, na którym chcesz odebrać wdrożenie.

3. Wybierz pozycję **Utwórz wdrożenie**.

4. W Eksploratorze plików wybierz **deployment.windows-amd64.jsw** pliku w folderze config Twojego rozwiązania.

5. Odśwież program Cloud Explorer, aby zobaczyć wdrożone moduły wymienione na urządzeniu.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz użyć rozszerzenia narzędzi IoT Edge, aby wyświetlić komunikaty w miarę ich nadejścia do IoT Hub.

1. W programie Visual Studio Cloud Explorer wybierz nazwę urządzenia IoT Edge.

2. Na liście **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Wyświetl komunikaty docierające do IoT Hub. Może upłynąć trochę czasu, zanim pojawią się komunikaty, ponieważ zmiany wprowadzone w kodzie CSharpModule zaczekają na 25 stopni przed wysłaniem komunikatów. Dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

   ![Wyświetl komunikaty docierające do IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

Do ustawiania progu temperatury na 25 stopni użyto dwuosiowego modułu CSharpModule. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Otwórz **deployment.windows-amd64.js** pliku. (Nie jest to plik wdrożenia. Template. Jeśli manifest wdrożenia nie jest widoczny w pliku konfiguracji w Eksploratorze rozwiązań, wybierz ikonę **Pokaż wszystkie pliki** na pasku narzędzi Eksploratora.

2. Znajdź splot CSharpModule i zmień wartość parametru **temperatureThreshold** na nową temperaturę o 5 stopni do 10 stopni wyższych od najnowszej zgłoszonej temperatury.

3. Zapisz **deployment.windows-amd64.js** pliku.

4. Wykonaj ponownie kroki wdrażania, aby zastosować zaktualizowany manifest wdrożenia na urządzeniu.

5. Monitoruj przychodzące komunikaty z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat [opracowywania własnych modułów IoT Edge](module-development.md) lub tworzenia [modułów w programie Visual Studio](how-to-visual-studio-develop-module.md). Aby zapoznać się z przykładami modułów IoT Edge, z uwzględnieniem modułu symulowanej temperatury, zapoznaj się z przykładami modułów [IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak Azure IoT Edge może pomóc w wdrażaniu usług Azure Cloud Services w celu przetwarzania i analizowania danych na krawędzi.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
