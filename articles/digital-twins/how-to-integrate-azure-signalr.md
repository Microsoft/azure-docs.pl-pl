---
title: Integracja z usługą Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Zobacz, jak przesyłać dane telemetryczne usługi Azure Digital bliźniaczych reprezentacji do klientów za pomocą usługi Azure Signal
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4b0e0bd38c8bb9ea1d2331a65fc891e157971eef
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495851"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrowanie usługi Azure Digital bliźniaczych reprezentacji z usługą Azure Signal Service

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [usługą Azure Signal Service](../azure-signalr/signalr-overview.md).

Rozwiązanie opisane w tym artykule umożliwi wypychanie danych telemetrycznych cyfrowych sznurów do podłączonych klientów, takich jak pojedyncza strona sieci Web lub aplikacja mobilna. W związku z tym klienci są aktualizacją metrykami i Stanami w czasie rzeczywistym z urządzeń IoT, bez konieczności sondowania serwera ani przesyłania nowych żądań HTTP do aktualizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne, które należy wykonać przed kontynuowaniem:

* Przed integracją rozwiązania z usługą Azure Signal Service w tym artykule należy wykonać samouczek Digital bliźniaczych reprezentacji na platformie Azure [_**: łączenie kompleksowego rozwiązania**_](tutorial-end-to-end.md), ponieważ ta procedura obejmuje kompilację. Ten samouczek przeprowadzi Cię przez proces konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które współpracuje z wirtualnym urządzeniem IoT w celu wyzwolenia aktualizacji cyfrowych sznurów. Ta procedura zapewnia połączenie tych aktualizacji z przykładową aplikacją internetową przy użyciu usługi Azure Signal Service.
    - Wymagana jest nazwa **tematu siatki zdarzeń** utworzonego w samouczku.
