---
title: Integracja z usługą Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować trasy zdarzeń z Azure Digital Twins do Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783777"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integracja Azure Digital Twins z Azure Time Series Insights

W tym artykule dowiesz się, jak zintegrować Azure Digital Twins z Azure Time Series Insights [(TSI).](../time-series-insights/overview-what-is-tsi.md)

Rozwiązanie opisane w tym artykule umożliwi zbieranie i analizowanie danych historycznych dotyczących rozwiązania IoT. Usługa Azure Digital Twins to doskonałe rozwiązanie do obsługi danych w usłudze Time Series Insights, ponieważ umożliwia skorelowanie wielu strumieni danych i standaryzację informacji przed wysłaniem ich do usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem relacji z Time Series Insights należy skonfigurować następujące zasoby:
* Centrum **IoT Hub.** Aby uzyskać instrukcje, zobacz sekcję [*Tworzenie*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) IoT Hub w przewodniku Szybki start wysyłanie IoT Hub *telemetrii.*
* Wystąpienie **Azure Digital Twins .**
Aby uzyskać instrukcje, [*zobacz Instrukcje: konfigurowanie Azure Digital Twins i uwierzytelniania.*](./how-to-set-up-instance-portal.md)
* Model **i bliźniacze reprezentacji w Azure Digital Twins wystąpienia**.
Musisz kilka razy zaktualizować informacje bliźniaczej reprezentacji, aby zobaczyć, że dane są śledzone w Time Series Insights. Aby uzyskać instrukcje, [*zobacz sekcję Dodawanie modelu*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) i bliźniaczej reprezentacji artykułu *Instrukcje: pozysowanie centrum IoT* Hub.

> [!TIP]
> W tym artykule zmieniające się wartości bliźniaczych reprezentacji cyfrowych, które są Time Series Insights są aktualizowane ręcznie dla uproszczenia. Jeśli jednak chcesz ukończyć ten artykuł przy użyciu symulowanych danych na żywo, możesz skonfigurować funkcję platformy Azure, która aktualizuje usługi Digital Twins na podstawie zdarzeń telemetrii IoT z symulowanego urządzenia. Aby uzyskać instrukcje, postępuj zgodnie z [*instrukcjami:*](how-to-ingest-iot-hub-data.md)pozyskuj IoT Hub, w tym końcowe kroki uruchamiania symulatora urządzenia i sprawdzania, czy przepływ danych działa.
>
> Później poszukaj innej porady, aby pokazać, gdzie rozpocząć uruchamianie symulatora urządzenia i automatycznie zaktualizować bliźniacze bliźniacze reprezentacji usługi Azure Functions, zamiast wysyłać ręczne polecenia aktualizacji bliźniaczych reprezentacji cyfrowych.


## <a name="solution-architecture"></a>Architektura rozwiązania

Dołączasz Time Series Insights do Azure Digital Twins za pośrednictwem poniższej ścieżki.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram usług platformy Azure w scenariuszu end-to-end z wyróżnieniami Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Tworzenie przestrzeni nazw centrum zdarzeń

