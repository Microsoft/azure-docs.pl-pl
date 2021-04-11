---
title: Samouczek — opracowywanie modułów w języku C# dla systemu Windows za pomocą Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduły IoT Edge przy użyciu kodu w języku C# i wdrożyć je na urządzeniach z systemem Windows, na których są uruchomione IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 84e5bbff11d0a5ff5d47ca43a3da54d1f1fb5555
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218859"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Samouczek: opracowywanie modułów IoT Edge C# przy użyciu kontenerów systemu Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule pokazano, jak za pomocą programu Visual Studio opracowywać kod C# i wdrażać go na urządzeniu z systemem Windows, na którym działa Azure IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS to ostatni kanał wydania obsługujący kontenery systemu Windows. Począwszy od wersji 1,2, kontenery systemu Windows nie są obsługiwane. Rozważ użycie lub przechodzenie do [IoT Edge dla systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md) w celu uruchomienia IoT Edge na urządzeniach z systemem Windows.

Za pomocą modułów Azure IoT Edge można wdrażać kod, który implementuje logikę biznesową bezpośrednio na urządzeniach IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na zestawie C# SDK.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Moduł wysyła komunikaty do strumienia tylko wtedy, gdy temperatura przekracza określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób tworzenia modułu w języku C# za pomocą programu Visual Studio 2019, a następnie wdrażania go na urządzeniu z systemem Windows. Jeśli tworzysz moduły przy użyciu kontenerów systemu Linux, przejdź do pozycji [opracowywanie modułów w języku C# IoT Edge przy użyciu kontenerów systemu Linux](tutorial-csharp-module.md) .

Aby poznać opcje tworzenia i wdrażania modułów języka C# przy użyciu kontenerów systemu Windows, zapoznaj się z poniższą tabelą:

| C# | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; i &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 — programowanie | ![Opracowywanie modułów języka C# dla WinAMD64 w Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Opracowywanie modułów języka C# dla WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| Windows AMD64 Debug |   | ![Debugowanie modułów języka C# dla programu WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

Przed rozpoczęciem pracy z tym samouczkiem Skonfiguruj środowisko programistyczne, postępując zgodnie z instrukcjami zawartymi w samouczku [tworzenie IoT Edge modułów przy użyciu kontenerów systemu Windows](tutorial-develop-for-windows.md) . Po jego zakończeniu środowisko będzie zawierać następujące wymagania wstępne:

