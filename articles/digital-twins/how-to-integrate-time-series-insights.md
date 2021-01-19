---
title: Integracja z usługą Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować trasy zdarzeń z usługi Azure Digital bliźniaczych reprezentacji do Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 24b4f56e5798acc4d9bd0962be7059a359958645
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573245"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integracja usługi Azure Digital bliźniaczych reprezentacji z usługą Azure Time Series Insights

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Rozwiązanie opisane w tym artykule umożliwi zbieranie i analizowanie danych historycznych dotyczących rozwiązania IoT. Usługa Azure Digital Twins to doskonałe rozwiązanie do obsługi danych w usłudze Time Series Insights, ponieważ umożliwia skorelowanie wielu strumieni danych i standaryzację informacji przed wysłaniem ich do usługi Time Series Insights. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować relację z Time Series Insights, musisz mieć **wystąpienie usługi Azure Digital bliźniaczych reprezentacji**. To wystąpienie powinno być skonfigurowane z możliwością aktualizowania informacji o cyfrowym bliźniaczym wyznaczonym na podstawie danych, ponieważ należy kilka razy zaktualizować informacje o bliźniaczych, aby zobaczyć, że dane są śledzone w Time Series Insights. 

Jeśli jeszcze tego nie zrobiono, możesz go utworzyć, postępując zgodnie z samouczkiem Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md). Ten samouczek przeprowadzi Cię przez proces konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które współpracuje z wirtualnym urządzeniem IoT w celu wyzwolenia aktualizacji cyfrowych sznurów.

## <a name="solution-architecture"></a>Architektura rozwiązania

Time Series Insights można dołączyć do usługi Azure Digital bliźniaczych reprezentacji za pomocą poniższej ścieżki.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu, wyróżnianie Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Tworzenie trasy i filtrowanie powiadomień o aktualizacjach reprezentacji bliźniaczej

Usługa Azure Digital bliźniaczych reprezentacji Instances może emitować [wieloosiowe zdarzenia aktualizacji](how-to-interpret-event-data.md) za każdym razem, gdy stanem splotu jest zaktualizowany. W tej sekcji utworzysz [**trasę zdarzeń**](concepts-route-events.md) usługi Azure Digital bliźniaczych reprezentacji, która będzie kierować te zdarzenia aktualizacji do usługi Azure [Event Hubs](../event-hubs/event-hubs-about.md) w celu dalszej obróbki.

Samouczek Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md) w scenariuszu, w którym termometr jest używany do aktualizacji atrybutu temperatury na dwucyfrowej sieci dwukierunkowej. Ten wzorzec polega na aktualizacjach bliźniaczych, a nie przekazywaniu danych telemetrycznych z urządzenia IoT, co zapewnia elastyczność zmiany bazowego źródła danych bez konieczności aktualizowania logiki Time Series Insights.