Przed utworzeniem centrów zdarzeń należy najpierw utworzyć przestrzeń nazw centrum zdarzeń, która będzie odbierać zdarzenia z Azure Digital Twins wystąpienia. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub skorzystać z Azure Portal: [*Szybki start:*](../event-hubs/event-hubs-create.md)tworzenie centrum zdarzeń przy użyciu Azure Portal . Aby sprawdzić, które regiony obsługują centra zdarzeń, odwiedź stronę [*Dostępne produkty platformy Azure według regionów.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Jeśli zostanie wyświetlany komunikat o błędzie z informacją , upewnij się, że nazwa wybrana dla przestrzeni nazw spełnia wymagania dotyczące nazewnictwa opisane w tym dokumencie `BadRequest: The specified service namespace is invalid.` referencyjnym: Create Namespace (Utwórz przestrzeń [nazw).](/rest/api/servicebus/create-namespace)

Będziesz używać tej przestrzeni nazw usługi Event Hubs do przechowywania dwóch centrów zdarzeń wymaganych w tym artykule:

  1. **Centrum bliźniaczych reprezentacji** — centrum zdarzeń do odbierania zdarzeń zmiany bliźniaczej reprezentacji
  2. **Centrum szeregów czasu —** centrum zdarzeń do strumieniowego przesyłania zdarzeń do Time Series Insights

W następnych sekcjach otworzymy i konfigurujemy te centra w przestrzeni nazw centrum zdarzeń.

## <a name="create-twins-hub"></a>Tworzenie centrum bliźniaczych reprezentacji

Pierwszym centrum zdarzeń, które utworzysz w tym artykule, jest **centrum bliźniaczych reprezentacji**. To centrum zdarzeń będzie odbierać zdarzenia zmiany bliźniaczej reprezentacji z Azure Digital Twins.
Aby skonfigurować centrum bliźniaczych reprezentacji, wykonaj następujące kroki w tej sekcji:

1. Tworzenie centrum bliźniaczych reprezentacji
2. Tworzenie reguły autoryzacji w celu kontrolowania uprawnień do centrum
3. Utwórz punkt końcowy w Azure Digital Twins, który używa reguły autoryzacji do uzyskiwania dostępu do centrum
4. Tworzenie trasy w u Azure Digital Twins, która wysyła zdarzenie aktualizacji bliźniaczej reprezentacji do punktu końcowego i centrum połączonych bliźniaczych reprezentacji
5. Uzyskiwanie parametrów połączenia centrum bliźniaczych reprezentacji

Utwórz centrum **bliźniaczych reprezentacji za** pomocą następującego polecenia interfejsu wiersza polecenia. Określ nazwę centrum bliźniaczych reprezentacji.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Tworzenie reguły autoryzacji centrum bliźniaczych reprezentacji

Utwórz [regułę autoryzacji z](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) uprawnieniami wysyłania i odbierania. Określ nazwę reguły.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Tworzenie punktu końcowego centrum bliźniaczych reprezentacji

Utwórz punkt [Azure Digital Twins,](concepts-route-events.md#create-an-endpoint) który łączy centrum zdarzeń z Azure Digital Twins wystąpieniem. Określ nazwę punktu końcowego centrum bliźniaczych reprezentacji.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Tworzenie trasy zdarzeń centrum bliźniaczych reprezentacji

Azure Digital Twins mogą emitować zdarzenia aktualizacji bliźniaczych [reprezentacji](how-to-interpret-event-data.md) przy każdej aktualizacji stanu bliźniaczej reprezentacji. W tej sekcji utworzysz trasę zdarzeń  usługi Azure Digital Twins, która będzie kierować te zdarzenia aktualizacji do centrum bliźniaczych reprezentacji w celu dalszego przetwarzania.

Utwórz [trasę w](concepts-route-events.md#create-an-event-route) Azure Digital Twins, aby wysyłać zdarzenia aktualizacji bliźniaczej reprezentacji do punktu końcowego z powyższego punktu końcowego. Filtr w tej trasie umożliwi tylko przesłania komunikatów aktualizacji bliźniaczych reprezentacji do punktu końcowego. Określ nazwę trasy zdarzeń centrum bliźniaczych reprezentacji.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum bliźniaczych reprezentacji

Pobierz ciąg [połączenia centrum zdarzeń bliźniaczych](../event-hubs/event-hubs-get-connection-string.md)reprezentacji przy użyciu reguł autoryzacji utworzonych powyżej dla centrum bliźniaczych reprezentacji.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Zanotuj wartość **primaryConnectionString z** wyniku, aby skonfigurować ustawienie aplikacji centrum bliźniaczych reprezentacji w dalszej części tego artykułu.

## <a name="create-time-series-hub"></a>Tworzenie centrum szeregów czasu

Drugim centrum zdarzeń, które utworzysz w tym artykule, jest **centrum szeregów czasu**. Jest to centrum zdarzeń, które będzie przesyłać strumieniowo zdarzenia Azure Digital Twins do Time Series Insights.
Aby skonfigurować centrum szeregów czasu, wykonaj następujące kroki:

1. Tworzenie centrum szeregów czasu
2. Tworzenie reguły autoryzacji w celu kontrolowania uprawnień do centrum
3. Uzyskiwanie parametrów połączenia centrum szeregów czasu

Później, podczas tworzenia Time Series Insights, połączysz to centrum szeregów czasu jako źródło zdarzeń dla Time Series Insights wystąpienia.

Utwórz centrum **szeregów czasu za** pomocą następującego polecenia. Określ nazwę centrum szeregów czasu.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Tworzenie reguły autoryzacji centrum szeregów czasu

Utwórz [regułę autoryzacji z](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) uprawnieniami do wysyłania i odbierania. Określ nazwę reguły uwierzytelniania centrum szeregów czasu.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum szeregów czasu

Pobierz ciąg [połączenia centrum szeregów czasu](../event-hubs/event-hubs-get-connection-string.md)przy użyciu reguł autoryzacji utworzonych powyżej dla centrum szeregów czasu:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Zanotuj **wartość primaryConnectionString** z wyniku, aby skonfigurować ustawienie aplikacji centrum szeregów czasu w dalszej części tego artykułu.

Ponadto zanotuj następujące wartości, aby użyć ich później do utworzenia Time Series Insights wystąpienia.
* Przestrzeń nazw centrum zdarzeń
* Nazwa centrum szeregów czasowych
* Reguła uwierzytelniania centrum szeregów czasu

## <a name="create-a-function"></a>Tworzenie funkcji

W tej sekcji utworzysz funkcję platformy Azure, która przekonwertuje zdarzenia aktualizacji bliźniaczych reprezentacji z ich oryginalnego formularza jako dokumenty JSON Patch na obiekty JSON zawierające tylko zaktualizowane i dodane wartości z bliźniaczych reprezentacji.

### <a name="step-1-create-function-app"></a>Krok 1. Tworzenie aplikacji funkcji

Najpierw utwórz nowy projekt aplikacji funkcji w Visual Studio. Aby uzyskać instrukcje dotyczące tego, jak to zrobić, zobacz sekcję Tworzenie aplikacji funkcji Visual Studio w artykule *Instrukcje: konfigurowanie* funkcji do przetwarzania danych. [](how-to-create-azure-function.md#create-a-function-app-in-visual-studio)

### <a name="step-2-add-a-new-function"></a>Krok 2. Dodawanie nowej funkcji

Utwórz nową funkcję platformy Azure o nazwie *ProcessDTUpdatetoTSI.cs,* aby zaktualizować zdarzenia telemetrii urządzenia do Time Series Insights. Typem funkcji będzie wyzwalacz **centrum zdarzeń**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Zrzut ekranu Visual Studio tworzenia nowej funkcji platformy Azure typu wyzwalacz centrum zdarzeń.":::

### <a name="step-3-fill-in-function-code"></a>Krok 3. Wypełnianie kodu funkcji

Dodaj następujące pakiety do projektu:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Zastąp kod w pliku *ProcessDTUpdatetoTSI.cs* następującym kodem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Zapisz kod funkcji.

### <a name="step-4-publish-the-function-app-to-azure"></a>Krok 4. Publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt za pomocą *funkcji ProcessDTUpdatetoTSI.cs* w aplikacji funkcji na platformie Azure.

Aby uzyskać instrukcje dotyczące tego, jak to zrobić, zobacz sekcję Publikowanie aplikacji funkcji na platformie [**Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) w artykule *Instrukcje: konfigurowanie* funkcji do przetwarzania danych.

Zapisz nazwę aplikacji funkcji, aby użyć jej później do skonfigurowania ustawień aplikacji dla dwóch centrów zdarzeń.

### <a name="step-5-security-access-for-the-function-app"></a>Krok 5. Dostęp zabezpieczeń dla aplikacji funkcji

Następnie **przypisz rolę dostępu** dla  funkcji i skonfiguruj ustawienia aplikacji, aby uzyskać dostęp do Azure Digital Twins wystąpienia. Aby uzyskać instrukcje dotyczące tego, [](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) jak to zrobić, zobacz sekcję Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji w artykule *Instrukcje: konfigurowanie* funkcji do przetwarzania danych.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Krok 6. Konfigurowanie ustawień aplikacji dla dwóch centrów zdarzeń

Następnie dodasz zmienne środowiskowe w ustawieniach aplikacji funkcji, które umożliwiają jej dostęp do centrum bliźniaczych reprezentacji i centrum szeregów czasu.

Użyj zapisanej wcześniej wartości **primaryConnectionString** centrum bliźniaczych reprezentacji, aby utworzyć ustawienie aplikacji w aplikacji funkcji zawierające ciąg połączenia centrum bliźniaczych reprezentacji:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Użyj zapisanej wcześniej wartości **primaryConnectionString** centrum szeregów czasu, aby utworzyć w aplikacji funkcji ustawienie aplikacji zawierające ciąg połączenia centrum szeregów czasu:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Tworzenie i łączenie wystąpienia usługi Time Series Insights

W tej sekcji skonfigurujesz wystąpienie Time Series Insights do odbierania danych z centrum szeregów czasu. Aby uzyskać więcej informacji na temat tego procesu, zobacz [*Samouczek: konfigurowanie środowiska Azure Time Series Insights Gen2.*](../time-series-insights/tutorial-set-up-environment.md) Wykonaj poniższe kroki, aby utworzyć środowisko usługi Time Series Insights.

1. Na stronie [Azure Portal](https://portal.azure.com)wyszukaj Time Series Insights *,* a następnie wybierz **przycisk** Dodaj. Wybierz następujące opcje, aby utworzyć środowisko szeregów czasu.

    * **Subskrypcja** — wybierz swoją subskrypcję.
        - **Grupa zasobów** — wybierz grupę zasobów.
    * **Nazwa środowiska** — określ nazwę środowiska szeregów czasu.
    * **Lokalizacja** — wybierz lokalizację.
    * **Warstwa** — wybierz **warstwę cenową Gen2(L1).**
    * **Nazwa właściwości** — **wprowadź $dtId** (Więcej informacji na temat wybierania wartości identyfikatora znajduje się w te tematu Best practices for choosing a Time Series ID (Najlepsze rozwiązania dotyczące [*wybierania identyfikatora szeregu czasowego).*](../time-series-insights/how-to-select-tsid.md)
    * **Nazwa konta magazynu** — określ nazwę konta magazynu.
    * **Włącz ciepły magazyn —** dla tego pola pozostaw wartość *Tak.*

    Możesz pozostawić wartości domyślne dla innych właściwości na tej stronie. Wybierz przycisk **Dalej: Źródło >** zdarzenia.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia Time Series Insights środowiska. Wybierz subskrypcję, grupę zasobów i lokalizację z odpowiednich listy rozwijanych, a następnie wybierz nazwę środowiska." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia Time Series Insights środowiska. Warstwa cenowa Gen2(L1) jest zaznaczona, a nazwa właściwości identyfikatora szeregu czasowego jest $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Na *karcie Źródło zdarzeń* wybierz następujące pola:

   * **Tworzysz źródło zdarzeń?** — Wybierz *pozycję Tak.*
   * **Typ źródła** — wybierz *pozycję Centrum zdarzeń.*
   * **Nazwa** — określ nazwę źródła zdarzeń.
   * **Subskrypcja** — wybierz subskrypcję platformy Azure.
   * **Przestrzeń nazw centrum zdarzeń** — wybierz przestrzeń nazw utworzoną wcześniej w tym artykule.
   * **Nazwa centrum zdarzeń** — wybierz nazwę **centrum szeregów czasu** utworzoną wcześniej w tym artykule.
   * **Nazwa zasad dostępu centrum zdarzeń** — wybierz regułę *uwierzytelniania* centrum szeregów czasu utworzoną wcześniej w tym artykule.
   * **Grupa odbiorców centrum zdarzeń** — wybierz *pozycję Nowa* i określ nazwę grupy odbiorców centrum zdarzeń. Następnie wybierz pozycję *Dodaj*.
   * **Nazwa właściwości** — pozostaw to pole puste.
    
    Wybierz przycisk **Przejrzyj i utwórz,** aby przejrzeć wszystkie szczegóły. Następnie ponownie wybierz **przycisk Przejrzyj i utwórz,** aby utworzyć środowisko szeregów czasu.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym Time Series Insights środowisko. Tworzysz źródło zdarzeń z powyższymi informacjami o centrum zdarzeń. Tworzysz również nową grupę odbiorców." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Wysyłanie danych IoT do Azure Digital Twins

Aby rozpocząć wysyłanie danych do Time Series Insights, musisz rozpocząć aktualizowanie właściwości bliźniaczych reprezentacji cyfrowych w oknie Azure Digital Twins zmianą wartości danych.

Użyj następującego polecenia interfejsu wiersza polecenia, aby zaktualizować właściwość *Temperature* w bliźniaczej reprezentacji *termostat67* dodanej do wystąpienia w [sekcji Wymagania](#prerequisites) wstępne.

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Powtórz polecenie co najmniej 4** razy z różnymi wartościami temperatury, aby utworzyć kilka punktów danych, które można zaobserwować później w Time Series Insights danych.

> [!TIP]
> Jeśli chcesz ukończyć ten artykuł przy użyciu symulowanych danych na żywo zamiast ręcznie aktualizować wartości cyfrowych [](#prerequisites) bliźniaczych reprezentacji, najpierw upewnij się, że ukończono poradę z sekcji Wymagania wstępne, aby skonfigurować funkcję platformy Azure, która aktualizuje bliźniacze bliźniacze reprezentacji z urządzenia symulowanego.
Następnie możesz uruchomić urządzenie teraz, aby rozpocząć wysyłanie symulowanych danych i aktualizowanie bliźniaczej reprezentacji cyfrowej za pośrednictwem tego przepływu danych.

## <a name="visualize-your-data-in-time-series-insights"></a>Wizualizowanie danych w usłudze Time Series Insights

Teraz dane powinny przepływać do Time Series Insights, gotowe do analizy. Wykonaj poniższe kroki, aby eksplorować dane, które się znajdują.

1. W [Azure Portal](https://portal.azure.com)wyszukaj nazwę utworzonego wcześniej środowiska szeregów czasu. W opcjach menu po lewej stronie wybierz pozycję *Przegląd,* aby wyświetlić *Eksplorator usługi Time Series Insights URL.* Wybierz adres URL, aby wyświetlić zmiany temperatury odzwierciedlone w Time Series Insights danych.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby wybrać adres URL eksploratora Time Series Insights na karcie przeglądu Time Series Insights środowiska." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. W eksploratorze zobaczysz bliźniacze reprezentacji w wystąpieniu Azure Digital Twins po lewej stronie. Wybierz *bliźniacze reprezentacji termostat67,* wybierz właściwość *Temperature* i wybierz pozycję **Dodaj**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Zrzut ekranu przedstawiający eksploratora Time Series Insights, aby wybrać pozycję termostat67, wybrać temperaturę właściwości i wybrać pozycję Dodaj." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Powinny zostać wyświetlone początkowe odczyty temperatury z termostatu, jak pokazano poniżej. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Zrzut ekranu przedstawiający eksploratora usługi TSI w celu wyświetlenia danych dotyczących początkowej temperatury. Jest to linia losowych wartości z 68 do 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Jeśli zezwolisz na znacznie dłuższe uruchamianie symulacji, wizualizacja będzie wyglądać mniej więcej tak:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Zrzut ekranu przedstawiający eksploratora tsi, w którym dane temperatury dla każdej bliźniaczej reprezentacji są grafowane w trzech równoległych wierszach różnych kolorów." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Następne kroki

Bliźniacze reprezentacji cyfrowych są przechowywane domyślnie jako płaska hierarchia w Time Series Insights, ale można je wzbogacić o informacje o modelu i hierarchię wielopoziomową dla organizacji. Aby dowiedzieć się więcej na temat tego procesu, przeczytaj: 

* [*Samouczek: definiowanie i stosowanie modelu*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Możesz napisać logikę niestandardową, aby automatycznie dostarczać te informacje przy użyciu modelu i danych grafu już przechowywanych w Azure Digital Twins. Aby dowiedzieć się więcej na temat zarządzania, uaktualniania i pobierania informacji z grafu bliźniaczych reprezentacji, zobacz następujące informacje:

* [*How-to: Manage a digital twin (Jak zarządzać bliźniaczą reprezentacjią cyfrową)*](./how-to-manage-twin.md)
* [*How-to: Query the twin graph (Graf bliźniaczej reprezentacji zapytania)*](./how-to-query-graph.md)