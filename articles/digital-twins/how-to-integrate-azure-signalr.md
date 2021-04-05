---
title: Integracja z usługą Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Zobacz, jak przesyłać dane telemetryczne usługi Azure Digital bliźniaczych reprezentacji do klientów za pomocą usługi Azure Signal
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 89bd77c30ec52a72087598b86f22e85659fa1b0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102203899"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrowanie usługi Azure Digital bliźniaczych reprezentacji z usługą Azure Signal Service

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [usługą Azure Signal Service](../azure-signalr/signalr-overview.md).

Rozwiązanie opisane w tym artykule umożliwia wypychanie danych telemetrycznych z cyfrowego przędzy do podłączonych klientów, na przykład pojedynczej strony sieci Web lub aplikacji mobilnej. W związku z tym klienci są uaktualniani przy użyciu metryk i stanu w czasie rzeczywistym z urządzeń IoT, bez konieczności sondowania serwera ani przesyłania nowych żądań HTTP do aktualizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne, które należy wykonać przed kontynuowaniem:

* Przed integracją rozwiązania z usługą Azure Signal Service w tym artykule należy wykonać samouczek Digital bliźniaczych reprezentacji na platformie Azure [_**: łączenie kompleksowego rozwiązania**_](tutorial-end-to-end.md), ponieważ w tym artykule opisano kompilację. Ten samouczek przeprowadzi Cię przez proces konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które współpracuje z wirtualnym urządzeniem IoT w celu wyzwolenia aktualizacji cyfrowych sznurów. Ten artykuł zawiera instrukcje łączenia tych aktualizacji z przykładową aplikacją internetową przy użyciu usługi Azure Signal Service.

* W samouczku będą potrzebne następujące wartości:
  - Temat dotyczący siatki zdarzeń
  - Grupa zasobów
  - Nazwa aplikacji funkcji/usługi App Service
    