* Na maszynie zainstalowano [**Node.js**](https://nodejs.org/) .

Możesz też zalogować się do [Azure Portal](https://portal.azure.com/) przy użyciu konta platformy Azure.

## <a name="solution-architecture"></a>Architektura rozwiązania

Do usługi Azure Digital bliźniaczych reprezentacji zostanie dołączana usługa Azure Signal Service za pomocą poniższej ścieżki. Sekcje A, B i C na diagramie są pobierane z diagramu architektury [kompleksowego, wymaganego samouczka](tutorial-end-to-end.md). w tym instruktażu utworzysz tę opcję, dodając sekcję D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Pobierz przykładowe aplikacje

Najpierw pobierz wymagane przykładowe aplikacje. Wymagane są obie następujące czynności:
* [**Przykłady kompleksowej bliźniaczych reprezentacjii na platformie Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Ten przykład zawiera *AdtSampleAppą* dwie usługi Azure Functions do przenoszenia danych wokół wystąpienia usługi Azure Digital bliźniaczych reprezentacji (informacje o tym scenariuszu można uzyskać bardziej szczegółowo w [*samouczku: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)). Zawiera również przykładową aplikację *DeviceSimulator* , która symuluje urządzenie IoT, generując nową wartość temperatury co sekundę. 
    - Przejdź do linku przykładowego i naciśnij przycisk *Pobierz plik zip* , aby pobrać kopię przykładu do maszyny jako _**Azure_Digital_Twins_end_to_end_samples.zip**_. Rozpakuj folder.
* [**Przykład aplikacji sieci Web do integracji sygnałów**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): jest to przykładowa aplikacja sieci Web z reakcję, która będzie korzystać z danych telemetrycznych usługi Azure Digital bliźniaczych reprezentacji z usługi Azure Signal Service.
    -  Przejdź do linku przykładowego i naciśnij przycisk *Pobierz plik zip* , aby pobrać kopię przykładu do maszyny jako _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Rozpakuj folder.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Pozostaw otwarte okno przeglądarki do Azure Portal, ponieważ zostanie ono użyte ponownie w następnej sekcji.

## <a name="configure-and-run-the-azure-functions-app"></a>Konfigurowanie i uruchamianie aplikacji Azure Functions

W tej sekcji zostaną skonfigurowane dwie usługi Azure Functions:
* **negocjowanie** — funkcja wyzwalacza http. Używa powiązania danych wejściowych *SignalRConnectionInfo* do generowania i zwracania prawidłowych informacji o połączeniu.
* **Broadcast** — funkcja wyzwalacza [Event Grid](../event-grid/overview.md) . Odbiera dane telemetryczne usługi Azure Digital bliźniaczych reprezentacji w usłudze Event Grid i używa powiązania danych wyjściowych wystąpienia *sygnalizującego* utworzonego w poprzednim kroku, aby emitować komunikat do wszystkich połączonych aplikacji klienckich.

Najpierw przejdź do przeglądarki, w której jest otwarta Azure Portal i wykonaj następujące kroki, aby uzyskać **Parametry połączenia** dla skonfigurowanego wystąpienia sygnalizującego. Będzie ona potrzebna do skonfigurowania funkcji.
1. Upewnij się, że wdrożone wcześniej wystąpienie usługi sygnalizującego zostało utworzone pomyślnie. W tym celu można wyszukać jego nazwę w polu wyszukiwania w górnej części portalu. Wybierz wystąpienie, aby je otworzyć.

1. Wybierz pozycję **klucze** z menu wystąpienie, aby wyświetlić parametry połączenia dla wystąpienia usługi sygnalizującego.

1. Wybierz ikonę, aby skopiować podstawowe parametry połączenia.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Następnie uruchom program Visual Studio (lub inny wybrany edytor kodu) i Otwórz rozwiązanie Code w folderze *Azure_Digital_Twins_end_to_end_samples > ADTSampleApp* . Następnie wykonaj następujące kroki, aby utworzyć funkcje:

1. Utwórz nową klasę Sharp języka C# o nazwie **SignalRFunctions.cs** w projekcie *SampleFunctionsApp* .

1. Zastąp zawartość pliku klasy następującym kodem:

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. W oknie *konsola Menedżera pakietów* programu Visual Studio lub dowolnym oknie poleceń na komputerze w folderze *Azure_Digital_Twins_end_to_end_samples \adtsampleapp\samplefunctionsapp* Uruchom następujące polecenie, aby zainstalować `SignalRService` pakiet NuGet w projekcie:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Powinno to rozwiązać wszelkie problemy zależności w klasie.

Następnie opublikuj funkcję na platformie Azure, korzystając z procedury opisanej w [sekcji *publikowanie aplikacji* ](tutorial-end-to-end.md#publish-the-app) w samouczku *łączenie kompleksowego rozwiązania* . Możesz opublikować ją w tej samej aplikacji usługi App Service/Function, która została użyta w kompleksowym samouczku Ignoruj lub utworzyć nową, ale możesz użyć tego samego, aby zminimalizować duplikowanie. Ponadto Ukończ publikowanie aplikacji, wykonując następujące czynności:
1. Zbierz **adres URL punktu końcowego protokołu HTTP**funkcji *Negotiate* . W tym celu przejdź do strony [aplikacji funkcji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal i wybierz z listy aplikację funkcji. W menu aplikacji wybierz pozycję *funkcje* i wybierz funkcję *Negocjuj* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

    Trafij *adres URL funkcji Get* i skopiuj wartość **do _/API_ (nie Uwzględniaj ostatnich _/Negotiate?_)**. Zostanie ona użyta później.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

1. Na koniec Dodaj **Parametry połączenia** usługi Azure Signal from wcześniej do ustawień aplikacji funkcji, korzystając z następującego polecenia interfejsu CLI platformy Azure. Polecenie można uruchomić w [Azure Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)interfejs wiersza polecenia platformy Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Dane wyjściowe tego polecenia wyświetla wszystkie ustawienia aplikacji skonfigurowane dla funkcji platformy Azure. Znajdź `AzureSignalRConnectionString` u dołu listy, aby sprawdzić, czy została dodana.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

#### <a name="connect-the-function-to-event-grid"></a>Połącz funkcję z Event Grid

Następnie Zasubskrybuj funkcję *emisji* Azure w **temacie Event Grid** utworzonym w [*samouczku: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md) Ignoruj. Dzięki temu dane telemetryczne mogą być przesyłane z przędzy *thermostat67ej* za pośrednictwem usługi Event Grid do funkcji, która może być emitowana do wszystkich klientów.

W tym celu utworzysz **subskrypcję Event gridową** z tematu usługi Event Grid do funkcji *emisji* platformy Azure jako punktu końcowego.

W [Azure Portal](https://portal.azure.com/)przejdź do tematu usługi Event Grid, wyszukując jego nazwę na górnym pasku wyszukiwania. Wybierz pozycję *+ Subskrypcja zdarzeń*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

Na stronie *Tworzenie subskrypcji zdarzeń* Wypełnij pola w następujący sposób (pola wypełnione domyślnie nie są wymienione):
* *szczegóły*  >  subskrypcji zdarzeń **Nazwa**: nadaj nazwę subskrypcji zdarzenia.
* *szczegóły*  >  punktu końcowego **Typ punktu końcowego**: wybierz pozycję *Funkcja platformy Azure* z opcji menu.
* *szczegóły*  >  punktu końcowego **Punkt końcowy**: naciśnij link *Wybierz punkt końcowy* . Spowoduje to otwarcie okna *Wybierz funkcję platformy Azure* :
    - Wypełnij swoją **subskrypcję**, **grupę zasobów**, **funkcję App** and **Function** (*Broadcast*). Niektóre z tych elementów mogą być wypełniane automatycznie po wybraniu subskrypcji.
    - Kliknij przycisk **Potwierdź wybór**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

Wróć na stronę *Tworzenie subskrypcji zdarzeń* , kliknij przycisk **Utwórz**.

## <a name="configure-and-run-the-web-app"></a>Konfigurowanie i uruchamianie aplikacji sieci Web

W tej sekcji zobaczysz wynik działania. Najpierw utworzysz **przykładową aplikację symulowanego urządzenia** , która wysyła dane telemetryczne za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Następnie skonfigurujesz **przykładową aplikację internetową klienta** w celu nawiązania połączenia z ustawionym przepływem usługi Azure Signal. Następnie powinno być możliwe wyświetlenie danych aktualizujących przykładową aplikację internetową w czasie rzeczywistym.

### <a name="run-the-device-simulator"></a>Uruchamianie symulatora urządzeń

W ramach kompleksowego wymagania wstępnego samouczka [skonfigurowano symulator urządzenia](tutorial-end-to-end.md#configure-and-run-the-simulation) do wysyłania danych za pomocą IoT Hub i do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Teraz wystarczy uruchomić projekt symulatora znajdujący się w *Azure_Digital_Twins_end_to_end_samples > DeviceSimulator > DeviceSimulator. sln*. Jeśli używasz programu Visual Studio, możesz otworzyć projekt, a następnie uruchomić go za pomocą tego przycisku na pasku narzędzi:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

Zostanie otwarte okno konsoli zawierające symulowane komunikaty telemetryczne dotyczące temperatury. Są one wysyłane za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, w którym są następnie wybierane przez usługę Azure Functions i sygnalizujący.

Nie musisz wykonywać żadnych innych czynności w tej konsoli, ale pozostaw to działanie w trakcie wykonywania następnych kroków.

### <a name="configure-the-sample-client-web-app"></a>Konfigurowanie przykładowej aplikacji internetowej klienta

Następnie skonfiguruj **przykład aplikacji sieci Web do integracji sygnalizującej** , wykonując następujące kroki:
1. Korzystając z programu Visual Studio lub dowolnego dowolnego edytora kodu, Otwórz niespakowany folder _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ pobrany w sekcji [*Pobieranie przykładowych aplikacji*](#download-the-sample-applications) .

1. Otwórz plik *src/App.js* i Zastąp adres URL przy `HubConnectionBuilder` użyciu adresu URL punktu końcowego protokołu HTTP, **negotiate** który został zapisany wcześniej:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. W *wierszu polecenia dewelopera* programu Visual Studio lub dowolnym oknie polecenia na maszynie przejdź do folderu *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* . Uruchom następujące polecenie, aby zainstalować pakiety węzła zależnego:

    ```cmd
    npm install
    ```

Następnie ustaw uprawnienia w aplikacji funkcji w Azure Portal:
1. Na stronie [aplikacje funkcji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal wybierz wystąpienie aplikacji funkcji.
1. Przewiń w dół w menu wystąpienie i wybierz pozycję *CORS*. Na stronie CORS Dodaj `http://localhost:3000` jako dozwolony punkt początkowy, wprowadzając go w pustym polu. Zaznacz pole wyboru *Włącz dostęp-kontrola-Zezwalaj-poświadczenia* i naciśnij przycisk *Zapisz*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

### <a name="see-the-results"></a>Zobacz wyniki

Aby wyświetlić wyniki w akcji, uruchom **przykład aplikacji sieci Web do integracji sygnałów**. Można to zrobić z dowolnego okna konsoli w Azure_Digital_Twins_SignalR_integration_web_app_sample lokalizacji *\src* , uruchamiając następujące polecenie:

```cmd
npm start
```

Spowoduje to otwarcie okna przeglądarki z uruchomioną przykładową aplikacją, która wyświetla miernik temperatury wizualizacji. Gdy aplikacja jest uruchomiona, należy zacząć wyświetlać wartości telemetryczne dla temperatury z symulatora urządzeń, który propaguje za pośrednictwem usługi Azure Digital bliźniaczych reprezentacji przez aplikację sieci Web w czasie rzeczywistym.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są już potrzebne, wykonaj następujące kroki, aby je usunąć. 

Korzystając z Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure, możesz usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Usunięcie grupy zasobów spowoduje również usunięcie...
* wystąpienie usługi Azure Digital bliźniaczych reprezentacji (z kompleksowego samouczka)
* Rejestracja Centrum IoT Hub i urządzenia Hub (od końca do końca)
* temat dotyczący siatki zdarzeń i skojarzonych subskrypcji
* Aplikacja Azure Functions, w tym wszystkie trzy funkcje i powiązane zasoby, takie jak magazyn
* wystąpienie usługi Azure Signaler

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Na koniec Usuń foldery Przykładowe projektu pobrane do komputera lokalnego (*Azure_Digital_Twins_end_to_end_samples.zip* i *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano konfigurowanie usługi Azure Functions with Signaler do emisji zdarzeń telemetrii Digital bliźniaczych reprezentacji platformy Azure do przykładowej aplikacji klienckiej.

Następnie Dowiedz się więcej o usłudze Azure Signal Service:
* [*Co to jest usługa Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Lub Przeczytaj więcej na temat uwierzytelniania usługi Azure sygnalizacyjnej przy użyciu Azure Functions:
* [*Uwierzytelnianie usługi Azure sygnalizujące*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
