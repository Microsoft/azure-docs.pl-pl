---
title: Metryki, alerty i dzienniki diagnostyczne
description: Rejestruj i Analizuj zdarzenia dziennika diagnostycznego dla zasobów konta Azure Batch, takich jak pule i zadania.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: fe2697c73f2a5f3f0b33cfb598f11f39420ed723
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994115"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki, alerty i dzienniki usługi Batch na potrzeby oceny i monitorowania diagnostyki

W tym artykule wyjaśniono, jak monitorować konto usługi Batch przy użyciu funkcji programu [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [metryki](../azure-monitor/platform/data-platform-metrics.md) i [dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) dla zasobów na koncie usługi Batch. Zbieraj te dane i korzystaj z nich na wiele sposobów, aby monitorować konto w usłudze Batch i diagnozować problemy. Możesz również skonfigurować [alerty metryk](../azure-monitor/platform/alerts-overview.md) , aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość.

## <a name="batch-metrics"></a>Metryki wsadowe

Metryki to dane telemetryczne platformy Azure (nazywane również licznikami wydajności), które są emitowane przez zasoby platformy Azure i zużywane przez usługę Azure Monitor. Przykłady metryk na koncie w usłudze Batch to zdarzenia tworzenia puli, Low-Priority liczbę węzłów i zdarzenia ukończenia zadania.

Zapoznaj się z [listą obsługiwanych metryk wsadowych](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metryki to:

- Domyślnie włączone w każdym koncie wsadowym bez dodatkowej konfiguracji
- Wygenerowane co 1 minutę
- Nie są utrwalane automatycznie, ale zawierają 30-dniową historię zmian. Możesz utrwalać metryki działania w ramach rejestrowania diagnostycznego.

## <a name="view-batch-metrics"></a>Wyświetlanie metryk wsadowych

W Azure Portal stronie **Przegląd** dla konta będą domyślnie wyświetlane metryki węzła, rdzeń i zadanie.

Aby wyświetlić wszystkie metryki konta w usłudze Batch w Azure Portal:

1. W Azure Portal wybierz pozycję **wszystkie usługi**  >  **Batch konta**, a następnie wybierz nazwę swojego konta w usłudze Batch.
2. W obszarze **Monitorowanie** wybierz pozycję **Metryki**.
3. Wybierz pozycję **Dodaj metrykę** , a następnie wybierz metrykę z listy rozwijanej.
4. Wybierz opcję **agregacji** dla metryki. W przypadku metryk opartych na liczbie (takich jak "dedykowana liczba rdzeni" lub "Liczba węzłów o niskim priorytecie") Użyj **średniej** agregacji. W przypadku metryk opartych na zdarzeniach (takich jak "wszystkie zdarzenia dotyczące zmiany rozmiaru puli") Użyj agregacji " **Count**".

   > [!WARNING]
   > Nie należy używać agregacji "Sum", która dodaje wartości wszystkich punktów danych odebranych w okresie wykresu.

5. Aby dodać dodatkowe metryki, powtórz kroki 3 i 4.

Możesz również programowo pobrać metryki za pomocą interfejsów API Azure Monitor. Aby zapoznać się z przykładem, zobacz [pobieranie metryk Azure monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Niezawodność metryki partii

Metryki mogą pomóc identyfikować trendy i mogą służyć do analizy danych. Należy pamiętać, że dostarczenie metryki nie jest gwarantowane i może podlegać dostawie, utracie danych i/lub duplikacji. Z tego względu nie zaleca się używania pojedynczych zdarzeń do alertów lub funkcji wyzwalacza. Zapoznaj się z następną sekcją, aby uzyskać szczegółowe informacje na temat ustawiania progów dla alertów.

Metryki emitowane w ciągu ostatnich 3 minut nadal mogą być agregowane, więc wartości metryk mogą być poddane zgłoszeniu w tym przedziale czasu.

## <a name="batch-metric-alerts"></a>Alerty metryki partii

Można skonfigurować *alerty metryk* niemal w czasie rzeczywistym, które wyzwalają, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje powiadomienie, gdy alert jest "aktywowany" (gdy próg zostanie przekroczony, a warunek alertu jest spełniony), a także wtedy, gdy jest "rozwiązany" (gdy próg zostanie przekroczony, a warunek nie jest już spełniany).

Alerty wyzwalające pojedynczy punkt danych nie są zalecane, ponieważ metryki podlegają dostawie, utracie danych i/lub duplikacji. Podczas tworzenia alertów można użyć progów, aby uwzględnić te niespójności.

Można na przykład skonfigurować alert metryki, gdy liczba rdzeni o niskim priorytecie przepada na określony poziom, aby można było dostosować skład pul. Aby uzyskać najlepsze wyniki, należy ustawić okres co najmniej 10 minut, gdzie wyzwalane są alerty, jeśli średnia liczba rdzeni o niskim priorytecie spadnie poniżej wartości progowej dla całego okresu. Pozwala to na więcej czasu na agregowanie metryk, dzięki czemu można uzyskać dokładniejsze wyniki.

Aby skonfigurować alert dotyczący metryki w Azure Portal:

1. Wybierz kolejno pozycje **wszystkie usługi Usługa**  >  **Batch konta**, a następnie wybierz nazwę swojego konta w usłudze Batch.
2. W obszarze **monitorowanie** wybierz pozycję **alerty**, a następnie wybierz pozycję **Nowa reguła alertu**.
3. Kliknij pozycję **Wybierz warunek**, a następnie wybierz metrykę. Potwierdź wartości **okresu wykresu**, **typu progu**, **operatora** i **typu agregacji**, a następnie wprowadź **wartość progową**. Następnie wybierz pozycję **Done** (Gotowe).
4. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
5. W sekcji **szczegóły reguły alertu** wprowadź nazwę i **Opis** **reguły alertu** , a następnie wybierz **ważność**
6. Wybierz pozycję **Utwórz regułę alertu**.

Aby uzyskać więcej informacji na temat tworzenia alertów metryk, zobacz informacje o tym, [jak alerty metryk działają w Azure monitor](../azure-monitor/platform/alerts-metric-overview.md) i [tworzyć, wyświetlać i zarządzać alertami metryk przy użyciu Azure monitor](../azure-monitor/platform/alerts-metric.md).

Możesz również skonfigurować alert niemal w czasie rzeczywistym za pomocą [interfejsu API REST](/rest/api/monitor/)Azure monitor. Aby uzyskać więcej informacji, zobacz [Omówienie alertów w Microsoft Azure](../azure-monitor/platform/alerts-overview.md). Aby uwzględnić w alertach informacje dotyczące zadania, zadania lub puli, zobacz informacje na temat wyszukiwania zapytań w odpowiedzi na [zdarzenia z alertami Azure monitor](../azure-monitor/learn/tutorial-response.md).

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

Dzienniki diagnostyczne zawierają informacje emitowane przez zasoby platformy Azure opisujące operacje poszczególnych zasobów. W przypadku usługi Batch można zbierać następujące dzienniki:

- **Usługa rejestruje** zdarzenia, które są emitowane przez usługę Azure Batch w okresie istnienia pojedynczego zasobu usługi Batch, takiego jak Pula lub zadanie.
- Dzienniki **metryk** na poziomie konta.

Ustawienia do włączenia zbierania dzienników diagnostycznych nie są domyślnie włączone. Jawnie Włącz ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destinations"></a>Miejsca docelowe dzienników

Typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby przechowywać dzienniki w usłudze Azure Storage, należy utworzyć konto przed włączeniem kolekcji dzienników. Jeśli masz skojarzone konto magazynu z kontem w usłudze Batch, możesz wybrać to konto jako miejsce docelowe dziennika.

Alternatywnie można:

- Zdarzenia dziennika diagnostycznego usługi Stream Part strumienia do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-about.md). Event Hubs może pozyskać miliony zdarzeń na sekundę, które można następnie przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym.
- Wyślij dzienniki diagnostyczne do [dzienników Azure monitor](../azure-monitor/log-query/log-query-overview.md), gdzie można je analizować lub wyeksportować do analizy w programie Power BI lub Excel.

> [!NOTE]
> Za pomocą usług platformy Azure mogą być naliczane dodatkowe koszty związane z przechowywaniem lub przetwarzaniem danych dzienników diagnostycznych.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienników diagnostycznych partii

Aby utworzyć nowe ustawienie diagnostyczne w Azure Portal, wykonaj poniższe kroki.

1. W Azure Portal wybierz pozycję **wszystkie usługi**  >  **Batch konta**, a następnie wybierz nazwę swojego konta w usłudze Batch.
2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
3. W obszarze **Ustawienia diagnostyczne** wybierz pozycję **Dodaj ustawienie diagnostyczne**.
4. Wprowadź nazwę tego ustawienia.
5. Wybierz lokalizację docelową: **wysyłanie do log Analytics**, **Archiwizowanie na koncie magazynu** lub **przesyłanie strumieniowe do centrum zdarzeń**. W przypadku wybrania konta magazynu można opcjonalnie ustawić zasady przechowywania. Jeśli nie określisz liczby dni przechowywania danych, dane są zachowywane w trakcie okresu istnienia konta magazynu.
6. Wybierz opcję **ServiceLog**, **AllMetrics** lub Both.
7. Wybierz pozycję **Zapisz** , aby utworzyć ustawienie diagnostyczne.

Można również [włączyć zbieranie za pośrednictwem Azure monitor w Azure Portal w](../azure-monitor/platform/diagnostic-settings.md) celu skonfigurowania ustawień diagnostycznych, przy użyciu [szablonu Menedżer zasobów](../azure-monitor/samples/resource-manager-diagnostic-settings.md)lub z Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie dzienników platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Dostęp do dzienników diagnostyki w magazynie

W przypadku archiwizowania dzienników diagnostycznych wsadowych na koncie magazynu kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia powiązanego. Obiekty blob są tworzone zgodnie z poniższym wzorcem nazewnictwa:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Każdy `PT1H.json` plik obiektu BLOB zawiera zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonej w adresie URL obiektu BLOB (na przykład `h=12` ). W ciągu tej godziny zdarzenia są dołączane do `PT1H.json` pliku w miarę ich występowania. Wartość minuta ( `m=00` ) jest zawsze `00` , ponieważ zdarzenia dzienników diagnostycznych są dzielone na pojedyncze obiekty blob na godzinę. (Wszystkie czasy są w formacie UTC).

Poniżej znajduje się przykład `PoolResizeCompleteEvent` wpisu w `PT1H.json` pliku dziennika. Zawiera informacje o bieżącej i docelowej liczbie węzłów dedykowanych i o niskim priorytecie oraz o godzinie rozpoczęcia i zakończenia operacji:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Aby uzyskać więcej informacji na temat schematu dzienników diagnostycznych na koncie magazynu, zobacz [Archiwizowanie dzienników zasobów platformy Azure na koncie magazynu](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Aby programowo uzyskać dostęp do dzienników na koncie magazynu, Użyj interfejsów API magazynu.

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Zdarzenia dziennika usługi emitowane przez usługę Batch obejmują następujące elementy:

- [Tworzenie puli](batch-pool-create-event.md)
- [Początek usuwania puli](batch-pool-delete-start-event.md)
- [Ukończono Usuwanie puli](batch-pool-delete-complete-event.md)
- [Początek zmiany rozmiaru puli](batch-pool-resize-start-event.md)
- [Ukończono Zmienianie rozmiaru puli](batch-pool-resize-complete-event.md)
- [Automatyczne skalowanie puli](batch-pool-autoscale-event.md)
- [Rozpoczęcie zadania](batch-task-start-event.md)
- [Zadanie zakończone](batch-task-complete-event.md)
- [Niepowodzenie zadania](batch-task-fail-event.md)
- [Niepowodzenie harmonogramu zadań](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
- Dowiedz się więcej o [monitorowaniu rozwiązań wsadowych](monitoring-overview.md).