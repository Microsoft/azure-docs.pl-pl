---
title: 'Samouczek: wdrażanie Azure Functions jako modułów — Azure IoT Edge'
description: W tym samouczku opracowywasz funkcję platformy Azure jako IoT Edge, a następnie wdrożysz ją na urządzeniu brzegowym.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 30abc4a5a1431800cef2bcbda6f5eeedf9a216a3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874643"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Samouczek: wdrażanie Azure Functions jako IoT Edge modułów

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Możesz użyć usługi Azure Functions, aby wdrożyć kod implementujący Twoją logikę biznesową bezpośrednio na urządzeniach usługi Azure IoT Edge. Ten samouczek zawiera tworzyć i wdrażać funkcję platformy Azure, która filtruje dane czujników na symulowanym urządzeniu IoT Edge danych. Użyj symulowanego urządzenia IoT Edge utworzonego w przewodnikach Szybki start. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj Visual Studio Code, aby utworzyć funkcję platformy Azure.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu VS Code i platformy Docker.
> * Wdrażanie modułu z rejestru kontenerów na urządzeniu usługi IoT Edge.
> * Wyświetlanie filtrowanych danych.

<center>

![Diagram — architektura samouczka: etap i wdrażanie modułu funkcji](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Funkcja platformy Azure generowana w tym samouczku filtruje dane dotyczące temperatury generowane przez urządzenie. Funkcja wysyła komunikaty nadrzędne do Azure IoT Hub tylko wtedy, gdy temperatura przekroczy określony próg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy przejść przez poprzedni samouczek, aby skonfigurować środowisko projektowe do tworzenia kontenerów dla systemu Linux: Tworzenie modułów IoT Edge przy użyciu [kontenerów systemu Linux.](tutorial-develop-for-linux.md) Ukończenie tego samouczka powinno wymagać następujących wymagań wstępnych:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Urządzenie z systemem Azure IoT Edge kontenerami systemu Linux. Przewodników Szybki start można użyć do skonfigurowania urządzenia z [systemem Linux](quickstart-linux.md) lub urządzenia z [systemem Windows.](quickstart.md)
* Rejestr kontenerów, taki [jak Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowane przy użyciu [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Program Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

Aby opracować moduł IoT Edge w programie Azure Functions, zainstaluj następujące dodatkowe wymagania wstępne na maszynie dewelopera:

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Narzędzia usługi Azure IoT dla programu Visual Studio Code, które zostało zainstalowane w ramach wymagań wstępnych, udostępnia możliwości zarządzania oraz pewne szablony kodu. W tej sekcji użyjemy Visual Studio Code, aby utworzyć IoT Edge, które zawiera funkcję platformy Azure.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania funkcji języka C#, który można dostosować przy użyciu własnego kodu.

1. Otwórz program Visual Studio Code na maszynie deweloperskiej.

2. Otwórz paletę VS Code poleceń, wybierając pozycję **Wyświetl**  >  **paletę poleceń.**

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie folderu | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **FunctionSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Azure Functions — C#**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CSharpFunction**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp **wartość localhost:5000** wartością **serwera logowania** z rejestru kontenerów platformy Azure. Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal. Ostateczny ciąg wygląda następująco: \<registry name\> .azurecr.io/CSharpFunction. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials&quot;></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

Rozszerzenie IoT Edge próbuje ściągnąć poświadczenia rejestru kontenerów z platformy Azure i wypełnić je w pliku środowiska. Sprawdź, czy twoje poświadczenia są już dołączone. Jeśli nie, dodaj je teraz:

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name=&quot;select-your-target-architecture&quot;></a>Wybieranie architektury docelowej

Obecnie można Visual Studio Code moduły języka C dla urządzeń z systemami Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, dla której mają być przeznaczone poszczególne rozwiązania, ponieważ kontener jest budowy i uruchamiany inaczej dla każdego typu architektury. Wartość domyślna to Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj pozycję **Azure IoT Edge:** Ustaw domyślną platformę docelową dla rozwiązania edge lub wybierz ikonę skrótu na pasku bocznym w dolnej części okna.

2. Na palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej z systemem Ubuntu jako urządzenia IoT Edge, więc zachowaj domyślną **amd64.**

### <a name=&quot;update-the-module-with-custom-code&quot;></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Dodajmy dodatkowy kod, aby moduł przetwarzał komunikaty na brzegu sieci przed ich przekazywaniem do IoT Hub.

1. W Visual Studio Code moduły   >  **CSharpFunction**  >  **CSharpFunction.cs.**

1. Zastąp zawartość pliku **CSharpFunction.cs** następującym kodem. Ten kod odbiera dane telemetrii dotyczące temperatury otoczenia i maszyny oraz przesyła dalej komunikat do usługi IoT Hub tylko wtedy, gdy temperatura maszyny przekracza zdefiniowaną wartość progową.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName(&quot;CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Zapisz plik.

## <a name="build-and-push-your-iot-edge-solution"></a>Kompilowanie i wypychanie IoT Edge aplikacji

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i zmodyfikowano **CSharpFunction** w celu odfiltrowania komunikatów z zgłoszoną temperaturą maszyny poniżej akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz VS Code zintegrowany terminal, wybierając pozycję **Wyświetl**  >  **terminal.**

2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może zostać wyświetlony ostrzeżenie o zabezpieczeniach z rekomendacją użycia polecenia `--password-stdin` . Chociaż to najlepsze rozwiązanie jest zalecane w przypadku scenariuszy produkcyjnych, jest ono poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz informacje [dotyczące logowania do platformy Docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

3. W eksploratorze VS Code kliknij prawym przyciskiem myszy pozycję **deployment.template.jsw** pliku, a następnie wybierz pozycję Skompilowanie i **wypychanie IoT Edge rozwiązania.**

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera pełny manifest wdrożenia, który jest zbudowany na podstawie informacji w szablonie wdrożenia i innych plikach rozwiązania. Po drugie jest uruchamiany w `docker build` celu skompilowania obrazu kontenera na podstawie pliku dockerfile odpowiedniego dla architektury docelowej. Następnie zostanie uruchomiony `docker push` program , aby wypchnąć repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut za pierwszym razem, ale jest szybszy przy następnym uruchomieniu poleceń.

## <a name="view-your-container-image"></a>Wyświetlanie obrazu kontenera

Program Visual Studio Code generuje komunikat o powodzeniu po wypchnięciu obrazu kontenera do rejestru kontenerów. Jeśli chcesz osobiście potwierdzić pomyślne zakończenie operacji, możesz wyświetlić obraz w rejestrze.

1. W witrynie Azure Portal przejdź do rejestru kontenerów platformy Azure.
2. Wybierz **pozycję Repozytoria.**
3. Na liście powinno zostać wyświetlone repozytorium **csharpfunction**. Wybierz to repozytorium, aby wyświetlić więcej szczegółów.
4. W sekcji **Tagi** powinien zostać wyświetlony tag **0.0.1-amd64**. Ten tag odzwierciedla wersję i platformę skompilowanego obrazu. Te wartości są ustawiane w pliku module.json w folderze CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Możesz użyć tej Azure Portal, aby wdrożyć moduł funkcji na urządzeniu IoT Edge, tak jak w przewodnikach Szybki start. Możesz również wdrażać i monitorować moduły z poziomu programu Visual Studio Code. W następujących sekcjach używane są narzędzia usługi Azure IoT dla programu Visual Studio Code, wymienione w wymaganiach wstępnych. Zainstaluj teraz rozszerzenie, jeśli jeszcze tego nie zrobiono.

1. W eksploratorze Visual Studio Code w sekcji **Azure IoT Hub** rozwiń listę  Urządzenia, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia.**

3. Przejdź do folderu rozwiązania, który zawiera moduł **CSharpFunction**. Otwórz folder konfiguracji, wybierz plik **deployment.amd64.js,** a następnie wybierz pozycję **Wybierz manifest wdrożenia usługi Edge.**

4. W obszarze urządzenia **rozwiń listę Modułów,** aby wyświetlić listę wdrożonych i uruchomionych modułów. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **CSharpFunction** uruchomiony wraz z modułem **SimulatedTemperatureSensor** oraz **modułem $edgeAgent** i **$edgeHub**.

    Wyświetlenie nowych modułów może zająć kilka chwil. Urządzenie usługi IoT Edge musi pobrać nowe informacje o wdrożeniu z usługi IoT Hub, uruchomić nowe kontenery i zgłosić stan usłudze IoT Hub.

   ![Wyświetlanie wdrożonych modułów w programie VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby wyświetlić wszystkie komunikaty przychodzące do centrum IoT Hub, uruchom polecenie **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** (Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzeń) w palecie poleceń.

Możesz również filtrować widok, aby wyświetlać wszystkie komunikaty przychodzące do centrum IoT Hub z określonego urządzenia. Kliknij prawym przyciskiem myszy urządzenie w sekcji **Azure IoT Hub urządzenia** i wybierz pozycję Rozpocznij **monitorowanie wbudowanego punktu końcowego zdarzenia**.

Aby zatrzymać monitorowanie komunikatów, uruchom polecenie **Azure IoT Hub: Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzeń** w palecie poleceń.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono moduł funkcji platformy Azure z kodem do filtrowania nieprzetworzonych danych generowanych przez urządzenie IoT Edge danych.

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Znajdowanie średnich przy użyciu okna ruchomego w usłudze Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
