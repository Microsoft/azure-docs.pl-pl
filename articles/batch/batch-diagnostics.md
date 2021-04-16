---
title: Metryki, alerty i dzienniki diagnostyczne
description: Rejestruj i analizuj zdarzenia dziennika diagnostycznego dla Azure Batch kont, takich jak pule i zadania.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389319"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki, alerty i dzienniki usługi Batch do oceny diagnostycznej i monitorowania

Azure Monitor zbiera [metryki i dzienniki](../azure-monitor/essentials/data-platform-metrics.md) diagnostyczne [dla](../azure-monitor/essentials/platform-logs-overview.md) zasobów na Azure Batch konta.

Te dane można zbierać i korzystać z nich na różne sposoby, aby monitorować konto usługi Batch i diagnozować problemy. Możesz również skonfigurować [alerty dotyczące metryk,](../azure-monitor/alerts/alerts-overview.md) aby otrzymywać powiadomienia, gdy metryka osiągnie określoną wartość.

## <a name="batch-metrics"></a>Metryki usługi Batch

[Metryki](../azure-monitor/essentials/data-platform-metrics.md)  to dane telemetryczne platformy Azure (nazywane również licznikami wydajności), które są emitowane przez zasoby platformy Azure i używane przez Azure Monitor usługę. Przykładami metryk na koncie usługi Batch są zdarzenia tworzenia puli, Low-Priority liczba węzłów i Zdarzenia ukończenia zadania. Te metryki mogą pomóc w identyfikowaniu trendów i mogą być używane do analizy danych.

Zobacz listę [obsługiwanych metryk usługi Batch.](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)

Metryki są:

- Domyślnie włączone na każdym koncie usługi Batch bez dodatkowej konfiguracji
- Generowane co 1 minutę
- Nie są utrwalane automatycznie, ale mają 30-dniową historię kroczącą. Metryki aktywności można utrwalać w ramach rejestrowania diagnostycznego.

## <a name="view-batch-metrics"></a>Wyświetlanie metryk usługi Batch

W Azure Portal **stronie** Przegląd dla konta usługi Batch będą domyślnie wyświetlane metryki węzła klucza, rdzenia i zadania.

Aby wyświetlić dodatkowe metryki dla konta usługi Batch:

1. Na stronie Azure Portal pozycję **Wszystkie usługi** Konta usługi Batch, a następnie wybierz nazwę konta usługi  >  Batch.
1. W obszarze **Monitorowanie** wybierz pozycję **Metryki**.
1. Wybierz **pozycję Dodaj metrykę,** a następnie wybierz metrykę z listy rozwijanej.
1. Wybierz opcję **Agregacja** dla metryki. W przypadku metryk opartych na liczbach (takich jak "Dedykowana liczba rdzeni" lub "Liczba węzłów o niskim priorytecie") użyj **agregacji Średnia.** W przypadku metryk opartych na zdarzeniach (takich jak "Zdarzenia ukończenia zmiany rozmiaru puli") użyj **agregacji Liczba**". Unikaj **używania agregacji** Suma, która sumuje wartości wszystkich punktów danych odebranych w okresie wykresu.
1. Aby dodać dodatkowe metryki, powtórz kroki 3 i 4.

Metryki można również pobrać programowo za pomocą interfejsów API Azure Monitor api. Aby uzyskać przykład, zobacz [Pobieranie metryk Azure Monitor za pomocą .NET.](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/)

> [!NOTE]
> Metryki emitowane w ciągu ostatnich 3 minut mogą nadal być agregujące, więc wartości mogą być w tym czasie zgłaszane jako niedostrzegające. Dostarczanie metryk nie jest gwarantowane i może mieć na nie wpływ dostarczanie poza kolejnością, utrata danych lub duplikacja.

## <a name="batch-metric-alerts"></a>Alerty dotyczące metryk usługi Batch

Możesz skonfigurować alerty metryk niemal w czasie rzeczywistym, które są wyzwalane, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje powiadomienie, gdy alert jest "Aktywowany" (po przekroczeniu progu i spełnianiu warunku alertu), a także gdy zostanie "Rozwiązany" (gdy próg zostanie ponownie skrzyżowany, a warunek nie jest już spełniony).

