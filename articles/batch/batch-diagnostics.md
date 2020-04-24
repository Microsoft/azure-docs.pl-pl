---
title: Metryki, alerty i dzienniki diagnostyczne
description: Rejestruj i Analizuj zdarzenia dziennika diagnostycznego dla zasobów konta Azure Batch, takich jak pule i zadania.
ms.topic: article
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 7f75a8302c8ba368138e6c8edee6c6069c5031d8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117305"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki, alerty i dzienniki usługi Batch na potrzeby oceny i monitorowania diagnostyki

 
W tym artykule wyjaśniono, jak monitorować konto usługi Batch przy użyciu funkcji programu [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [metryki](../azure-monitor/platform/data-platform-metrics.md) i [dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) dla zasobów na koncie usługi Batch. Zbieraj te dane i korzystaj z nich na wiele sposobów, aby monitorować konto w usłudze Batch i diagnozować problemy. Możesz również skonfigurować [alerty metryk](../azure-monitor/platform/alerts-overview.md) , aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość. 

## <a name="batch-metrics"></a>Metryki wsadowe

Metryki to dane telemetryczne platformy Azure (nazywane również licznikami wydajności) emitowane przez zasoby platformy Azure, które są używane przez usługę Azure Monitor. Przykładowe metryki na koncie wsadowym obejmują: zdarzenia tworzenia puli, liczba węzłów o niskim priorytecie oraz zdarzenia ukończenia zadania. 

Zapoznaj się z [listą obsługiwanych metryk wsadowych](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metryki to:

* Domyślnie włączone w każdym koncie wsadowym bez dodatkowej konfiguracji
* Wygenerowane co 1 minutę
* Nie są utrwalane automatycznie, ale zawierają 30-dniową historię zmian. Możesz utrwalać metryki działania w ramach rejestrowania diagnostycznego.

### <a name="view-metrics"></a>Wyświetlanie metryk

Wyświetl metryki dla konta usługi Batch w Azure Portal. Na stronie **Przegląd** konta domyślnie są wyświetlane metryki węzeł klucza, rdzeń i zadanie. 

Aby wyświetlić wszystkie metryki konta w usłudze Batch: 

1. W portalu kliknij kolejno pozycje **wszystkie usługi Usługa** > **Batch konta**, a następnie kliknij nazwę swojego konta w usłudze Batch.
2. W obszarze **monitorowanie**kliknij pozycję **metryki**.
3. Wybierz co najmniej jedną metrykę. Jeśli chcesz, wybierz pozycję dodatkowe metryki zasobów przy użyciu opcji **subskrypcje**, **Grupa zasobów**, **Typ zasobu**i listy rozwijane **zasobów** .
    * W przypadku metryk opartych na liczbie (takich jak "dedykowana liczba rdzeni" lub "Liczba węzłów o niskim priorytecie") Użyj agregacji "Średnia". W przypadku metryk opartych na zdarzeniach (takich jak "zmiany rozmiaru puli zakończonych zdarzeń") Użyj agregacji "Count".

> [!WARNING]
> Nie należy używać agregacji "Sum", która dodaje wartości wszystkich punktów danych odebranych w okresie wykresu
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Aby programowo pobrać metryki, użyj Azure Monitor interfejsów API. Na przykład zobacz [pobieranie metryk Azure monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Niezawodność metryki partii

Metryki są przeznaczone do użycia w analizie trendów i danych. Dostarczanie metryk nie jest gwarantowane i podlega dostawie, utracie danych i/lub duplikacji. Korzystanie z pojedynczych zdarzeń do alertów lub funkcji wyzwalacza nie jest zalecane. Więcej informacji na temat ustawiania progów alertów można znaleźć w sekcji [alerty metryk usługi Batch](#batch-metric-alerts) .

Metryki emitowane w ciągu ostatnich 3 minut nadal mogą być agregowane. W tym przedziale czasowym wartości metryk mogą być poddane zgłoszeniu.

## <a name="batch-metric-alerts"></a>Alerty metryki partii

Opcjonalnie można skonfigurować *alerty metryk* niemal w czasie rzeczywistym, które wyzwalają, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje [powiadomienie](../monitoring-and-diagnostics/insights-alerts-portal.md) wybierane, gdy alert jest "aktywowany" (gdy próg zostanie przekroczony i spełniony jest warunek alertu), a także wtedy, gdy jest "rozwiązany" (gdy próg zostanie przekroczony, a warunek nie jest już spełniany). Alerty na podstawie pojedynczych punktów danych nie są zalecane, ponieważ metryki podlegają dostawie, utracie danych i/lub duplikacji. Alerty powinny używać progów, aby uwzględnić te niespójności.

Można na przykład skonfigurować alert metryki, gdy liczba rdzeni o niskim priorytecie przepada na określony poziom, aby można było dostosować skład pul. Zalecane jest ustawienie okresu co najmniej 10 minut, w których wyzwalane są alerty, jeśli średnia liczba rdzeni o niskim priorytecie spadnie poniżej wartości progowej dla całego okresu. Nie zaleca się zgłaszania alertów w ciągu 1-5 minut, ponieważ metryki nadal mogą być agregowane.

Aby skonfigurować alert dotyczący metryki w portalu:

1. Kliknij kolejno pozycje **wszystkie usługi Usługa** > **Batch konta**, a następnie kliknij nazwę konta w usłudze Batch.
2. W obszarze **monitorowanie**kliknij pozycję **reguły** > alertów**Dodaj alert metryki**.
3. Wybierz metrykę, warunek alertu (na przykład, gdy Metryka przekroczy określoną wartość w danym okresie) i jedno lub więcej powiadomień.

Możesz również skonfigurować alert niemal w czasie rzeczywistym za pomocą [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/). Aby uzyskać więcej informacji, zobacz [Omówienie alertów](../azure-monitor/platform/alerts-overview.md). Aby uwzględnić w alertach informacje dotyczące zadania, zadania lub puli, zobacz informacje na temat wyszukiwania zapytań w odpowiedzi na [zdarzenia z alertami Azure monitor](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

Dzienniki diagnostyczne zawierają informacje emitowane przez zasoby platformy Azure opisujące operacje poszczególnych zasobów. W przypadku usługi Batch można zbierać następujące dzienniki:

* **Usługa rejestruje** zdarzenia, które są emitowane przez usługę Azure Batch w okresie istnienia pojedynczego zasobu usługi Batch, takiego jak Pula lub zadanie. 

* Dzienniki **metryk** na poziomie konta. 

Ustawienia do włączenia zbierania dzienników diagnostycznych nie są domyślnie włączone. Jawnie Włącz ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destinations"></a>Miejsca docelowe dzienników

Typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby przechowywać dzienniki w usłudze Azure Storage, należy utworzyć konto przed włączeniem kolekcji dzienników. Jeśli masz skojarzone konto magazynu z kontem w usłudze Batch, możesz wybrać to konto jako miejsce docelowe dziennika. 

Inne opcjonalne miejsca docelowe dla dzienników diagnostycznych:

* Zdarzenia dziennika diagnostycznego usługi Stream Part strumienia do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs może pozyskać miliony zdarzeń na sekundę, które można następnie przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym. 

* Wyślij dzienniki diagnostyczne do [dzienników Azure monitor](../log-analytics/log-analytics-overview.md), gdzie można je analizować lub wyeksportować do analizy w programie Power BI lub Excel.

> [!NOTE]
> Za pomocą usług platformy Azure mogą być naliczane dodatkowe koszty związane z przechowywaniem lub przetwarzaniem danych dzienników diagnostycznych. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienników diagnostycznych partii

1. W portalu kliknij kolejno pozycje **wszystkie usługi Usługa** > **Batch konta**, a następnie kliknij nazwę swojego konta w usłudze Batch.
2. W obszarze **monitorowanie**kliknij pozycję **dzienniki** > diagnostyczne**Włącz diagnostykę**.
3. W obszarze **Ustawienia diagnostyczne**wprowadź nazwę ustawienia i wybierz miejsce docelowe dziennika (istniejące konto magazynu, centrum zdarzeń lub dzienniki Azure monitor). Wybierz albo zarówno **ServiceLog** , jak i **AllMetrics**.

    Po wybraniu konta magazynu opcjonalnie Ustaw zasady przechowywania. Jeśli nie określisz liczby dni przechowywania danych, dane są zachowywane w trakcie okresu istnienia konta magazynu.

4. Kliknij pozycję **Zapisz**.

    ![Diagnostyka usługi Batch](media/batch-diagnostics/diagnostics-portal.png)

Inne opcje włączania zbierania dzienników: Użyj Azure Monitor w portalu, aby skonfigurować ustawienia diagnostyczne, użyć [szablonu Menedżer zasobów](../azure-monitor/platform/diagnostic-settings-template.md)lub użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Zobacz [zbieranie danych dzienników z zasobów platformy Azure i korzystanie z nich](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Dostęp do dzienników diagnostyki w magazynie

W przypadku archiwizowania dzienników diagnostycznych wsadowych na koncie magazynu kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia powiązanego. Obiekty blob są tworzone zgodnie z poniższym wzorcem nazewnictwa:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Przykład:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Każdy `PT1H.json` plik obiektu BLOB zawiera zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonej w adresie URL `h=12`obiektu BLOB (na przykład). W ciągu tej godziny zdarzenia są dołączane do `PT1H.json` pliku w miarę ich występowania. Wartość minuta (`m=00`) jest zawsze `00`, ponieważ zdarzenia dzienników diagnostycznych są dzielone na pojedyncze obiekty blob na godzinę. (Wszystkie czasy są w formacie UTC).

Poniżej znajduje się przykład `PoolResizeCompleteEvent` wpisu w pliku `PT1H.json` dziennika. Zawiera informacje o bieżącej i docelowej liczbie węzłów dedykowanych i o niskim priorytecie oraz o godzinie rozpoczęcia i zakończenia operacji:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Aby uzyskać więcej informacji na temat schematu dzienników diagnostycznych na koncie magazynu, zobacz [Archiwizowanie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Aby programowo uzyskać dostęp do dzienników na koncie magazynu, Użyj interfejsów API magazynu. 

### <a name="service-log-events"></a>Zdarzenia dziennika usługi
Azure Batch dzienniki usług, jeśli są zbierane, zawierają zdarzenia emitowane przez usługę Azure Batch w trakcie okresu istnienia pojedynczego zasobu usługi Batch, takiego jak Pula lub zadanie. Każde zdarzenie emitowane przez zadanie wsadowe jest rejestrowane w formacie JSON. Na przykład jest to treść przykładowego **zdarzenia tworzenia puli**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Usługa Batch obecnie emituje następujące zdarzenia dziennika usługi. Ta lista może nie być wyczerpująca, ponieważ dodatkowe zdarzenia mogły zostać dodane od czasu ostatniej aktualizacji tego artykułu.

| **Zdarzenia dziennika usługi** |
| --- |
| [Tworzenie puli](batch-pool-create-event.md) |
| [Początek usuwania puli](batch-pool-delete-start-event.md) |
| [Ukończono Usuwanie puli](batch-pool-delete-complete-event.md) |
| [Początek zmiany rozmiaru puli](batch-pool-resize-start-event.md) |
| [Ukończono Zmienianie rozmiaru puli](batch-pool-resize-complete-event.md) |
| [Rozpoczęcie zadania](batch-task-start-event.md) |
| [Zadanie zakończone](batch-task-complete-event.md) |
| [Niepowodzenie zadania](batch-task-fail-event.md) |



## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [monitorowaniu rozwiązań wsadowych](monitoring-overview.md).
