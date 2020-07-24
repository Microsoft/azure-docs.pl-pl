---
title: Integracja z usługą Time Series Insights
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować trasy zdarzeń z usługi Azure Digital bliźniaczych reprezentacji do Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131603"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integracja Digital bliźniaczych reprezentacji z Azure Time Series Insights

W tym temacie dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). To rozwiązanie umożliwi zbieranie i analizowanie danych historycznych dotyczących rozwiązania IoT. Usługa Azure Digital bliźniaczych reprezentacji to doskonałe rozwiązanie do obsługi danych w Time Series Insights, ponieważ umożliwia skorelowanie wielu strumieni danych i standaryzację informacji przed wysłaniem ich do Time Series Insights. 

## <a name="solution-architecture"></a>Architektura rozwiązania

Dołączysz usługę Time Series Insights do usługi Azure Digital bliźniaczych reprezentacji za pomocą poniższej ścieżki.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu, wyróżnianie Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebujesz wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które można zaktualizować informacje o bliźniaczych kilka razy, aby zobaczyć, że dane są śledzone w Time Series Insights. 
    * Jeśli go nie masz, postępuj zgodnie z samouczkiem Digital bliźniaczych reprezentacji na platformie Azure [*: Połącz kompleksowe rozwiązanie*](./tutorial-end-to-end.md) , aby skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wirtualne urządzenie IoT na potrzeby generowania zmian bliźniaczych.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Tworzenie trasy i filtrowanie w celu otrzymywania powiadomień o aktualizacjach bliźniaczych

Usługa Azure Digital bliźniaczych reprezentacji Instances może emitować [wieloosiowe zdarzenia aktualizacji](how-to-interpret-event-data.md) za każdym razem, gdy stanem splotu jest zaktualizowany. Zostanie utworzona trasa, która będzie kierować te zdarzenia aktualizacji do centrum zdarzeń w celu dalszej obróbki.

Samouczek Digital bliźniaczych reprezentacji platformy Azure [*: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md) w scenariuszu, w którym termometr jest używany do aktualizowania atrybutu temperatury dołączonego do sznurka pokoju. Ten wzorzec używa aktualizacji bliźniaczych, a nie przekazywania danych telemetrycznych z urządzenia IoT, co zapewnia elastyczność zmiany bazowego źródła danych bez konieczności aktualizowania logiki Time Series Insights.

1. Utwórz przestrzeń nazw centrum zdarzeń, która będzie odbierać zdarzenia z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Utwórz centrum zdarzeń.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Utwórz [regułę autoryzacji](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Utwórz punkt końcowy, aby połączyć swój temat z siatką zdarzeń z usługą Azure Digital bliźniaczych reprezentacji.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Utwórz trasę w usłudze Azure Digital bliźniaczych reprezentacji, aby wysyłać do punktu końcowego zdarzenia aktualizacji z przędzą. Filtr w tej trasie zezwala tylko na przekazywanie komunikatów o aktualizacjach dwuosiowych do punktu końcowego.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure 

Następnie utworzysz funkcję wyzwalaną Event Hubs w ramach nowej aplikacji funkcji — aplikacji funkcji z kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md)) z programem. Ta funkcja spowoduje przekonwertowanie tych aktualizacji z oryginalnego formularza jako dokumentów poprawek JSON do obiektów JSON zawierających tylko zaktualizowane i dodane wartości z bliźniaczych reprezentacji.

Aby uzyskać więcej informacji na temat używania Event Hubs z usługą Azure Functions, zobacz [*wyzwalacz usługi azure Event Hubs dla Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

W opublikowanej aplikacji funkcji Zastąp kod funkcji następującym kodem.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

W tym miejscu funkcja będzie wysyłać do drugiego centrum zdarzeń obiekty JSON, które zostaną połączone z Time Series Insights.

## <a name="send-telemetry-to-an-event-hub"></a>Wysyłanie danych telemetrycznych do centrum zdarzeń

Teraz utworzysz drugie centrum zdarzeń i skonfigurujesz funkcję do przesyłania strumieniowego danych wyjściowych do tego centrum zdarzeń. To centrum zdarzeń zostanie następnie podłączone do Time Series Insights.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal*](../event-hubs/event-hubs-create.md).

1. Przygotuj przestrzeń nazw centrum zdarzeń i nazwę grupy zasobów z wcześniejszych 