Ponieważ dostarczanie metryk może podlegać niespójnościom, takim jak dostarczanie poza kolejnością, utrata danych lub duplikowanie, zalecamy unikanie alertów wyzwalanych w jednym punkcie danych. Zamiast tego należy użyć progów, aby uwzględnić wszelkie niespójności, takie jak dostarczanie poza kolejnością, utrata danych i duplikacja w czasie.

Na przykład można skonfigurować alert metryki, gdy liczba rdzeni o niskim priorytecie spadnie do określonego poziomu, aby można było dostosować kompozycję pul. Aby uzyskać najlepsze wyniki, ustaw okres co najmniej 10 minut, w którym alert zostanie wyzwolony, jeśli średnia liczba rdzeni o niskim priorytecie spadnie poniżej wartości progowej dla całego okresu. Dzięki temu można agregować metryki, aby uzyskać dokładniejsze wyniki.

Aby skonfigurować alert metryki w Azure Portal:

1. Wybierz **pozycję Wszystkie usługi** Konta usługi Batch, a następnie wybierz nazwę konta usługi  >  Batch.
1. W **obszarze Monitorowanie** wybierz pozycję **Alerty,** a następnie wybierz pozycję Nowa reguła **alertu.**
1. Wybierz **pozycję Dodaj warunek,** a następnie wybierz metrykę.
1. Wybierz żądane wartości dla **opcji Okres wykresu,** **Próg,** **Operator** i **Typ agregacji.**
1. Wprowadź wartość **progową i** wybierz **jednostkę** dla progu.  Następnie wybierz pozycję **Done** (Gotowe).
1. Dodaj [grupę akcji](../azure-monitor/alerts/action-groups.md) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
1. W sekcji **Szczegóły reguły alertu** wprowadź nazwę reguły **alertu i** opis .  Jeśli chcesz natychmiast włączyć alert, upewnij się, że pole Włącz regułę **alertu po utworzeniu** jest zaznaczone.
1. Wybierz pozycję **Utwórz regułę alertu**.

Aby uzyskać więcej informacji na temat tworzenia alertów metryk, zobacz Understand [how metric alerts work in Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) and [Create, view,](../azure-monitor/alerts/alerts-metric.md)and manage metric alerts using Azure Monitor .

