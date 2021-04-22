---
title: Samouczek — tworzenie modułu języka C# dla systemu Linux przy użyciu Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge kodem C# i wdrożyć go na urządzeniu z IoT Edge Linux.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e24c04152911f976907e6bdc433c33e035ade639
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874733"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Samouczek: tworzenie modułu języka C# IoT Edge przy użyciu kontenerów systemu Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Użyj Visual Studio Code, aby opracowywać kod w języku C# i wdrażać go na urządzeniu z Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego w ramach przewodnika Szybki start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi IoT Edge opartego na zestawie .NET Core SDK 2.1 przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku pokazano, jak opracować moduł w języku **C#** przy **użyciu** Visual Studio Code i wdrożyć go na IoT Edge urządzeniu. Jeśli opracowujesz moduły przy użyciu kontenerów systemu Windows, przejdź do witryny Develop a C# IoT Edge using Windows containers (Tworzenie modułu języka [C# przy użyciu kontenerów systemu Windows).](tutorial-csharp-module-windows.md)

W poniższej tabeli przedstawiono opcje tworzenia i wdrażania modułów języka C# przy użyciu kontenerów systemu Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Moduły języka C# dla systemu LinuxAMD64 w VS Code](./media/tutorial-c-module/green-check.png) | ![Moduły języka C# dla systemu LinuxAMD64 w Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Moduły języka C# dla systemu LinuxARM32 w VS Code](./media/tutorial-c-module/green-check.png) | ![Moduły języka C# dla systemu LinuxARM64 w Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Obsługa urządzeń z systemem Linux ARM64 jest dostępna w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać więcej informacji, zobacz [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Tworzenie](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)i debugowanie modułów arm64 w Visual Studio Code (wersja zapoznawcza).