2. Tworzenie centrum zdarzeń
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Tworzenie [reguły autoryzacji](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Skonfiguruj funkcję

Należy ustawić jedną zmienną środowiskową w aplikacji funkcji wcześniej zawierającej parametry połączenia centrum zdarzeń.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Ustawianie parametrów połączenia centrum zdarzeń Time Series Insights

1. Pobierz [Parametry połączenia centrum zdarzeń](../event-hubs/event-hubs-get-connection-string.md) dla reguł autoryzacji utworzonych powyżej dla centrum Time Series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. W aplikacji funkcji Utwórz ustawienie aplikacji zawierające parametry połączenia:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Ustaw parametry połączenia centrum zdarzeń bliźniaczych reprezentacji

1. Pobierz [Parametry połączenia centrum zdarzeń](../event-hubs/event-hubs-get-connection-string.md) dla reguł autoryzacji utworzonych powyżej dla centrum bliźniaczych reprezentacji.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. W aplikacji funkcji Utwórz ustawienie aplikacji zawierające parametry połączenia:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Tworzenie i łączenie wystąpienia Time Series Insights

Następnie skonfigurujesz wystąpienie Time Series Insights, aby otrzymywać dane z drugiego centrum zdarzeń. Aby uzyskać więcej informacji o tym procesie, zobacz [ *Samouczek: konfigurowanie środowiska Azure Time Series INSIGHTS Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. Na Azure Portal Rozpocznij tworzenie zasobu Time Series Insights. 
    1. Wybierz warstwę cenową **PAYG (wersja zapoznawcza)** .
    2. Konieczne będzie wybranie identyfikatora szeregów czasowych dla tego środowiska. Identyfikator szeregów czasowych może zawierać maksymalnie trzy wartości, które będą używane do wyszukiwania danych w Time Series Insights. W tym samouczku można użyć **$dtId**. Przeczytaj więcej na temat wybierania wartości identyfikatora w [*najlepszych rozwiązaniach dotyczących wybierania identyfikatora szeregów czasowych*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Interfejs użytkownika portalu tworzenia dla środowiska Time Series Insightsowego. Wybrana jest warstwa cenowa PAYG (wersja zapoznawcza), a nazwa właściwości identyfikatora szeregów czasowych to $dtId":::

2. Wybierz pozycję **Dalej: Źródło zdarzenia** i wybierz z powyższych informacje Event Hubs. Należy również utworzyć nową grupę konsumentów Event Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="ŚRODOWISKO użytkownika portalu tworzenia dla źródła zdarzeń środowiska Time Series Insights. Tworzysz źródło zdarzeń z informacjami o centrum zdarzeń z powyższych. Tworzysz również nową grupę odbiorców.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Rozpocznij wysyłanie danych IoT do usługi Azure Digital bliźniaczych reprezentacji

Aby rozpocząć wysyłanie danych do Time Series Insights, musisz rozpocząć aktualizowanie właściwości cyfrowej przędzy, zmieniając wartości danych. Użyj polecenia [AZ DT bliźniaczy Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Jeśli używasz kompleksowego samouczka, aby pomóc w instalacji środowiska, możesz zacząć wysyłać symulowane dane IoT, uruchamiając `DeviceSimulator` projekt z samouczka Digital bliźniaczych reprezentacji platformy Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md). Instrukcje znajdują się w sekcji [*Konfigurowanie i uruchamianie symulacji*](tutorial-end-to-end.md#configure-and-run-the-simulation) w samouczku.

## <a name="visualize-your-data-in-time-series-insights"></a>Wizualizowanie danych w Time Series Insights

Teraz dane powinny być przepływane do wystąpienia Time Series Insights, gotowe do przeanalizowania. Postępuj zgodnie z poniższymi instrukcjami, aby poznać dane pochodzące z programu.

1. Otwórz wystąpienie Time Series Insights w Azure Portal. Odwiedź adres URL Eksploratora Time Series Insights przedstawiony w omówieniu.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Kliknij adres URL Eksploratora Time Series Insights na karcie Przegląd środowiska Time Series Insights":::

2. W Eksploratorze zobaczysz trzy bliźniaczych reprezentacji widoczne po lewej stronie. Kliknij pozycję **thermostat67**, wybierz pozycję **patch_value**, a następnie kliknij pozycję **Dodaj**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Kliknij pozycję * * thermostat67 * *, wybierz pozycję * * temperatura * *, a następnie kliknij pozycję * * Dodaj * *":::

3. Teraz należy zapoznać się z początkowymi odczytami temperatury z termostatu, jak pokazano poniżej. Ten sam odczyt temperatury jest aktualizowany dla room21 i floor1 i można wizualizować te strumienie danych wspólnie.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Początkowe dane temperatury są przedstawiane w Eksploratorze TSI. Jest to wiersz losowych wartości z zakresu od 68 do 85":::

4. Aby symulacja działała dłużej, Wizualizacja będzie wyglądać następująco:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Dane temperatury dla każdej z nich są przedstawiane w trzech równoległych wierszach różnych kolorów.":::

## <a name="next-steps"></a>Następne kroki

Bliźniaczych reprezentacji cyfrowe są przechowywane domyślnie jako płaska hierarchia w Time Series Insights, ale można je wzbogacać o informacje o modelu i hierarchię wielopoziomową dla organizacji. Aby dowiedzieć się więcej na temat tego procesu, Przeczytaj: 

* [*Samouczek: Definiowanie i stosowanie modelu*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Można napisać logikę niestandardową, aby automatycznie podawać te informacje za pomocą modelu i danych grafu przechowywanych już w usłudze Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące odwołania:

* [*Instrukcje: Zarządzanie dwuosiową cyfrą*](./how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](./how-to-query-graph.md)