Alert niemal w czasie rzeczywistym można również skonfigurować przy użyciu interfejsu [API REST](/rest/api/monitor/)usługi Azure Monitor . Aby uzyskać więcej informacji, zobacz [Omówienie alertów w programie Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Aby uwzględnić w alertach informacje dotyczące zadania, zadania lub puli, zobacz Odpowiadanie na zdarzenia za [pomocą Azure Monitor alertów.](../azure-monitor/alerts/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

[Dzienniki diagnostyczne](../azure-monitor/essentials/platform-logs-overview.md) zawierają informacje emitowane przez zasoby platformy Azure, które opisują działanie każdego zasobu. W przypadku usługi Batch można zebrać następujące dzienniki:

- **ServiceLog:** [zdarzenia emitowane przez usługę Batch](#service-log-events) w okresie istnienia pojedynczego zasobu, takiego jak pula lub zadanie.
- **AllMetrics:** metryki na poziomie konta usługi Batch.

Należy jawnie włączyć ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destination-options"></a>Opcje miejsca docelowego dziennika

Powszechnym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby przechowywać dzienniki w usłudze Azure Storage, utwórz konto przed włączeniem zbierania dzienników. Jeśli konto magazynu jest skojarzone z kontem usługi Batch, możesz wybrać to konto jako miejsce docelowe dziennika.

Alternatywnie można:

- Przesyłanie strumieniowe zdarzeń dziennika diagnostycznego usługi Batch do [usługi Azure Event Hubs.](../event-hubs/event-hubs-about.md) Event Hubs pozysować miliony zdarzeń na sekundę, które można następnie przekształcać i przechowywać przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym.
- Wysyłaj dzienniki diagnostyczne [do Azure Monitor ,](../azure-monitor/logs/log-query-overview.md)gdzie możesz je analizować lub eksportować do analizy w programie Power BI Excel.

> [!NOTE]
> Przechowywanie lub przetwarzanie danych dzienników diagnostycznych za pomocą usług platformy Azure może wiązać się z dodatkowymi kosztami.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włączanie zbierania dzienników diagnostycznych usługi Batch

Aby utworzyć nowe ustawienie diagnostyczne w Azure Portal, wykonaj poniższe kroki.

1. W chmurze Azure Portal pozycję **Wszystkie usługi** Konta usługi  >  **Batch,** a następnie wybierz nazwę konta usługi Batch.
2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
3. W **ustawieniach diagnostycznych** wybierz pozycję **Dodaj ustawienie diagnostyczne.**
4. Wprowadź nazwę ustawienia.
5. Wybierz miejsce docelowe: **Wyślij do usługi Log Analytics,** **Zarchiwizuj** na konto magazynu lub **Prześlij strumieniowo do centrum zdarzeń.** W przypadku wybrania konta magazynu możesz opcjonalnie wybrać liczbę dni przechowywania danych dla każdego dziennika. Jeśli nie określisz liczby dni przechowywania, dane zostaną zachowane przez okres życia konta magazynu.
6. Wybierz **pozycję ServiceLog,** **AllMetrics** lub oba te typy.
7. Wybierz **pozycję Zapisz,** aby utworzyć ustawienie diagnostyczne.

Zbieranie dzienników można również włączyć, tworząc ustawienia diagnostyczne w Azure Portal [,](../azure-monitor/essentials/diagnostic-settings.md)przy użyciu szablonu [Resource Manager](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)lub przy użyciu Azure PowerShell wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Overview of Azure platform logs (Omówienie dzienników platformy Azure).](../azure-monitor/essentials/platform-logs-overview.md)

### <a name="access-diagnostics-logs-in-storage"></a>Uzyskiwanie dostępu do dzienników diagnostycznych w magazynie

W przypadku [archiwizacji dzienników](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)diagnostycznych usługi Batch na koncie magazynu kontener magazynu jest tworzony na koncie magazynu natychmiast po wystąpienia powiązanego zdarzenia. Obiekty blob są tworzone zgodnie z następującym wzorcem nazewnictwa:

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

Każdy plik obiektu blob zawiera zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonej w `PT1H.json` adresie URL obiektu blob (na przykład `h=12` ). W ciągu tej godziny zdarzenia są dołączane do `PT1H.json` pliku w czasie ich wystąpienia. Wartość minuty ( ) jest zawsze , ponieważ zdarzenia dziennika diagnostycznego są podzielone `m=00` na poszczególne obiekty blob na `00` godzinę. (Wszystkie godziny są w czasie UTC).

Poniżej znajduje się przykład `PoolResizeCompleteEvent` wpisu w `PT1H.json` pliku dziennika. Zawiera informacje o bieżącej i docelowej liczbie węzłów dedykowanych i o niskim priorytecie, a także o godzinie rozpoczęcia i zakończenia operacji:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Aby programowo uzyskać dostęp do dzienników na koncie magazynu, użyj interfejsów API usługi Storage.

### <a name="service-log-events"></a>Zdarzenia dziennika usługi

Azure Batch usługi zawierają zdarzenia emitowane przez usługę Batch w okresie istnienia pojedynczego zasobu usługi Batch, takiego jak pula lub zadanie. Każde zdarzenie emitowane przez partię jest rejestrowane w formacie JSON. Na przykład jest to treść przykładowego zdarzenia **tworzenia puli**:

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Zdarzenia dziennika usługi emitowane przez usługę Batch są następujące:

- [Tworzenie puli](batch-pool-create-event.md)
- [Rozpoczynanie usuwania puli](batch-pool-delete-start-event.md)
- [Ukończono usuwanie puli](batch-pool-delete-complete-event.md)
- [Rozpoczynanie zmiany rozmiaru puli](batch-pool-resize-start-event.md)
- [Zmiana rozmiaru puli jest ukończona](batch-pool-resize-complete-event.md)
- [Automatyczne skalowanie puli](batch-pool-autoscale-event.md)
- [Uruchamianie zadania](batch-task-start-event.md)
- [Ukończono zadanie](batch-task-complete-event.md)
- [Niepowodzenie zadania](batch-task-fail-event.md)
- [Harmonogram zadań nie powiódł się](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
- Dowiedz się więcej o [monitorowaniu rozwiązań usługi Batch.](monitoring-overview.md)
