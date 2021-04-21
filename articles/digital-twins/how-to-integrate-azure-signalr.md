---
title: Integracja z usługą Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Zobacz, jak przesyłać strumieniowo dane Azure Digital Twins do klientów przy użyciu usługi Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775335"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integracja Azure Digital Twins z Azure SignalR Service

W tym artykule dowiesz się, jak zintegrować program Azure Digital Twins [z](../azure-signalr/signalr-overview.md)Azure SignalR Service .

Rozwiązanie opisane w tym artykule umożliwia wypychanie danych telemetrycznych bliźniaczych reprezentacji cyfrowych do połączonych klientów, takich jak pojedyncza strona internetowa lub aplikacja mobilna. W związku z tym klienci są aktualizowani przy użyciu metryk i stanu w czasie rzeczywistym z urządzeń IoT bez konieczności sondowania serwera ani przesyłania nowych żądań HTTP w celu aktualizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej podano wymagania wstępne, które należy spełnić przed przystąpieniem do pracy:

* Przed zintegrowaniem rozwiązania z usługą Azure SignalR Service w tym artykule należy ukończyć samouczek [_**Azure Digital Twins:**_](tutorial-end-to-end.md)łączenie kompleksowego rozwiązania , ponieważ ten artykuł z przewodnika jest na jego podstawie kompilowany. Ten samouczek zawiera instrukcji konfigurowania wystąpienia Azure Digital Twins, które współpracuje z wirtualnym urządzeniem IoT w celu wyzwalania aktualizacji bliźniaczych reprezentacji cyfrowych. W tym artykule z jaki sposób połączysz te aktualizacje z przykładową aplikacją internetową przy użyciu Azure SignalR Service.

* Z tego samouczka będą potrzebne następujące wartości:
  - Temat siatki zdarzeń
  - Grupa zasobów
  - Nazwa usługi App Service/aplikacji funkcji
    
