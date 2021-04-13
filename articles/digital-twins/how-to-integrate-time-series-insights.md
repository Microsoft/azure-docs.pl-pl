---
title: Integracja z usługą Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować trasy zdarzeń z usługi Azure Digital bliźniaczych reprezentacji do Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 256bb80687ffedba9718303710335cce1a582c1d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304009"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integracja usługi Azure Digital bliźniaczych reprezentacji z usługą Azure Time Series Insights

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Rozwiązanie opisane w tym artykule umożliwi zbieranie i analizowanie danych historycznych dotyczących rozwiązania IoT. Usługa Azure Digital Twins to doskonałe rozwiązanie do obsługi danych w usłudze Time Series Insights, ponieważ umożliwia skorelowanie wielu strumieni danych i standaryzację informacji przed wysłaniem ich do usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować relację z Time Series Insights, należy skonfigurować następujące zasoby:
* **Centrum IoT**. Aby uzyskać instrukcje, zapoznaj się z sekcją [*tworzenie IoT Hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) w obszarze Szybki Start *telemetrii usługi IoT Hub* .
* **Wystąpienie usługi Azure Digital bliźniaczych reprezentacji**.
Aby uzyskać instrukcje, zobacz [*How to: set up the Azure Digital bliźniaczych reprezentacji instance and Authentication*](./how-to-set-up-instance-portal.md).
* **Model i dwuosiowy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji**.
Aby zobaczyć, że dane są śledzone w Time Series Insights, należy kilka razy zaktualizować informacje o przędze. Aby uzyskać instrukcje, zobacz sekcję [*Dodawanie modelu i sznurów*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) w artykule jak: pozyskiwanie usługi *IoT Hub* .

> [!TIP]
> W tym artykule zmiana wartości cyfrowych przędzy, które są wyświetlane w Time Series Insights są aktualizowane ręcznie dla uproszczenia. Jeśli jednak chcesz wykonać ten artykuł przy użyciu danych symulowanych na żywo, możesz skonfigurować funkcję platformy Azure, która aktualizuje cyfrowy bliźniaczych reprezentacji na podstawie zdarzeń telemetrii IoT z symulowanego urządzenia. Aby uzyskać instrukcje, postępuj zgodnie z poniższymi instrukcjami: Pozyskaj [*IoT Hub dane*](how-to-ingest-iot-hub-data.md), łącznie z końcowymi krokami do uruchomienia symulatora urządzeń i sprawdź, czy przepływ danych działa.
>
> Później poszukaj kolejnej porady, aby zobaczyć, gdzie zacząć korzystać z symulatora urządzeń i czy usługa Azure Functions ma automatycznie aktualizować bliźniaczych reprezentacji zamiast wysyłania ręcznych poleceń aktualizacji z podpisem cyfrowym.


## <a name="solution-architecture"></a>Architektura rozwiązania

Time Series Insights można dołączyć do usługi Azure Digital bliźniaczych reprezentacji za pomocą poniższej ścieżki.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram usług platformy Azure w kompleksowym scenariuszu, wyróżnianie Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Tworzenie przestrzeni nazw centrum zdarzeń

