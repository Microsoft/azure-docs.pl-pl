---
title: Samouczek — opracowanie modułu C# dla systemu Windows przy użyciu usługi Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge z kodem języka C# i wdrożyć go na urządzeniu usługi Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- amqp
ms.openlocfilehash: b71db71ac61e0dcd65a2546b2164610e618dab18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733506"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: Opracowanie modułu C# IoT Edge dla urządzeń z systemem Windows

Za pomocą programu Visual Studio opracować kod języka C# i wdrożyć go na urządzeniu z systemem Windows z uruchomiona usługą Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na C# SDK.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku pokazano, jak opracować moduł w **języku C#** przy użyciu **programu Visual Studio 2019** i wdrożyć go na **urządzeniu z systemem Windows.** Jeśli tworzysz moduły dla urządzeń z systemem Linux, przejdź do [opracowania modułu IoT Edge języka C# dla urządzeń z systemem Linux.](tutorial-csharp-module.md)

Poniższa tabela służy do zapoznania się z opcjami tworzenia i wdrażania modułów języka C# na urządzeniach z systemem Windows:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 rozwijać** | ![Tworzenie modułów języka C# dla programu WinAMD64 w programie VS Code](./media/tutorial-c-module/green-check.png) | ![Tworzenie modułów języka C# dla programu WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Debugowanie amd64 systemu Windows** |   | ![Moduły debugowania języka C# dla programu WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne, [Opracowanie modułu IoT Edge dla urządzenia z systemem Windows](tutorial-develop-for-windows.md). Po zakończeniu tego samouczka, już powinien mieć następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Windows z usługą Azure IoT Edge](quickstart.md).
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) skonfigurowany z [rozszerzeniem Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Pulpit platformy Docker](https://docs.docker.com/docker-for-windows/install/) skonfigurowany do uruchamiania kontenerów systemu Windows.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15.7 lub nowsza), pobierz i zainstaluj [narzędzia Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu VS 2017 z witryny Visual Studio marketplace

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu usługi IoT Edge przy użyciu programu Visual Studio i rozszerzenia Azure IoT Edge Tools. Po utworzeniu szablonu projektu dodaj nowy kod, aby moduł odfiltrowywany wiadomości na podstawie ich zgłoszonych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Narzędzia usługi Azure IoT Edge zapewniają szablony projektów dla wszystkich obsługiwanych języków modułów usługi IoT Edge w programie Visual Studio. Te szablony mają wszystkie pliki i kod, które należy wdrożyć działający moduł do testowania usługi IoT Edge lub daje punkt wyjścia do dostosowania szablonu z własnej logiki biznesowej.

1. Uruchom program Visual Studio 2019 i wybierz pozycję **Utwórz nowy projekt**.

2. Wyszukaj **usługę IoT Edge** i wybierz projekt **usługi Azure IoT Edge (Windows amd64).** Kliknij przycisk **Dalej**.

   ![Tworzenie nowego projektu usługi Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Zmień nazwę projektu i rozwiązania na coś opisowego jak **CSharpTutorialApp**. Kliknij **przycisk Utwórz,** aby utworzyć projekt.

   ![Konfigurowanie nowego projektu usługi Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Skonfiguruj projekt przy następujących wartościach:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie szablonu | Wybierz **moduł C#**. |
   | Nazwa projektu modułu | Nazwij moduł **CSharpModule**. |
   | Repozytorium obrazów platformy Docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniony z wartości nazwy projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |

   ![Konfigurowanie projektu dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Wybierz **pozycję Dodaj,** aby utworzyć projekt.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrażania udostępnia poświadczenia rejestru kontenerów ze środowiska wykonawczego usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **Klucze programu Access** rejestru kontenerów platformy Azure.

1. W Eksploratorze rozwiązań programu Visual Studio otwórz plik **deployment.template.json.**

2. Znajdź **właściwość registryCredentials** w $edgeAgent żądanych właściwościach. Powinien mieć adres rejestru automatycznie wypełniony z informacji podanych podczas tworzenia projektu, a następnie pola nazwy użytkownika i hasła powinny zawierać nazwy zmiennych. Przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otwórz plik **.env** w rozwiązaniu modułu. (Jest domyślnie ukryty w Eksploratorze rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki,** aby go wyświetlić). Plik .env powinien zawierać te same zmienne nazwy użytkownika i hasła, które zostały 2000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000

4. Dodaj wartości **nazwy użytkownika** i **hasła** z rejestru kontenerów platformy Azure.

5. Zapisz zmiany w pliku env.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Domyślny kod modułu odbiera wiadomości w kolejce wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetwarzał wiadomości na krawędzi przed przekazaniem ich do usługi IoT Hub. Zaktualizuj moduł tak, aby analizował dane temperatury w każdej wiadomości i wysyła komunikat do usługi IoT Hub tylko wtedy, gdy temperatura przekracza określony próg.

1. W programie Visual Studio otwórz **Program.cs CSharpModule** > **Program.cs**.

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj **temperaturęS zmienna Podtrzymuje** do **klasy Program** po zmiennej licznika. TemperaturaDekreza podtrzymuje ustawia wartość, którą mierzona temperatura musi przekroczyć dla danych, które mają być wysyłane do centrum IoT hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj **MessageBody**, **Machine**i **Ambient** klasy do **Program** klasy po deklaracji zmiennych. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

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

5. Znajdź metodę **Init.** Ta metoda tworzy i konfiguruje **ModuleClient** obiektu, który umożliwia modułowi połączyć się z lokalnym środowisko uruchomieniowe usługi Azure IoT Edge do wysyłania i odbierania wiadomości. Kod rejestruje również wywołania zwrotnego do odbierania wiadomości z usługi IoT Edge hub za pośrednictwem punktu końcowego **input1.**

   Zastąp całą metodę Init następującym kodem:

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

   Ta zaktualizowana metoda Init nadal konfiguruje połączenie ze środowiska wykonawczego usługi IoT Edge za pomocą moduleClient, ale także dodaje nowe funkcje. Odczytuje żądane właściwości modułu, aby pobrać **temperaturęWynikushold.** Następnie tworzy wywołanie zwrotne, który nasłuchuje wszelkich przyszłych aktualizacji do modułu bliźniaczej właściwości żądane właściwości. Za pomocą tego wywołania zwrotnego można zdalnie zaktualizować próg temperatury w bliźniaczej reprezentacji modułu, a zmiany zostaną włączone do modułu.

   Zaktualizowana metoda Init zmienia również istniejącą metodę **SetInputMessageHandlerAsync.** W przykładowym kodzie przychodzące wiadomości na *input1* są przetwarzane za pomocą *pipemessage* funkcji, ale chcemy zmienić, aby użyć *FilterMessage funkcji,* które utworzymy w poniższych krokach.

6. Dodaj nową metodę **onDesiredPropertiesUpdate** do **klasy Program.** Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

7. Usuń próbkę **PipeMessage** metody i zastąpić go nową metodę **FilterMessages.** Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**.

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

9. Otwórz plik **deployment.template.json** w rozwiązaniu usługi IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły do wdrożenia, w tym przypadku **SimulatedTemperatureSensor** i **CSharpModule**, i informuje centrum usługi IoT Edge, jak rozsyłać wiadomości między nimi.

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

## <a name="build-and-push-your-module"></a>Zbuduj i wypchnij swój moduł

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Użyj następującego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Użyj nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w witrynie Azure portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz utworzyć. Domyślna nazwa to **AzureIotEdgeApp1,** a ponieważ budujesz moduł Windows, rozszerzenie powinno być **Windows.Amd64**.

3. Wybierz **opcję Zbuduj i wypychaj moduły brzegowe IoT**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj Eksploratora chmury programu Visual Studio i rozszerzenia Azure IoT Edge Tools, aby wdrożyć projekt modułu na urządzeniu usługi IoT Edge. Masz już manifest wdrożenia przygotowany dla twojego scenariusza, plik **deployment.json** w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze chmury programu Visual Studio rozwiń zasoby, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, które ma zostać odebrane w celu wdrożenia.

3. Wybierz **pozycję Utwórz wdrożenie**.

4. W eksploratorze plików wybierz plik **deployment.windows-amd64** w folderze konfiguracji rozwiązania.

5. Odśwież Eksploratora chmury, aby wyświetlić wdrożone moduły wymienione w urządzeniu.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Za pomocą rozszerzenia Narzędzia brzegowe IoT można wyświetlać wiadomości po ich dotarcie do centrum IoT Hub.

1. W Eksploratorze chmury programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge.

2. Na liście **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Wyświetlanie wiadomości docierających do Centrum IoT. Może upłynąć trochę czasu dla wiadomości, aby dotrzeć, ponieważ zmiany wprowadzone do kodu CSharpModule czekać, aż temperatura komputera osiągnie 25 stopni przed wysłaniem wiadomości. Dodaje również typ komunikatu **Alert** do wszystkich komunikatów, które osiągają ten próg temperatury.

   ![Wyświetlanie wiadomości docierających do Usługi IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edycja bliźniaczej reprezentacji modułu

Użyliśmy cSharpModule moduł twin, aby ustawić próg temperatury na 25 stopni. Bliźniaczej reprezentacji modułu można zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio otwórz plik **deployment.windows-amd64.json.** (Nie plik deployment.template. Jeśli manifest wdrożenia nie jest widoczny w pliku konfiguracyjnym w Eksploratorze rozwiązań, wybierz ikonę **Pokaż wszystkie pliki** na pasku narzędzi eksploratora).

2. Znajdź CSharpModule twin i zmienić wartość **temperatureThreshold** parametr do nowej temperatury 5 stopni do 10 stopni wyższe niż ostatnio zgłaszane temperatury.

3. Zapisz plik **deployment.windows-amd64.json.**

4. Wykonaj ponownie kroki wdrażania, aby zastosować zaktualizowany manifest wdrażania na urządzeniu.

5. Monitorowanie przychodzących komunikatów z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy będziesz gotowy do tworzenia własnych modułów, możesz dowiedzieć się więcej o [tworzeniu własnych modułów IoT Edge](module-development.md) lub o tym, jak tworzyć [moduły za pomocą programu Visual Studio.](how-to-visual-studio-develop-module.md) Przykłady modułów IoT Edge, w tym symulowanego modułu temperatury, można znaleźć w [przykładach modułów IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Możesz przejść do następnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge może pomóc we wdrażaniu usług w chmurze platformy Azure w celu przetwarzania i analizowania danych na serwerze brzegowym.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Strumienia Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