* Bezpłatna lub Standardowa usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.
* [Urządzenie z systemem Windows, na którym działa Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Rejestr kontenerów, taki jak [Azure Container Registry](../container-registry/index.yml).
* [Program Visual Studio 2019](/visualstudio/install/install-visual-studio)został skonfigurowany przy użyciu rozszerzenia [narzędzi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* Program [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)skonfigurowany do uruchamiania kontenerów systemu Windows.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15,7 lub nowsza), Pobierz i Zainstaluj Azure IoT Edge narzędzia dla programu Visual Studio 2017 z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

W tej sekcji utworzysz projekt modułu IoT Edge za pomocą programu Visual Studio i rozszerzenia narzędzi Azure IoT Edge Tools. Po utworzeniu szablonu projektu należy dodać nowy kod, aby moduł odfiltruje komunikaty na podstawie ich raportowanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Narzędzia Azure IoT Edge zawierają szablony projektów dla wszystkich obsługiwanych języków modułów IoT Edge w programie Visual Studio. Te szablony mają wszystkie pliki i kod, które są potrzebne do wdrożenia modułu roboczego na potrzeby testowania IoT Edge. Mogą także zapewnić punkt wyjścia do dostosowywania ich przy użyciu własnej logiki biznesowej.

1. Otwórz program Visual Studio 2019, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W okienku **Utwórz nowy projekt** Wyszukaj **IoT Edge** a następnie na liście wyników wybierz projekt **Azure IoT Edge (Windows amd64)** .

   ![Zrzut ekranu przedstawiający okienko IoT Edge "Utwórz nowy projekt".](./media/tutorial-csharp-module-windows/new-project.png)

1. Wybierz opcję **Dalej**.

    Zostanie otwarte okienko **Konfiguruj nowy projekt** .

   ![Zrzut ekranu przedstawiający okienko "Konfigurowanie nowego projektu".](./media/tutorial-csharp-module-windows/configure-project.png)

1. W okienku **Konfiguruj nowy projekt** Zmień nazwę projektu i rozwiązania na bardziej opisową, taką jak **CSharpTutorialApp**. 

1. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

   Zostanie otwarte okienko **Dodawanie modułu** .

   ![Zrzut ekranu przedstawiający okienko "Dodawanie modułu" w celu skonfigurowania projektu.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Na stronie **Konfiguruj nowy projekt** wykonaj następujące czynności:

   a. W okienku po lewej stronie wybierz szablon **modułu C#** .  
   b. W polu **Nazwa modułu** wprowadź **CSharpModule**.  
   c. W polu **adres URL repozytorium** Zastąp wartość **localhost: 5000** wartością **serwera logowania** z usługi Azure Container Registry w następującym formacie: `<registry name>.azurecr.io/csharpmodule`

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

Kod modułu domyślnego odbiera komunikaty w kolejce wejściowej i przekazuje je za pomocą kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetworzył komunikaty na brzegu przed przekazaniem ich do centrum IoT. Zaktualizuj moduł, aby przeanalizować dane temperatury w każdym komunikacie i wysłać komunikat do centrum IoT tylko wtedy, gdy temperatura przekroczy określony próg.

1. W programie Visual Studio wybierz pozycję **CSharpModule**  >  **program. cs**.

1. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Dodaj zmienną **temperatureThreshold** do klasy **program** po zmiennej licznika. Zmienna temperatureThreshold ustawia wartość, którą pomiar temperatury musi przekroczyć, aby dane były wysyłane do centrum IoT Hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Dodaj klasy **MessageBody**, **Machine** i **otoczenia** do klasy **program** po deklaracjach zmiennych. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

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

1. Wyszukaj metodę **init** . Ta metoda tworzy i konfiguruje obiekt **ModuleClient** , który umożliwia modułowi łączenie się z lokalnymi Azure IoT Edge środowiska uruchomieniowego w celu wysyłania i odbierania komunikatów. Kod rejestruje również wywołanie zwrotne, aby odbierać komunikaty z Centrum IoT Edge za pośrednictwem punktu końcowego **INPUT1** .

   Zastąp całą metodę init następującym kodem:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Ta zaktualizowana metoda init nadal konfiguruje połączenie do środowiska uruchomieniowego IoT Edge z ModuleClient, ale również dodaje nowe funkcje. Odczytuje odpowiednie właściwości sznurka modułu, aby pobrać wartość **temperatureThreshold** . Następnie tworzy wywołanie zwrotne, które nasłuchuje wszelkich przyszłych aktualizacji odpowiednich właściwości sieci splotów modułu. Za pomocą tego wywołania zwrotnego można zdalnie aktualizować próg temperatury w module, a zmiany zostaną włączone do modułu.

   Zaktualizowana metoda init również zmienia istniejącą metodę **SetInputMessageHandlerAsync** . W przykładowym kodzie komunikaty przychodzące na *INPUT1* są przetwarzane przy użyciu funkcji *PipeMessage* , ale chcemy zmienić to, aby użyć funkcji *FilterMessages* , którą utworzymy w poniższych krokach.

1. Dodaj nową metodę **onDesiredPropertiesUpdate** do klasy **program** . Ta metoda odbiera aktualizacje odpowiednich właściwości z sznurka modułu i aktualizuje zmienną **temperatureThreshold** w celu dopasowania. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

1. Usuń przykładową metodę **PipeMessage** i Zastąp ją nową metodą **FilterMessages** . Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Filtruje komunikaty, które raportują temperatury poniżej progu temperatury, który jest ustawiany za pośrednictwem sznurka modułu. Metoda dodaje również właściwość **MessageType** do komunikatu z wartością ustawioną na **alert**.

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

1. Zapisz plik *Program.cs*.

1. Otwórz *deployment.template.js* pliku w rozwiązaniu IoT Edge. Ten plik zawiera informacje o agencie IoT Edge, które moduły wdrożyć i instruują Centrum IoT Edge, jak kierować komunikaty między nimi. Oto moduły do wdrożenia to **SimulatedTemperatureSensor** i **CSharpModule**.

1. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw poniższą zawartość w formacie JSON w dolnej części `modulesContent` sekcji po **$edgeHubj** sznurka modułu:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Zrzut ekranu przedstawiający dwuosiowy moduł dodawany do szablonu wdrożenia.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Zapisz plik *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod **CSharpModule** , aby odfiltrować komunikaty, w przypadku których zgłoszona temperatura maszyny jest mniejsza niż akceptowalna wartość progowa. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

1. Użyj poniższego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Użyj nazwy użytkownika, hasła i serwera logowania z usługi Azure Container Registry. Te wartości można pobrać z sekcji **klucze dostępu** rejestru w Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlone ostrzeżenie o zabezpieczeniach, które zaleca korzystanie z programu `--password-stdin` . Chociaż zalecamy to najlepsze rozwiązanie w zakresie scenariuszy produkcyjnych, to poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz [informacje dotyczące logowania do platformy Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Kompiluj i wypchnij

1. W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1** i, ponieważ tworzysz moduł systemu Windows, rozszerzenie powinno mieć wartość **Windows. amd64**.

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

1. Wyświetlanie komunikatów, które docierają do centrum IoT Hub. Dostarczenie komunikatów może chwilę potrwać, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Zmiany w kodzie CSharpModule muszą czekać na 25 stopni przed wysłaniem komunikatów. Kod dodaje również **alert** typu komunikat do wszystkich komunikatów, które docierają do tego progu temperatury.

   ![Zrzut ekranu przedstawiający okno dane wyjściowe zawierające komunikaty docierające do centrum IoT Hub.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj sznurek modułu

Za pomocą sznurka modułu CSharpModule można ustawić próg temperatury na 25 stopni. Możesz użyć sznurka modułu, aby zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Otwórz *deployment.windows-amd64.js* pliku. 

   *Nie* otwieraj pliku *Deployment. Template* . Jeśli manifest wdrożenia nie jest widoczny w pliku *konfiguracji* w Eksplorator rozwiązań, zaznacz ikonę **Pokaż wszystkie pliki** na pasku narzędzi Eksplorator rozwiązań.

1. Poszukaj CSharpModuleej przędzy i zmień wartość parametru **temperatureThreshold** na nową temperaturę od 5 do 10 stopni wyższą od najnowszej zgłoszonej temperatury.

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