1. Najpierw utwórz przestrzeń nazw centrum zdarzeń, która będzie odbierać zdarzenia z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal*](../event-hubs/event-hubs-create.md). Aby dowiedzieć się, które regiony obsługują Event Hubs, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Utwórz centrum zdarzeń w przestrzeni nazw, aby otrzymywać dwuosiowe zdarzenia zmiany. Określ nazwę centrum zdarzeń.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Utwórz [regułę autoryzacji](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania. Określ nazwę reguły.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Utwórz [punkt końcowy](concepts-route-events.md#create-an-endpoint) usługi Azure Digital bliźniaczych reprezentacji, który łączy centrum zdarzeń z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

    ```azurecli-interactive
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Utwórz [trasę](concepts-route-events.md#create-an-event-route) w usłudze Azure Digital Twins, aby wysyłać zdarzenia aktualizacji reprezentacji bliźniaczej do punktu końcowego. Filtr w tej trasie zezwala tylko na przekazywanie komunikatów o aktualizacjach dwuosiowych do punktu końcowego.

    >[!NOTE]
    >Obecnie istnieje **znany problem** w usłudze Cloud Shell, który wpływa na następujące grupy poleceń: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Aby rozwiązać ten problem, uruchom polecenie `az login` w usłudze Cloud Shell przed uruchomieniem danego polecenia lub użyj [lokalnego interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) zamiast usługi Cloud Shell. Aby uzyskać szczegółowe informacje na ten temat, zobacz [*Rozwiązywanie problemów: znane problemy w usłudze Azure Digital bliźniaczych reprezentacji*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Przed przejściem do programu Zanotuj *Event Hubs przestrzeń nazw* i *grupę zasobów*, ponieważ zostaną one użyte ponownie w celu utworzenia innego centrum zdarzeń w dalszej części tego artykułu.

## <a name="create-a-function-in-azure"></a>Tworzenie funkcji na platformie Azure

Następnie użyjesz Azure Functions, aby utworzyć **funkcję wyzwalaną Event Hubs** wewnątrz aplikacji funkcji. Możesz użyć aplikacji funkcji utworzonej w kompleksowym samouczku ([*Samouczek: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md)) lub własnych. 

Ta funkcja spowoduje przekonwertowanie tych dwuosiowych zdarzeń aktualizacji z ich oryginalnego formularza jako dokumentów poprawek JSON do obiektów JSON, zawierających tylko zaktualizowane i dodane wartości z bliźniaczych reprezentacji.

Aby uzyskać więcej informacji o korzystaniu z Event Hubs z Azure Functions, zobacz [*wyzwalacz usługi Azure Event Hubs dla Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

W opublikowanej aplikacji funkcji Zastąp kod funkcji następującym kodem.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

W tym miejscu funkcja będzie wysyłać do drugiego centrum zdarzeń obiekty JSON, które zostaną połączone z Time Series Insights.

Później należy również ustawić zmienne środowiskowe, które będą używane przez tę funkcję do łączenia się ze swoimi centrami zdarzeń.

## <a name="send-telemetry-to-an-event-hub"></a>Wysyłanie danych telemetrycznych do centrum zdarzeń

Teraz utworzysz drugie centrum zdarzeń i skonfigurujesz funkcję do przesyłania strumieniowego danych wyjściowych do tego centrum zdarzeń. To centrum zdarzeń zostanie następnie połączone z usługą Time Series Insights.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aby utworzyć drugie centrum zdarzeń, możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal*](../event-hubs/event-hubs-create.md).

1. Przygotuj *Event Hubs przestrzeń nazw* i nazwę *grupy zasobów* z wcześniejszej części tego artykułu

2. Utwórz nowe centrum zdarzeń. Określ nazwę centrum zdarzeń.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Utwórz [regułę autoryzacji](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania. Określ nazwę reguły.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Skonfiguruj funkcję

Następnie musisz ustawić zmienne środowiskowe w aplikacji funkcji z wcześniejszych wersji zawierających parametry połączenia dla utworzonych centrów zdarzeń.

### <a name="set-the-twins-event-hub-connection-string"></a>Ustawianie parametrów połączenia centrum zdarzeń reprezentacji bliźniaczych

1. Pobierz [Parametry połączenia centrum zdarzeń](../event-hubs/event-hubs-get-connection-string.md)bliźniaczych reprezentacji, używając reguł autoryzacji utworzonych powyżej dla centrum bliźniaczych reprezentacji.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Użyj parametrów połączenia uzyskanych w wyniku, aby utworzyć w aplikacji funkcji ustawienie aplikacji zawierające parametry połączenia:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Ustawianie parametrów połączenia centrum zdarzeń usługi Time Series Insights

1. Pobierz [Parametry połączenia centrum zdarzeń](../event-hubs/event-hubs-get-connection-string.md)TSI przy użyciu reguł autoryzacji utworzonych powyżej dla centrum Time Series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. W aplikacji funkcji utwórz ustawienie aplikacji zawierające parametry połączenia:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Tworzenie i łączenie wystąpienia usługi Time Series Insights

Następnie skonfigurujesz wystąpienie Time Series Insights, aby otrzymywać dane z drugiego centrum zdarzeń. Wykonaj poniższe kroki i aby uzyskać więcej informacji o tym procesie, zobacz [*Samouczek: Konfigurowanie środowiska Azure Time Series Insights GEN2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. Na Azure Portal Rozpocznij tworzenie zasobu Time Series Insights. 
    1. Wybierz warstwę cenową **Gen2 (L1)** .
    2. Konieczne będzie wybranie **identyfikatora szeregów czasowych** dla tego środowiska. Identyfikator szeregów czasowych może zawierać maksymalnie trzy wartości, które będą używane do wyszukiwania danych w Time Series Insights. W tym samouczku można użyć **$dtId**. Przeczytaj więcej na temat wybierania wartości identyfikatora w [*najlepszych rozwiązaniach dotyczących wybierania identyfikatora szeregów czasowych*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Interfejs użytkownika portalu tworzenia dla środowiska Time Series Insightsowego. Wybrana jest warstwa cenowa Gen2 (L1), a nazwa właściwości identyfikatora szeregów czasowych to $dtId" lightbox="media/how-to-integrate-time-series-insights/create-twin-id.png":::

2. Wybierz pozycję **Dalej: Źródło zdarzenia** i wybierz z powyższych informacje Event Hubs. Należy również utworzyć nową grupę konsumentów Event Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="ŚRODOWISKO użytkownika portalu tworzenia dla źródła zdarzeń środowiska Time Series Insights. Tworzysz źródło zdarzeń z informacjami o centrum zdarzeń z powyższych. Tworzysz również nową grupę odbiorców." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Rozpocznij wysyłanie danych IoT do usługi Azure Digital bliźniaczych reprezentacji

Aby rozpocząć wysyłanie danych do Time Series Insights, należy rozpocząć aktualizowanie właściwości cyfrowego przędzy w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu zmiany wartości danych. Użyj polecenia [AZ DT bliźniaczy Update](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) .

Jeśli używasz kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)) w celu ułatwienia instalacji środowiska, możesz zacząć wysyłać symulowane dane IoT, uruchamiając projekt *DeviceSimulator* z przykładu. Instrukcje znajdują się w sekcji [*Konfigurowanie i uruchamianie symulacji*](tutorial-end-to-end.md#configure-and-run-the-simulation) w samouczku.

## <a name="visualize-your-data-in-time-series-insights"></a>Wizualizowanie danych w usłudze Time Series Insights

Teraz dane powinny być przepływane do wystąpienia Time Series Insights, gotowe do przeanalizowania. Postępuj zgodnie z poniższymi instrukcjami, aby poznać dane pochodzące z programu.

1. Otwórz wystąpienie Time Series Insights w [Azure Portal](https://portal.azure.com) (możesz wyszukać nazwę wystąpienia na pasku wyszukiwania portalu). Odwiedź *adres URL eksploratora Time Series Insights* widoczny w przeglądzie wystąpienia.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Wybierz adres URL Eksploratora Time Series Insights na karcie Przegląd środowiska Time Series Insights":::

2. W Eksploratorze zobaczysz trzy bliźniaczych reprezentacji z usługi Azure Digital bliźniaczych reprezentacji wyświetlonej po lewej stronie. Wybierz pozycję _**thermostat67**_, wybierz pozycję **temperatura** i przycisk **Dodaj**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="SELECT * * thermostat67 * *, Select * * temperatura * * i naciśnij przycisk * * Dodaj * *":::

3. Teraz należy zapoznać się z początkowymi odczytami temperatury z termostatu, jak pokazano poniżej. Ten sam odczyt temperatury jest aktualizowany dla *room21* i *floor1* i można wizualizować te strumienie danych wspólnie.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Początkowe dane temperatury są przedstawiane w Eksploratorze TSI. Jest to wiersz losowych wartości z zakresu od 68 do 85":::

4. Aby symulacja działała dłużej, Wizualizacja będzie wyglądać następująco:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Dane temperatury dla każdej z nich są przedstawiane w trzech równoległych wierszach różnych kolorów.":::

## <a name="next-steps"></a>Następne kroki

Bliźniaczych reprezentacji cyfrowe są przechowywane domyślnie jako płaska hierarchia w Time Series Insights, ale można je wzbogacać o informacje o modelu i hierarchię wielopoziomową dla organizacji. Aby dowiedzieć się więcej na temat tego procesu, Przeczytaj: 

* [*Samouczek: Definiowanie i stosowanie modelu*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Można napisać logikę niestandardową, aby automatycznie podawać te informacje za pomocą modelu i danych grafu przechowywanych już w usłudze Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące odwołania:

* [*Instrukcje: Zarządzanie dwuosiową cyfrą*](./how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](./how-to-query-graph.md)