* Na maszynie będzie wymagane [**Node.js**](https://nodejs.org/) .

Należy również zalogować się do [Azure Portal](https://portal.azure.com/) przy użyciu konta platformy Azure.

## <a name="solution-architecture"></a>Architektura rozwiązania

Do usługi Azure Digital bliźniaczych reprezentacji zostanie dołączana usługa Azure Signal Service za pomocą poniższej ścieżki. Sekcje A, B i C na diagramie są pobierane z diagramu architektury dla [kompleksowego wymagania wstępnego samouczka](tutorial-end-to-end.md). W tym artykule dotyczącym wykonywania tej procedury utworzysz sekcję D w istniejącej architekturze.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu. Przedstawia dane przepływające z urządzenia do IoT Hub za pośrednictwem funkcji platformy Azure (strzałka B) do wystąpienia usługi Azure Digital bliźniaczych reprezentacji (sekcja A), a następnie za pośrednictwem Event Grid do innej funkcji platformy Azure do przetworzenia (strzałka C). Sekcja D zawiera dane przepływów z tego samego Event Grid w strzałce C do funkcji platformy Azure o nazwie &quot;broadcast&quot;. element &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure o nazwie &quot;Negotiate&quot;, a zarówno &quot;broadcast&quot;, jak i &quot;Negotiate&quot; komunikują się z urządzeniami komputerowymi." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Pobierz przykładowe aplikacje

Najpierw pobierz wymagane przykładowe aplikacje. Wymagane są obie następujące czynności:
* [**Przykłady kompleksowych bliźniaczych reprezentacji na platformie Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Ten przykład zawiera *AdtSampleApp* , który przechowuje dwie usługi Azure Functions do przenoszenia danych wokół wystąpienia usługi Azure Digital bliźniaczych reprezentacji (więcej informacji można znaleźć w [*samouczku: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)). Zawiera również przykładową aplikację *DeviceSimulator* , która symuluje urządzenie IoT, generując nową wartość temperatury co sekundę.
    - Jeśli przykład nie został jeszcze pobrany jako część samouczka w sekcji [*wymagania wstępne*](#prerequisites), przejdź do [linku](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) przykładowego i wybierz przycisk *Przeglądaj kod* pod tytułem. Spowoduje to przejście do repozytorium GitHub dla przykładów, które można pobrać jako *. Plik ZIP* , wybierając przycisk *Code (kod* ) i Pobierz plik *zip*.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Widok repozytorium Digital-bliźniaczych reprezentacji-Samples w witrynie GitHub. Wybrano przycisk kod, tworząc małe okno dialogowe, w którym jest wyróżniony przycisk Pobierz plik ZIP." lightbox="media/includes/download-repo-zip.png":::

    Spowoduje to pobranie kopii przykładowego repozytorium do maszyny jako **digital-twins-samples-master.zip**. Rozpakuj folder.
* [**Przykład aplikacji sieci Web do integracji sygnałów**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): jest to przykładowa aplikacja sieci Web z reakcję, która będzie korzystać z danych telemetrycznych usługi Azure Digital bliźniaczych reprezentacji z usługi Azure Signal Service.
    -  Przejdź do linku przykładowego i Użyj tego samego procesu pobierania, aby pobrać kopię przykładu do maszyny jako _**digitaltwins-signalr-webapp-sample-main.zip**_. Rozpakuj folder.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Pozostaw otwarte okno przeglądarki do Azure Portal, ponieważ zostanie ono użyte ponownie w następnej sekcji.

## <a name="publish-and-configure-the-azure-functions-app"></a>Publikowanie i Konfigurowanie aplikacji Azure Functions

W tej sekcji zostaną skonfigurowane dwie usługi Azure Functions:
* **negocjowanie** — funkcja wyzwalacza http. Używa powiązania danych wejściowych *SignalRConnectionInfo* do generowania i zwracania prawidłowych informacji o połączeniu.
* **Broadcast** — funkcja wyzwalacza [Event Grid](../event-grid/overview.md) . Odbiera dane telemetryczne usługi Azure Digital bliźniaczych reprezentacji w usłudze Event Grid i używa powiązania danych wyjściowych wystąpienia *sygnalizującego* utworzonego w poprzednim kroku, aby emitować komunikat do wszystkich połączonych aplikacji klienckich.

Uruchom program Visual Studio (lub inny wybrany edytor kodu) i Otwórz rozwiązanie Code w folderze *Digital-bliźniaczych reprezentacji-Samples-master > ADTSampleApp* . Następnie wykonaj następujące kroki, aby utworzyć funkcje:

1. W projekcie *SampleFunctionsApp* Utwórz nową klasę języka C# o nazwie **SignalRFunctions. cs**.

1. Zastąp zawartość pliku klasy następującym kodem:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. W oknie *konsola Menedżera pakietów* programu Visual Studio lub dowolnym oknie poleceń na maszynie przejdź do folderu *Digital-Twins-Samples-master\AdtSampleApp\SampleFunctionsApp*, a następnie uruchom następujące polecenie, aby zainstalować `SignalRService` pakiet NuGet w projekcie:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Powinno to rozwiązać wszelkie problemy zależności w klasie.

1. Opublikuj funkcję na platformie Azure, wykonując kroki opisane w [sekcji *publikowanie aplikacji*](tutorial-end-to-end.md#publish-the-app) w samouczku *łączenie kompleksowego rozwiązania* . Można go opublikować w tej samej aplikacji usługi App Service/Function, która została użyta w ramach [wymagania wstępnego](#prerequisites)samouczka "kompleksowe" lub utworzyć nowy, ale można użyć tego samego, aby zminimalizować duplikowanie. 

Następnie skonfiguruj funkcje do komunikacji z wystąpieniem usługi Azure Signal. Zacznij od zebrania **parametrów połączenia** wystąpienia sygnalizującego, a następnie dodaj go do ustawień aplikacji usługi Functions.

1. Przejdź do [Azure Portal](https://portal.azure.com/) i wyszukaj nazwę wystąpienia sygnalizującego na pasku wyszukiwania w górnej części portalu. Wybierz wystąpienie, aby je otworzyć.
1. Wybierz pozycję **klucze** z menu wystąpienie, aby wyświetlić parametry połączenia dla wystąpienia usługi sygnalizującego.
1. Wybierz ikonę *kopiowania* , aby skopiować podstawowe parametry połączenia.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym są wyświetlane strony klucze dla wystąpienia sygnalizującego. Ikona &quot;Kopiuj do schowka&quot; obok podstawowych parametrów połączenia jest wyróżniona." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Na koniec Dodaj **Parametry połączenia** z usługą Azure Signal do ustawień aplikacji funkcji, korzystając z następującego polecenia platformy Azure. Ponadto Zastąp symbole zastępcze nazwą swojej grupy zasobów i usługi App Service/ [App.](how-to-integrate-azure-signalr.md#prerequisites) Polecenie można uruchomić w [Azure Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli)interfejs wiersza polecenia platformy Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Dane wyjściowe tego polecenia wyświetla wszystkie ustawienia aplikacji skonfigurowane dla funkcji platformy Azure. Znajdź `AzureSignalRConnectionString` u dołu listy, aby upewnić się, że została dodana.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Fragment danych wyjściowych w oknie polecenia, pokazujący element listy o nazwie &quot;AzureSignalRConnectionString&quot;":::

#### <a name="connect-the-function-to-event-grid"></a>Połącz funkcję z Event Grid

Następnie Zasubskrybuj funkcję *Emituj* usługę Azure do **tematu usługi Event Grid** utworzonego w ramach [wymagań wstępnych samouczka](how-to-integrate-azure-signalr.md#prerequisites). Pozwoli to na przepływ danych telemetrycznych z thermostat67ch sieci za pośrednictwem tematu usługi Event Grid oraz do funkcji. W tym miejscu funkcja może emitować dane do wszystkich klientów.

W tym celu utworzysz **subskrypcję zdarzeń** z tematu usługi Event Grid do funkcji *emisji* platformy Azure jako punktu końcowego.

W [Azure Portal](https://portal.azure.com/)przejdź do tematu usługi Event Grid, wyszukując jego nazwę na górnym pasku wyszukiwania. Wybierz pozycję *+ Subskrypcja zdarzeń*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid subskrypcji zdarzeń":::

Na stronie *Tworzenie subskrypcji zdarzeń* Wypełnij pola w następujący sposób (pola wypełnione domyślnie nie są wymienione):
* *szczegóły*  >  subskrypcji zdarzeń **Nazwa**: nadaj nazwę subskrypcji zdarzenia.
* *szczegóły*  >  punktu końcowego **Typ punktu końcowego**: wybierz pozycję *Funkcja platformy Azure* z opcji menu.
* *szczegóły*  >  punktu końcowego **Punkt końcowy**: naciśnij link *Wybierz punkt końcowy* . Spowoduje to otwarcie okna *Wybierz funkcję platformy Azure* :
    - Wypełnij swoją **subskrypcję**, **grupę zasobów**, **funkcję App** and **Function** (*Broadcast*). Niektóre z tych elementów mogą być wypełniane automatycznie po wybraniu subskrypcji.
    - Kliknij przycisk **Potwierdź wybór**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Widok Azure Portal tworzenia subskrypcji zdarzeń. Powyższe pola są wypełnione, a przyciski &quot;Potwierdź wybór&quot; i &quot;Utwórz&quot; są wyróżnione.":::

Wróć na stronę *Tworzenie subskrypcji zdarzeń* , kliknij przycisk **Utwórz**.

W tym momencie na stronie *tematu Event Grid* powinny zostać wyświetlone dwie subskrypcje zdarzeń.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal widok dwóch subskrypcji zdarzeń na stronie tematu w usłudze Event Grid." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Konfigurowanie i uruchamianie aplikacji sieci Web

W tej sekcji zobaczysz wynik działania. Najpierw skonfiguruj **przykładową aplikację sieci Web klienta** , aby połączyć się z przepływem usługi Azure sygnalizującego, który został skonfigurowany. Następnie utworzysz **przykładową aplikację symulowanego urządzenia** , która wysyła dane telemetryczne za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Następnie zobaczysz przykładową aplikację internetową, aby zobaczyć symulowane dane urządzenia z aktualizacją przykładowej aplikacji sieci Web w czasie rzeczywistym.

### <a name="configure-the-sample-client-web-app"></a>Konfigurowanie przykładowej aplikacji internetowej klienta

Następnie skonfigurujesz przykładową aplikację internetową klienta. Zacznij od zebrania **adresu URL punktu końcowego protokołu HTTP** funkcji *Negotiate* , a następnie użyj go, aby skonfigurować kod aplikacji na maszynie.

1. Przejdź do strony [aplikacji funkcji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal i wybierz z listy aplikację funkcji. W menu aplikacji wybierz pozycję *funkcje* i wybierz funkcję *Negocjuj* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Widok Azure Portal aplikacji funkcji z wyróżnioną funkcją &quot;Functions&quot; w menu. Lista funkcji jest wyświetlana na stronie, a funkcja &quot;Negocjuj&quot; jest również wyróżniona.":::

1. Trafij *adres URL funkcji Get* i skopiuj wartość **do _/API_ (nie Uwzględniaj ostatnich _/Negotiate?_)**. Zostanie ona użyta w następnym kroku.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Widok Azure Portal funkcji &quot;Negotiate&quot;. Przycisk &quot;Pobierz adres URL funkcji&quot; jest wyróżniony, a część adresu URL od początku do &quot;/API&quot;":::

1. Korzystając z programu Visual Studio lub dowolnego dowolnego edytora kodu, Otwórz niespakowany folder _**digitaltwins-signaler-webapp-Sample-Main**_ pobrany w sekcji [*Pobieranie przykładowych aplikacji*](#download-the-sample-applications) .

1. Otwórz plik *src/App.js* i Zastąp adres URL funkcji przy `HubConnectionBuilder` użyciu adresu URL punktu końcowego http, który  został zapisany w poprzednim kroku:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. W *wierszu polecenia dewelopera* programu Visual Studio lub dowolnym oknie polecenia na maszynie przejdź do folderu *digitaltwins-SignalR-webapp-Sample-main\src* . Uruchom następujące polecenie, aby zainstalować pakiety węzła zależnego:

    ```cmd
    npm install
    ```

Następnie ustaw uprawnienia w aplikacji funkcji w Azure Portal:
1. Na stronie [aplikacje funkcji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal wybierz wystąpienie aplikacji funkcji.

1. Przewiń w dół w menu wystąpienie i wybierz pozycję *CORS*. Na stronie CORS Dodaj `http://localhost:3000` jako dozwolony punkt początkowy, wprowadzając go w pustym polu. Zaznacz pole wyboru *Włącz dostęp-kontrola-Zezwalaj-poświadczenia* i naciśnij przycisk *Zapisz*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Ustawienie mechanizmu CORS w usłudze Azure Function":::

### <a name="run-the-device-simulator"></a>Uruchamianie symulatora urządzeń

W ramach kompleksowego wymagania wstępnego samouczka [skonfigurowano symulator urządzenia](tutorial-end-to-end.md#configure-and-run-the-simulation) do wysyłania danych za pomocą IoT Hub i do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Teraz wystarczy uruchomić projekt symulatora znajdujący się w formacie *Digital-bliźniaczych reprezentacji-Samples-master > DeviceSimulator > DeviceSimulator. sln*. Jeśli używasz programu Visual Studio, możesz otworzyć projekt, a następnie uruchomić go za pomocą tego przycisku na pasku narzędzi:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Przycisk Start programu Visual Studio (projekt DeviceSimulator)":::

Zostanie otwarte okno konsoli zawierające symulowane komunikaty telemetryczne dotyczące temperatury. Są one wysyłane za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji, w którym są następnie wybierane przez usługę Azure Functions i sygnalizujący.

Nie musisz wykonywać żadnych innych czynności w tej konsoli, ale pozostawić ją uruchomiona, gdy wykonasz następny krok.

### <a name="see-the-results"></a>Zobacz wyniki

Aby wyświetlić wyniki w akcji, uruchom **przykład aplikacji sieci Web do integracji sygnałów**. Można to zrobić z dowolnego okna konsoli w lokalizacji *digitaltwins-SignalR-webapp-Sample-main\src* , uruchamiając następujące polecenie:

```cmd
npm start
```

Spowoduje to otwarcie okna przeglądarki z uruchomioną przykładową aplikacją, która wyświetla miernik temperatury wizualizacji. Gdy aplikacja jest uruchomiona, należy zacząć wyświetlać wartości telemetryczne dla temperatury z symulatora urządzeń, który propaguje za pośrednictwem usługi Azure Digital bliźniaczych reprezentacji przez aplikację sieci Web w czasie rzeczywistym.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Wyciąg z przykładowej aplikacji sieci Web klienta przedstawiającej miernik temperatury wizualizacji. Odzwierciedlona temperatura to 67,52":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są już potrzebne, wykonaj następujące kroki, aby je usunąć. 

Korzystając z Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure, możesz usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) . Usunięcie grupy zasobów spowoduje również usunięcie...
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

Na koniec Usuń foldery Przykładowe projektu pobrane do komputera lokalnego (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* i ich niespakowanego odpowiedniki).

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano konfigurowanie usługi Azure Functions with Signaler do emisji zdarzeń telemetrii Digital bliźniaczych reprezentacji platformy Azure do przykładowej aplikacji klienckiej.

Następnie Dowiedz się więcej o usłudze Azure Signal Service:
* [*Co to jest usługa Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Lub Przeczytaj więcej na temat uwierzytelniania usługi Azure sygnalizacyjnej przy użyciu Azure Functions:
* [*Uwierzytelnianie usługi Azure sygnalizujące*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)