Przed utworzeniem centrów zdarzeń należy najpierw utworzyć przestrzeń nazw centrum zdarzeń, która będzie odbierać zdarzenia z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal*](../event-hubs/event-hubs-create.md). Aby zobaczyć, które regiony obsługują Centra zdarzeń, odwiedź stronę [*dostępne dla regionu platformy Azure*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie `BadRequest: The specified service namespace is invalid.` , upewnij się, że nazwa wybrana dla obszaru nazw spełnia wymagania dotyczące nazewnictwa opisane w tym dokumencie odwołania: [Utwórz przestrzeń nazw](/rest/api/servicebus/create-namespace).

Ta przestrzeń nazw usługi Event Hub będzie używana do przechowywania dwóch centrów zdarzeń, które są potrzebne w tym artykule:

  1. **Bliźniaczych reprezentacji Hub** — centrum zdarzeń do odbierania dwuosiowych zdarzeń zmiany
  2. **Centra szeregów czasowych** — centrum zdarzeń do przesyłania strumieniowego zdarzeń do Time Series Insights

Kolejne sekcje przeprowadzą Cię przez proces tworzenia i konfigurowania tych centrów w przestrzeni nazw centrum zdarzeń.

## <a name="create-twins-hub"></a>Utwórz centrum bliźniaczych reprezentacji

Pierwszym centrum zdarzeń utworzonym w tym artykule jest **centrum bliźniaczych reprezentacji**. To centrum zdarzeń będzie odbierać dwuosiowe zdarzenia zmiany z usługi Azure Digital bliźniaczych reprezentacji.
Aby skonfigurować centrum bliźniaczych reprezentacji, wykonaj następujące kroki w tej sekcji:

1. Tworzenie centrum bliźniaczych reprezentacji
2. Tworzenie reguły autoryzacji w celu kontrolowania uprawnień do centrum
3. Tworzenie punktu końcowego w usłudze Azure Digital bliźniaczych reprezentacji, który korzysta z reguły autoryzacji w celu uzyskiwania dostępu do centrum
4. Tworzenie trasy w usłudze Azure Digital bliźniaczych reprezentacji, która wysyła wydarzenie z aktualizacjami bliźniaczych do punktu końcowego i połączonego centrum bliźniaczych reprezentacji
5. Pobierz parametry połączenia centrum bliźniaczych reprezentacji

Utwórz **centrum bliźniaczych reprezentacji** przy użyciu poniższego polecenia interfejsu CLI. Określ nazwę centrum bliźniaczych reprezentacji.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Tworzenie reguły autoryzacji centrum bliźniaczych reprezentacji

Utwórz [regułę autoryzacji](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania. Określ nazwę reguły.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Utwórz punkt końcowy centrum bliźniaczych reprezentacji

Utwórz [punkt końcowy](concepts-route-events.md#create-an-endpoint) usługi Azure Digital bliźniaczych reprezentacji, który łączy centrum zdarzeń z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Określ nazwę punktu końcowego centrum bliźniaczych reprezentacji.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Utwórz trasę zdarzeń centrum bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji Instances może emitować [wieloosiowe zdarzenia aktualizacji](how-to-interpret-event-data.md) za każdym razem, gdy stanem splotu jest zaktualizowany. W tej sekcji utworzysz **trasę zdarzeń** usługi Azure Digital bliźniaczych reprezentacji, która będzie kierować te zdarzenia aktualizacji do centrum bliźniaczych reprezentacji w celu dalszej obróbki.

Utwórz [trasę](concepts-route-events.md#create-an-event-route) w usłudze Azure Digital bliźniaczych reprezentacji, aby wysyłać do punktu końcowego zdarzenia aktualizacji z góry. Filtr w tej trasie zezwala tylko na przekazywanie komunikatów o aktualizacjach dwuosiowych do punktu końcowego. Określ nazwę dla trasy zdarzeń centrum bliźniaczych reprezentacji.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Pobierz parametry połączenia centrum bliźniaczych reprezentacji

Pobierz [Parametry połączenia centrum zdarzeń bliźniaczych reprezentacji](../event-hubs/event-hubs-get-connection-string.md), używając reguł autoryzacji utworzonych powyżej dla centrum bliźniaczych reprezentacji.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Zwróć uwagę na wartość **primaryConnectionString** z wyniku, aby skonfigurować ustawienie aplikacji centrum bliźniaczych reprezentacji w dalszej części tego artykułu.

## <a name="create-time-series-hub"></a>Tworzenie centrum szeregów czasowych

Drugie centrum zdarzeń, które utworzysz w tym artykule, jest **centrum szeregów czasowych**. Jest to centrum zdarzeń, które będzie przesyłać strumieniowo zdarzenia usługi Azure Digital bliźniaczych reprezentacji do Time Series Insights.
Aby skonfigurować centrum szeregów czasowych, wykonaj następujące czynności:

1. Tworzenie centrum szeregów czasowych
2. Tworzenie reguły autoryzacji w celu kontrolowania uprawnień do centrum
3. Pobieranie parametrów połączenia centrum szeregów czasowych

Później podczas tworzenia wystąpienia Time Series Insights należy połączyć to centrum szeregów czasowych jako źródło zdarzeń dla wystąpienia Time Series Insights.

Utwórz **centrum szeregów czasowych** przy użyciu następującego polecenia. Określ nazwę centrum szeregów czasowych.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Utwórz regułę autoryzacji centrum szeregów czasowych

Utwórz [regułę autoryzacji](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) z uprawnieniami do wysyłania i odbierania. Określ nazwę reguły uwierzytelniania centrum szeregów czasowych.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Pobierz parametry połączenia centrum szeregów czasowych

Pobierz [Parametry połączenia centrum szeregów czasowych](../event-hubs/event-hubs-get-connection-string.md), używając reguł autoryzacji utworzonych powyżej dla centrum szeregów czasowych:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Zanotuj wartość **primaryConnectionString** z wyniku, aby skonfigurować ustawienie aplikacji centrum szeregów czasowych w dalszej części tego artykułu.

Należy również wziąć pod uwagę następujące wartości, aby użyć ich później do utworzenia wystąpienia Time Series Insights.
* Przestrzeń nazw centrum zdarzeń
* Nazwa centrum szeregów czasowych
* Reguła uwierzytelniania centrum szeregów czasowych

## <a name="create-a-function"></a>Tworzenie funkcji

W tej sekcji utworzysz funkcję platformy Azure, która spowoduje przekonwertowanie zdarzeń aktualizacji splotów z ich oryginalnego formularza jako dokumentów poprawek w formacie JSON na obiekty JSON zawierające tylko zaktualizowane i dodane wartości z bliźniaczych reprezentacji.

### <a name="step-1-create-function-app"></a>Krok 1. Tworzenie aplikacji funkcji

Najpierw utwórz nowy projekt aplikacji funkcji w programie Visual Studio. Aby uzyskać instrukcje, jak to zrobić, zobacz sekcję [**Tworzenie aplikacji funkcji w programie Visual Studio w**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) sekcji *instrukcje: Konfigurowanie funkcji do przetwarzania artykułu danych* .

### <a name="step-2-add-a-new-function"></a>Krok 2. Dodawanie nowej funkcji

Utwórz nową funkcję platformy Azure o nazwie *ProcessDTUpdatetoTSI. cs* , aby zaktualizować zdarzenia telemetryczne urządzenia do Time Series Insights. Typem funkcji będzie **wyzwalacz centrum zdarzeń**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Zrzut ekranu przedstawiający program Visual Studio do tworzenia nowej funkcji platformy Azure typu wyzwalacz centrum zdarzeń.":::

### <a name="step-3-fill-in-function-code"></a>Krok 3. wypełnienie kodu funkcji

Dodaj następujące pakiety do projektu:
* [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Zastąp kod w pliku *ProcessDTUpdatetoTSI. cs* następującym kodem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Zapisz kod funkcji.

### <a name="step-4-publish-the-function-app-to-azure"></a>Krok 4. publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt przy użyciu funkcji *ProcessDTUpdatetoTSI. cs* w aplikacji funkcji na platformie Azure.

Aby uzyskać instrukcje, jak to zrobić, zapoznaj się z sekcją [**publikowanie aplikacji funkcji na platformie Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) *: Konfigurowanie funkcji do przetwarzania artykułu danych* .

Zapisz nazwę aplikacji funkcji, która będzie używana później do konfigurowania ustawień aplikacji dla dwóch centrów zdarzeń.

### <a name="step-5-security-access-for-the-function-app"></a>Krok 5. dostęp do zabezpieczeń aplikacji funkcji

Następnie **Przypisz rolę dostępu** dla funkcji i **Skonfiguruj ustawienia aplikacji** tak, aby mogły uzyskiwać dostęp do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Aby uzyskać instrukcje, jak to zrobić, zapoznaj się z sekcją [**Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) w artykule wykonywanie instrukcji *: Konfigurowanie funkcji do przetwarzania danych* .

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Krok 6. Konfigurowanie ustawień aplikacji dla dwóch centrów zdarzeń

Następnie dodasz zmienne środowiskowe w ustawieniach aplikacji funkcji, które umożliwiają jej dostęp do centrum bliźniaczych reprezentacji i koncentratora szeregów czasowych.

Użyj wartości **primaryConnectionString** Hub bliźniaczych reprezentacji, która została wcześniej zapisana, aby utworzyć ustawienie aplikacji w aplikacji funkcji, która zawiera parametry połączenia centrum bliźniaczych reprezentacji:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Użyj wartości **primaryConnectionString** centrum szeregów czasowych zapisanej wcześniej, aby utworzyć ustawienie aplikacji w aplikacji funkcji, która zawiera parametry połączenia centrum szeregów czasowych:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Tworzenie i łączenie wystąpienia usługi Time Series Insights

W tej sekcji skonfigurujesz Time Series Insights wystąpienie do odbierania danych z centrum szeregów czasowych. Aby uzyskać więcej informacji o tym procesie, zobacz [*Samouczek: Konfigurowanie środowiska Azure Time Series Insights GEN2 PAYG*](../time-series-insights/tutorial-set-up-environment.md). Wykonaj poniższe kroki, aby utworzyć środowisko wglądu w dane szeregów czasowych.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj *Time Series Insights środowiska*, a następnie wybierz przycisk **Dodaj** . Wybierz poniższe opcje, aby utworzyć środowisko szeregów czasowych.

    * **Subskrypcja** — wybierz swoją subskrypcję.
        - **Grupa zasobów** — wybierz grupę zasobów.
    * **Nazwa środowiska** — Określ nazwę środowiska szeregów czasowych.
    * **Lokalizacja** — wybierz lokalizację.
    * **Warstwa** — wybierz warstwę cenową **Gen2 (L1)** .
    * **Nazwa właściwości** — wprowadź **$dtId** (Dowiedz się więcej na temat wybierania wartości identyfikatora w [*najlepszych rozwiązaniach dotyczących wybierania identyfikatora szeregów czasowych*](../time-series-insights/how-to-select-tsid.md)).
    * **Nazwa konta magazynu** — Określ nazwę konta magazynu.
    * **Włącz sklep ciepły** — pozostaw to pole ustawione na *wartość tak*.

    Możesz pozostawić wartości domyślne dla innych właściwości na tej stronie. Wybierz przycisk **Dalej: Źródło zdarzenia >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia środowiska Time Series Insights. Wybierz swoją subskrypcję, grupę zasobów i lokalizację z odpowiednich list rozwijanych i wybierz nazwę środowiska." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia środowiska Time Series Insights. Wybrana jest warstwa cenowa Gen2 (L1), a nazwa właściwości identyfikatora szeregów czasowych to $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Na karcie *Źródło zdarzenia* wybierz następujące pola:

   * **Utworzyć źródło zdarzenia?** -Wybierz opcję *tak*.
   * **Typ źródła** — wybierz pozycję *centrum zdarzeń*.
   * **Nazwa** — Podaj nazwę źródła zdarzenia.
   * **Subskrypcja** — wybierz subskrypcję platformy Azure.
   * **Przestrzeń nazw centrum zdarzeń** — wybierz przestrzeń nazw utworzoną wcześniej w tym artykule.
   * **Nazwa centrum zdarzeń** — wybierz nazwę **centrum szeregów czasowych** utworzoną wcześniej w tym artykule.
   * **Nazwa zasad dostępu centrum zdarzeń** — wybierz *regułę uwierzytelniania centrum szeregów czasowych* utworzoną wcześniej w tym artykule.
   * **Grupa użytkowników centrum zdarzeń** — wybierz pozycję *Nowy* i określ nazwę grupy odbiorców centrum zdarzeń. Następnie wybierz pozycję *Dodaj*.
   * **Nazwa właściwości** — pozostaw to pole puste.
    
    Wybierz przycisk **Recenzja + Utwórz** , aby przejrzeć wszystkie szczegóły. Następnie wybierz ponownie przycisk **Recenzja + Utwórz** , aby utworzyć środowisko szeregów czasowych.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia środowiska Time Series Insights. Tworzysz źródło zdarzeń z informacjami o centrum zdarzeń z powyższych. Tworzysz również nową grupę odbiorców." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Wysyłanie danych IoT do usługi Azure Digital bliźniaczych reprezentacji

Aby rozpocząć wysyłanie danych do Time Series Insights, musisz rozpocząć aktualizowanie właściwości cyfrowego przędzy w usłudze Azure Digital bliźniaczych reprezentacji, zmieniając wartości danych.

Poniższe polecenie interfejsu wiersza polecenia służy do aktualizowania właściwości *temperatury* na *thermostat67* bliźniaczym dodanym do wystąpienia w sekcji [wymagania wstępne](#prerequisites) .

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Powtórz polecenie co najmniej 4 razy o różne wartości temperatury**, aby utworzyć kilka punktów danych, które mogą być obserwowane w dalszej części środowiska Time Series Insights.

> [!TIP]
> Jeśli chcesz wykonać ten artykuł przy użyciu symulowanych danych na żywo zamiast ręcznie zaktualizować wartości cyfrowej przędzy, najpierw zapoznaj się z sekcją [*wymagania wstępne*](#prerequisites) , aby skonfigurować funkcję platformy Azure, która aktualizuje bliźniaczych reprezentacji z symulowanego urządzenia.
Następnie możesz uruchomić urządzenie teraz, aby zacząć wysyłać symulowane dane i aktualizować wielocyfrowe sznurki za pomocą tego przepływu danych.

## <a name="visualize-your-data-in-time-series-insights"></a>Wizualizowanie danych w usłudze Time Series Insights

Teraz dane powinny być przepływane do wystąpienia Time Series Insights, gotowe do przeanalizowania. Postępuj zgodnie z poniższymi instrukcjami, aby poznać dane pochodzące z programu.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj wcześniej utworzoną nazwę środowiska szeregów czasowych. W opcje menu po lewej stronie wybierz pozycję *Przegląd* , aby wyświetlić *adres URL Eksploratora Time Series Insights*. Wybierz adres URL, aby wyświetlić zmiany temperatury odzwierciedlone w środowisku Time Series Insights.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby wybrać adres URL Eksploratora Time Series Insights na karcie Przegląd środowiska Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. W Eksploratorze zobaczysz bliźniaczych reprezentacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji widocznym po lewej stronie. Wybierz *thermostat67* sznurek, wybierz *temperaturę* właściwości i przycisk **Dodaj**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Zrzut ekranu Eksploratora Time Series Insights, aby wybrać thermostat67, wybierz temperaturę właściwości i przycisk Dodaj." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Należy teraz sprawdzić początkowe odczyty temperatury z termostatu, jak pokazano poniżej. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Zrzut ekranu Eksploratora TSI, aby wyświetlić początkowe dane temperatury. Jest to wiersz losowych wartości z zakresu od 68 do 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Jeśli zezwolisz na wykonywanie symulacji o wiele dłużej, Wizualizacja będzie wyglądać następująco:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Zrzut ekranu Eksploratora TSI, w którym dane temperatury dla każdej z nich są przedstawione w trzech równoległych wierszach różnych kolorów." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Następne kroki

Bliźniaczych reprezentacji cyfrowe są przechowywane domyślnie jako płaska hierarchia w Time Series Insights, ale można je wzbogacać o informacje o modelu i hierarchię wielopoziomową dla organizacji. Aby dowiedzieć się więcej na temat tego procesu, Przeczytaj: 

* [*Samouczek: Definiowanie i stosowanie modelu*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Można napisać logikę niestandardową, aby automatycznie podawać te informacje za pomocą modelu i danych grafu przechowywanych już w usłudze Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące odwołania:

* [*Instrukcje: Zarządzanie dwuosiową cyfrą*](./how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](./how-to-query-graph.md)