Przed rozpoczęciem tego samouczka należy przejść przez poprzedni samouczek, aby skonfigurować środowisko projektowe, opracowywać moduł IoT Edge [przy użyciu kontenerów systemu Linux.](tutorial-develop-for-linux.md) Po ukończeniu tego samouczka powinny już być spełnione następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Urządzenie z systemem Azure IoT Edge z kontenerami systemu Linux. Aby skonfigurować urządzenie z systemem [Linux](quickstart-linux.md) lub urządzenie z systemem Windows, możesz użyć [przewodników Szybki start.](quickstart.md)
* Rejestr kontenerów, taki [jak Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowane przy użyciu [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Program Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

Aby ukończyć te samouczki, przygotuj następujące dodatkowe wymagania wstępne na komputerze dewelopera:

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt IoT Edge dla języka C# przy użyciu Visual Studio Code i Azure IoT Tools rozszerzenia. Po utworzeniu szablonu projektu dodaj nowy kod, aby moduł odfiltrował komunikaty na podstawie ich zgłaszanych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W Visual Studio Code wybierz pozycję **Wyświetl**  >  **paletę poleceń,** aby otworzyć VS Code paletę poleceń.

2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Nazwij moduł **CSharpModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp **wartość localhost:5000** wartością **serwera logowania** z rejestru kontenerów platformy Azure. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<registry name\> .azurecr.io/csharpmodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **Klucze dostępu** rejestru kontenerów platformy Azure.

Rozszerzenie IoT Edge próbuje ściągnąć poświadczenia rejestru kontenerów z platformy Azure i wypełnić je w pliku środowiska. Sprawdź, czy twoje poświadczenia są już dołączone. Jeśli nie, dodaj je teraz:

1. W eksploratorze programu VS Code otwórz plik **env**.
2. Zaktualizuj pola przy użyciu wartości **nazwy użytkownika** **i hasła** z rejestru kontenerów platformy Azure.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybieranie architektury docelowej

Obecnie można Visual Studio Code moduły języka C# dla urządzeń z systemami Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, dla której ma być wybierane każde rozwiązanie, ponieważ kontener jest budowy i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj pozycję **Azure IoT Edge:** Ustaw domyślną platformę docelową dla rozwiązania edge lub wybierz ikonę skrótu na pasku bocznym w dolnej części okna.

2. Na palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej z systemem Ubuntu jako urządzenia IoT Edge, więc zachowaj domyślną **amd64.**

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze VS Code otwórz **moduły**  >  **CSharpModule**  >  **Program.cs.**

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj zmienną **temperatureThreshold** do klasy **Program**. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj klasy **MessageBody**, **Machine**, i **Ambient** do klasy **Program**. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

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

5. Znajdź **funkcję Init.** Ta funkcja tworzy i konfiguruje obiekt **ModuleClient,** co umożliwia modułowi łączenie się z lokalnym środowiskiem Azure IoT Edge w celu wysyłania i odbierania komunikatów. Po utworzeniu elementu **ModuleClient** kod odczytuje wartość **temperatureThreshold** z żądanych właściwości bliźniaczej reprezentacji modułu. Ten kod rejestruje wywołanie zwrotne w celu odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę **SetInputMessageHandlerAsync** nową metodą i dodaj metodę **SetDesiredPropertyUpdateCallbackAsync** dla aktualizacji do żądanych właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Dodaj metodę **onDesiredPropertiesUpdate** do klasy **Program**. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

7. Zastąp metodę **PipeMessage** metodą **FilterMessages**. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**.

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
                using (var filteredMessage = new Message(messageBytes))
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

9. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

10. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **modulesContent** po bliźniaczej reprezentacji modułu **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-csharp-module/module-twin.png)

11. Zapisz plik deployment.template.json.

## <a name="build-and-push-your-module"></a>Kompilowanie i wypychanie modułu

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod domoduły CSharpModule. Nowy kod filtruje komunikaty, w których zgłoszona temperatura maszyny jest w granicach akceptowalnych limitów. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz VS Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **terminal.**

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z **sekcji Klucze dostępu** rejestru w Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlony ostrzeżenie o zabezpieczeniach z rekomendacją użycia polecenia `--password-stdin` . Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje [dotyczące logowania do platformy Docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. W eksploratorze VS Code kliknij prawym przyciskiem myszy plik **deployment.template.js** a następnie wybierz polecenie **Skompilowanie** i wypychanie IoT Edge rozwiązania.

   Polecenie kompilowania i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera pełny manifest wdrożenia, zbudowany na podstawie informacji w szablonie wdrożenia i innych plikach rozwiązania. Po drugie jest uruchamiany `docker build` w celu skompilowania obrazu kontenera na podstawie pliku dockerfile odpowiedniego dla architektury docelowej. Następnie zostanie uruchomiony `docker push` program , aby wypchnąć repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut za pierwszym razem, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Użyj eksploratora Visual Studio Code i rozszerzenia Azure IoT Tools, aby wdrożyć projekt modułu na IoT Edge urządzeniu. Masz już manifest wdrożenia przygotowany dla Twojego scenariusza, plik **deployment.amd64.jsw** pliku w folderze config. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, IoT Edge urządzenie jest uruchomione.

1. W eksploratorze Visual Studio Code w sekcji **Azure IoT Hub** rozwiń listę  Urządzenia, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.amd64.jsw** **folderze config,** a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge.** Nie używaj pliku deployment.template.json.

4. W obszarze urządzenia **rozwiń listę Modułów,** aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **CSharpModule** uruchomiony wraz z modułem **SimulatedTemperatureSensor** oraz **modułem $edgeAgent** i **$edgeHub**.

    Uruchomienie modułów może potrwać kilka minut. Środowisko IoT Edge musi odebrać nowy manifest wdrożenia, ściągnąć obrazy modułów ze środowiska uruchomieniowego kontenera, a następnie uruchomić każdy nowy moduł.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

1. W eksploratorze Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz polecenie Rozpocznij monitorowanie **wbudowanego punktu końcowego zdarzeń.**

2. Wyświetl komunikaty przychodzące do IoT Hub. Odbieranie komunikatów może zająć trochę czasu, ponieważ urządzenie IoT Edge musi odebrać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule poczekają, aż temperatura maszyny osiągnie 25 stopni przed wysłaniem komunikatów. Dodaje również typ komunikatu **Alert do** wszystkich komunikatów, które osiągają ten próg temperatury.

   ![Wyświetlanie komunikatów odbieranych IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytowanie bliźniaczej reprezentacji modułu

Umyliśmy bliźniaczej reprezentacji modułu CSharpModule w manifeście wdrożenia, aby ustawić próg temperatury na 25 stopni. Bliźniaczej reprezentacji modułu możesz użyć do zmiany funkcjonalności bez konieczności aktualizowania kodu modułu.

1. W Visual Studio Code rozwiń szczegóły w obszarze IoT Edge, aby wyświetlić uruchomione moduły.

2. Kliknij prawym przyciskiem **myszy moduł CSharpModule** i wybierz polecenie **Edytuj bliźniacze reprezentacji modułu.**

3. Znajdź **temperatureThreshold** we odpowiednich właściwościach. Zmień jej wartość na nową temperaturę o 5 stopni na 10 stopni wyższą niż najnowsza zgłoszona temperatura.

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w okienku edytowania bliźniaczej reprezentacji modułu i wybierz pozycję **Zaktualizuj bliźniacze reprezentacji modułu**.

6. Monitorowanie przychodzących komunikatów wysyłanych z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięciu nowego progu temperatury.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure użyte w tym artykule, aby uniknąć nalicznych opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge.

Możesz przejść do kolejnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge może ułatwić wdrażanie usług w chmurze platformy Azure w celu przetwarzania i analizowania danych na brzegu sieci.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)