* Konieczne będzie [**Node.js**](https://nodejs.org/) na maszynie.

Musisz również zalogować się do witryny [Azure Portal](https://portal.azure.com/) przy użyciu konta platformy Azure.

## <a name="solution-architecture"></a>Architektura rozwiązania

Dołączasz Azure SignalR Service do Azure Digital Twins za pośrednictwem poniższej ścieżki. Sekcje A, B i C na diagramie zostały wykonane na diagramie architektury wymagań wstępnych samouczka [end-to-end](tutorial-end-to-end.md). W tym artykule z jaki sposób opisano tworzenie sekcji D na podstawie istniejącej architektury.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Widok usług platformy Azure w scenariuszu end-to-end. Przedstawia dane przepływające z urządzenia do usługi IoT Hub przez funkcję platformy Azure (strzałkę B) do wystąpienia usługi Azure Digital Twins (sekcja A), a następnie wychodzące przez usługę Event Grid do innej funkcji platformy Azure w celu przetworzenia (strzałka C). Sekcja D pokazuje dane przepływające z tej samej Event Grid strzałki C do funkcji platformy Azure z etykietą &quot;broadcast&quot;. Funkcja &quot;broadcast&quot; komunikuje się z inną funkcją platformy Azure oznaczoną etykietą &quot;negotiate&quot;, a &quot;broadcast&quot; i &quot;negotiate&quot; komunikuje się z urządzeniami komputerowymi." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Pobieranie przykładowych aplikacji

Najpierw pobierz wymagane przykładowe aplikacje. Potrzebne będą następujące elementy:
* [**Azure Digital Twins**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)przykłady typu end-to-end: ten przykład zawiera usługę *AdtSampleApp,* która zawiera dwie funkcje platformy Azure służące do przenoszenia danych wokół wystąpienia usługi Azure Digital Twins (więcej informacji na temat tego scenariusza można znaleźć w tesłudze [*Tutorial: Connect an end-to-end solution*](tutorial-end-to-end.md)( Samouczek: łączenie rozwiązania end-to-end). Zawiera również przykładową *aplikację DeviceSimulator,* która symuluje urządzenie IoT, generując nową wartość temperatury co sekundę.
    - Jeśli nie pobrano jeszcze przykładu w ramach samouczka w części [](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) Wymagania [*wstępne,*](#prerequisites)przejdź do linku przykładowego i wybierz przycisk *Przeglądaj* kod pod tytułem. Spowoduje to pobranie przykładów do repozytorium GitHub, które można pobrać jako *plik . ZIP,* wybierając przycisk *Kod* i *pozycję Pobierz plik ZIP.*

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Widok repozytorium digital-twins-samples w witrynie GitHub. Zostanie wybrany przycisk Kod, w którym zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="media/includes/download-repo-zip.png":::

    Spowoduje to pobranie na komputer kopii przykładowegodigital-twins-samples-master.zip **.** Rozpakuj folder.
* [**Przykładowa aplikacja internetowa integracji z usługą SignalR:**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)jest to przykładowa aplikacja internetowa React, która będzie Azure Digital Twins danych telemetrycznych z Azure SignalR Service.
    -  Przejdź do linku przykładowego i użyj tego samego procesu pobierania, aby pobrać kopię przykładu na komputer, tak jakdigitaltwins-signalr-webapp-sample-main.zip _**.**_ Rozpakuj folder.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Pozostaw okno przeglądarki otwarte Azure Portal, ponieważ użyjesz go ponownie w następnej sekcji.

## <a name="publish-and-configure-the-azure-functions-app"></a>Publikowanie i konfigurowanie Azure Functions aplikacji

W tej sekcji skonfigurujemy dwie funkcje platformy Azure:
* **negotiate** — funkcja wyzwalacza HTTP. Używa on powiązania *wejściowego SignalRConnectionInfo* do generowania i zwracania prawidłowych informacji o połączeniu.
* **broadcast** — [Event Grid](../event-grid/overview.md) wyzwalacza. Odbiera ona Azure Digital Twins telemetrii za pośrednictwem usługi Event Grid i używa powiązania wyjściowego wystąpienia *usługi SignalR* utworzonego w poprzednim kroku w celu rozgłaszeniu komunikatu do wszystkich połączonych aplikacji klienckich.

Uruchom Visual Studio (lub inny edytor kodu) i otwórz rozwiązanie kodu w folderze *digital-twins-samples-master > ADTSampleApp.* Następnie wykonaj następujące kroki, aby utworzyć funkcje:

1. W *projekcie SampleFunctionsApp* utwórz nową klasę języka C# o nazwie **SignalRFunctions.cs.**

1. Zastąp zawartość pliku klasy następującym kodem:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. W oknie konsoli usługi Visual Studio firmy *Menedżer pakietów* lub dowolnym oknie poleceń na maszynie przejdź do folderu *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* i uruchom następujące polecenie, aby zainstalować pakiet `SignalRService` NuGet w projekcie:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Powinno to rozwiązać wszelkie problemy z zależnościami w klasie .

1. Opublikuj funkcję na platformie Azure, korzystając z instrukcji opisanych w sekcji [ *Publikowanie*](tutorial-end-to-end.md#publish-the-app) aplikacji w samouczku Łączenie z rozwiązaniem *end-to-end.* Możesz opublikować ją w tej samej aplikacji usługi app service/funkcji, [](#prerequisites)która była używana w wstępnym samouczku całościowego samouczka, lub utworzyć nową — ale możesz użyć tej samej aplikacji, aby zminimalizować duplikację. 

Następnie skonfiguruj funkcje do komunikowania się z wystąpieniem usługi Azure SignalR. Rozpoczniesz od zebrania parametrów połączenia wystąpienia usługi SignalR, a następnie dodasz je do ustawień aplikacji funkcji.

1. Przejdź do [Azure Portal](https://portal.azure.com/) i wyszukaj nazwę wystąpienia usługi SignalR na pasku wyszukiwania w górnej części portalu. Wybierz wystąpienie, aby je otworzyć.
1. Wybierz **pozycję Klucze** z menu wystąpienia, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR.
1. Wybierz *ikonę Kopiuj,* aby skopiować podstawowe ciągi połączenia.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Zrzut ekranu przedstawiający Azure Portal przedstawiający stronę Klucze dla wystąpienia usługi SignalR. Ikona &quot;Kopiuj do schowka&quot; obok podstawowej wartości PARAMETRÓW POŁĄCZENIA jest wyróżniona." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Na koniec dodaj swoje ciągi połączenia usługi Azure **SignalR** do ustawień aplikacji funkcji przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure. Ponadto zastąp symbole zastępcze nazwą grupy zasobów i aplikacji usługi App Service/funkcji z wymagania [wstępnego samouczka](how-to-integrate-azure-signalr.md#prerequisites). Polecenie można uruchomić w [programie Azure Cloud Shell](https://shell.azure.com)lokalnie, jeśli na komputerze jest zainstalowany interfejs [wiersza polecenia platformy Azure:](/cli/azure/install-azure-cli)
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Dane wyjściowe tego polecenia drukuje wszystkie ustawienia aplikacji ustawione dla funkcji platformy Azure. Poszukaj w `AzureSignalRConnectionString` dolnej części listy, aby sprawdzić, czy została dodana.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Fragment danych wyjściowych w oknie polecenia przedstawiający element listy o nazwie &quot;AzureSignalRConnectionString&quot;":::

#### <a name="connect-the-function-to-event-grid"></a>Łączenie funkcji z Event Grid

Następnie zasubskrybuj *rozgłaszaną* funkcję platformy Azure do **tematu usługi Event Grid** utworzonego w ramach wymagania [wstępnego samouczka](how-to-integrate-azure-signalr.md#prerequisites). Umożliwi to przepływ danych telemetrycznych z bliźniaczej reprezentacji termostatu 67 przez temat usługi Event Grid i do funkcji. W tym miejscu funkcja może rozgłaszać dane do wszystkich klientów.

W tym celu utworzysz subskrypcję zdarzeń z tematu usługi **Event** Grid do rozgłaszanych *funkcji* platformy Azure jako punktu końcowego.

W [Azure Portal](https://portal.azure.com/)przejdź do tematu siatki zdarzeń, wyszukując jego nazwę na górnym pasku wyszukiwania. Wybierz pozycję *+ Subskrypcja zdarzeń*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid subskrypcji zdarzeń":::

Na stronie *Tworzenie subskrypcji zdarzeń* wypełnij pola w następujący sposób (pola wypełnione domyślnie nie są wymienione):
* *SZCZEGÓŁY SUBSKRYPCJI ZDARZEŃ*  >  **Nazwa:** nadaj nazwę subskrypcji zdarzeń.
* *SZCZEGÓŁY PUNKTU KOŃCOWEGO*  >  **Typ punktu końcowego:** wybierz *pozycję Funkcja platformy Azure* z opcji menu.
* *SZCZEGÓŁY PUNKTU KOŃCOWEGO*  >  **Punkt** końcowy: kliknij link *Wybierz punkt* końcowy. Spowoduje to otwarcie okna *Wybieranie funkcji platformy Azure:*
    - Wypełnij pola **Subskrypcja,** **Grupa zasobów,** **Aplikacja funkcji** i **Funkcja** *(emisja).* Niektóre z nich mogą zostać wypełnione automatycznie po wybraniu subskrypcji.
    - Kliknij **potwierdź wybór.**

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal tworzenia subskrypcji zdarzeń. Powyższe pola są wypełnione, a przyciski &quot;Potwierdź wybór&quot; i &quot;Utwórz&quot; są wyróżnione.":::

Po powrocie na *stronę Tworzenie subskrypcji zdarzeń* kliknij utwórz **.**

W tym momencie na stronie Event Grid tematu powinny być Event Grid *zdarzenia.*

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal dwóch subskrypcji zdarzeń na stronie tematu usługi Event Grid." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Konfigurowanie i uruchamianie aplikacji internetowej

W tej sekcji zobaczysz wynik w działaniu. Najpierw skonfiguruj przykładową **aplikację internetową klienta,** aby nawiązać połączenie ze skonfigurowanym przepływem usługi Azure SignalR. Następnie należy uruchomić przykładową aplikację urządzenia symulowanego, **która** wysyła dane telemetryczne za pośrednictwem Azure Digital Twins wystąpienia. Następnie zobaczysz przykładową aplikację internetową, aby zobaczyć dane symulowanego urządzenia aktualizujące przykładową aplikację internetową w czasie rzeczywistym.

### <a name="configure-the-sample-client-web-app"></a>Konfigurowanie przykładowej klienckiej aplikacji internetowej

Następnie skonfigurujesz przykładową aplikację internetową klienta. Zacznij od zebrania **adresu URL punktu** końcowego HTTP funkcji *negotiate,* a następnie użyj go do skonfigurowania kodu aplikacji na maszynie.

1. Przejdź do Azure Portal aplikacji [funkcji i](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) wybierz aplikację funkcji z listy. W menu aplikacji wybierz pozycję *Funkcje* i wybierz funkcję *negotiate.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal aplikacji funkcji z wyróżnionem w menu elementem &quot;Functions&quot;. Lista funkcji jest wyświetlana na stronie, a funkcja &quot;negotiate&quot; jest również wyróżniona.":::

1. Naciśnij *link Pobierz adres URL* funkcji i skopiuj wartość w górę za pośrednictwem ciągu **_/api_ (nie uwzględniaj ostatniego _/negotiate?_)**. Użyjesz go w następnym kroku.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal widoku funkcji &quot;negotiate&quot;. Przycisk &quot;Pobierz adres URL funkcji&quot; jest wyróżniony, a część adresu URL od początku do &quot;/api&quot;":::

1. Korzystając Visual Studio dowolnego edytora kodu, otwórz rozpakowany folder _**digitaltwins-signalr-webapp-sample-main**_ pobrany w sekcji Pobieranie przykładowych [*aplikacji.*](#download-the-sample-applications)

1. Otwórz plik *src/App.js* i zastąp adres URL funkcji w pliku adresem URL punktu końcowego HTTP funkcji negotiate zapisanej `HubConnectionBuilder` w poprzednim kroku: 

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. W Visual Studio wiersza  polecenia dewelopera lub dowolnego okna polecenia na maszynie przejdź do folderu *digitaltwins-signalr-webapp-sample-main\src.* Uruchom następujące polecenie, aby zainstalować pakiety węzłów zależnych:

    ```cmd
    npm install
    ```

Następnie ustaw uprawnienia w aplikacji funkcji w Azure Portal:
1. Na Azure Portal aplikacji [funkcji](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) wybierz wystąpienie aplikacji funkcji.

1. Przewiń w dół w menu wystąpienia i wybierz pozycję *CORS*. Na stronie CORS dodaj element `http://localhost:3000` jako dozwolone źródło, wprowadzając je w pustym polu. Zaznacz pole wyboru enable *access-control-allow-credentials* i naciśnij *przycisk Zapisz*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Ustawienie CORS w funkcji platformy Azure":::

### <a name="run-the-device-simulator"></a>Uruchamianie symulatora urządzenia

W ramach wymagań wstępnych samouczka [](tutorial-end-to-end.md#configure-and-run-the-simulation) end-to-end skonfigurowano symulator urządzenia do wysyłania danych za pośrednictwem IoT Hub i do Azure Digital Twins wystąpienia.

Teraz wystarczy uruchomić projekt symulatora znajdujący się w skrypcie *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln.* Jeśli używasz narzędzia Visual Studio, możesz otworzyć projekt, a następnie uruchomić go za pomocą tego przycisku na pasku narzędzi:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Przycisk Visual Studio start (projekt DeviceSimulator)":::

Zostanie otwarte okno konsoli z symulowanym komunikatem telemetrii temperatury. Są one wysyłane za pośrednictwem Azure Digital Twins, gdzie są one następnie odbierane przez usługi Azure Functions i SignalR.

Nie musisz nic robić w tej konsoli, ale pozostaw ją uruchamianą podczas następnego kroku.

### <a name="see-the-results"></a>Zobacz wyniki

Aby zobaczyć wyniki w działaniu, uruchom przykładową aplikację internetową **integracji usługi SignalR.** Możesz to zrobić w dowolnym oknie konsoli w lokalizacji *digitaltwins-signalr-webapp-sample-main\src,* uruchamiając to polecenie:

```cmd
npm start
```

Spowoduje to otwarcie okna przeglądarki z przykładową aplikacją, w którym zostanie wyświetlony wizualny miernik temperatury. Po uruchomieniu aplikacji powinny zacząć być widać wartości telemetryczne temperatury z symulatora urządzenia, które są propagowane przez Azure Digital Twins przez aplikację internetową w czasie rzeczywistym.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Fragment przykładowej klienckiej aplikacji internetowej przedstawiający wizualny miernik temperatury. Odbita temperatura wynosi 67,52":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w tym artykule, wykonaj następujące kroki, aby je usunąć. 

Za pomocą Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą [polecenia az group delete.](/cli/azure/group#az_group_delete) Usunięcie grupy zasobów spowoduje również usunięcie...
* wystąpienie Azure Digital Twins (z samouczka end-to-end)
* centrum IoT i rejestracja urządzenia centrum (z samouczka end-to-end)
* temat usługi Event Grid i skojarzone subskrypcje
* aplikacja Azure Functions, w tym wszystkie trzy funkcje i skojarzone zasoby, takie jak magazyn
* wystąpienie usługi Azure SignalR

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Na koniec usuń przykładowe foldery projektu pobrane na komputer lokalny *(digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* i ich rozpakowane odpowiedniki.

## <a name="next-steps"></a>Następne kroki

W tym artykule skonfigurujemy funkcje platformy Azure za pomocą usługi SignalR w celu emitowania Azure Digital Twins telemetrii do przykładowej aplikacji klienckiej.

Następnie dowiedz się więcej o Azure SignalR Service:
* [*Co to jest usługa Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Lub przeczytaj więcej na temat uwierzytelniania Azure SignalR Service za pomocą Azure Functions:
* [*Azure SignalR Service uwierzytelniania*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)