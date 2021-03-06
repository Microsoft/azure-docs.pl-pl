---
title: Analizowanie dzienników i metryk w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak analizować dane diagnostyczne w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c354435f689f5182dd368fd53fdab28f1ab63d48
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218795"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Korzystając z funkcji diagnostyki chmury wiosennej platformy Azure, można analizować dzienniki i metryki przy użyciu dowolnej z następujących usług:

* Użyj usługi Azure Log Analytics, w której dane są zapisywane w usłudze Azure Storage. Podczas eksportowania dzienników do Log Analytics istnieje opóźnienie.
* Zapisz dzienniki na koncie magazynu, aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach).
* Przesyłaj strumieniowo dzienniki do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej.

Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować.

> [!TIP]
> Chcesz tylko przesłać strumieniowo dzienniki? Sprawdź to [polecenie interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/spring-cloud/spring-cloud/app#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Dzienniki

|Dziennik | Opis |
|----|----|
| **ApplicationConsole** | Dziennik konsoli wszystkich aplikacji klienta. |
| **SystemLogs** | Obecnie w tej kategorii znajdują się tylko [źródła dzienników serwera konfiguracji chmury](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) . |

## <a name="metrics"></a>Metryki

Aby uzyskać pełną listę metryk, zobacz [sprężynowe metryki w chmurze](./spring-cloud-concept-metrics.md#user-metrics-options).

Aby rozpocząć, włącz jedną z tych usług, aby otrzymywać dane. Aby dowiedzieć się więcej o konfigurowaniu Log Analytics, zobacz Wprowadzenie do [log Analytics w Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurowanie ustawień diagnostycznych

1. W Azure Portal przejdź do wystąpienia chmury wiosennej platformy Azure.
1. Wybierz opcję **Ustawienia diagnostyczne** , a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę ustawienia, a następnie wybierz miejsce, w którym chcesz wysłać dzienniki. Można wybrać dowolną kombinację następujących trzech opcji:
    * **Archiwizowanie na koncie magazynu**
    * **Przesyłanie strumieniowe do centrum zdarzeń**
    * **Wysyłanie do usługi Log Analytics**

1. Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania ma zastosowanie tylko do konta magazynu.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> 1. Od momentu, gdy dzienniki lub metryki są emitowane i pojawiają się na koncie magazynu, centrum zdarzeń lub Log Analytics.
> 1. Jeśli wystąpienie chmury wiosennej platformy Azure zostanie usunięte lub przeniesione, operacja nie spowoduje przełączenia kaskadowego do zasobów **ustawień diagnostycznych** . Należy ręcznie usunąć zasoby **ustawień diagnostycznych** przed operacją nadrzędną, czyli wystąpieniem usługi Azure wiosny Cloud. W przeciwnym razie, jeśli nowe wystąpienie chmury Azure wiosny jest obsługiwane przy użyciu tego samego identyfikatora zasobu co usunięty, lub jeśli wystąpienie chmury wiosennej platformy Azure zostanie przeniesione z powrotem, poprzednie zasoby **ustawień diagnostycznych** nadal rozszerzają je.

## <a name="view-the-logs-and-metrics"></a>Wyświetlanie dzienników i metryk
Istnieją różne metody wyświetlania dzienników i metryk zgodnie z opisem w poniższych nagłówkach.

### <a name="use-the-logs-blade"></a>Korzystanie z bloku dzienników

1. W Azure Portal przejdź do wystąpienia chmury wiosennej platformy Azure.
1. Aby otworzyć okienko **przeszukiwania dzienników** , wybierz pozycję **dzienniki**.
1. W polu wyszukiwania **tabel**
   * Aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Aby wyświetlić metryki, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Aby wyświetlić wyniki wyszukiwania, wybierz pozycję **Uruchom**.

### <a name="use-log-analytics"></a>Korzystanie z usługi Log Analytics

1. W Azure Portal w lewym okienku wybierz pozycję **log Analytics**.
1. Wybierz obszar roboczy Log Analytics, który został wybrany podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **przeszukiwania dzienników** , wybierz pozycję **dzienniki**.
1. W polu wyszukiwania **tabele**
   * Aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Aby wyświetlić metryki, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Aby wyświetlić wyniki wyszukiwania, wybierz pozycję **Uruchom**.
1. Dzienniki konkretnej aplikacji lub wystąpienia można wyszukać, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` uwzględnia wielkość liter, ale `=~` nie jest.

Aby dowiedzieć się więcej o języku zapytań używanym w Log Analytics, zobacz [Azure monitor dziennika zapytań](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Korzystanie z konta magazynu

1. W Azure Portal Znajdź **konta magazynu** w lewym panelu nawigacyjnym lub w polu wyszukiwania.
1. Wybierz konto magazynu wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **kontenera obiektów BLOB** , wybierz pozycję **obiekty blob**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj kontener o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj kontener o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do konta magazynu, zobacz temat [Zapisywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Korzystanie z centrum zdarzeń

1. W Azure Portal Znajdź **Event Hubs** w lewym panelu nawigacyjnym lub w polu wyszukiwania.

1. Wyszukaj i wybierz centrum zdarzeń wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **Lista centrów zdarzeń** , wybierz pozycję **Event Hubs**.
1. Aby przejrzeć dzienniki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, Wyszukaj centrum zdarzeń o nazwie **Insights-Metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do centrum zdarzeń, zobacz [przesyłanie strumieniowe danych Diagnostyka Azure w ścieżce gorąca przy użyciu Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics działa z aparatem Kusto, dzięki czemu możesz wysyłać zapytania do dzienników w celu analizy. Aby zapoznać się z krótkim wprowadzeniem do wykonywania zapytań dotyczących dzienników przy użyciu Kusto, zapoznaj się z [samouczkiem log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Dzienniki aplikacji zawierają informacje krytyczne i pełne dzienniki dotyczące kondycji, wydajności i innych aplikacji. W następnych sekcjach znajdują się pewne proste zapytania, które pomagają zrozumieć bieżące i wcześniejsze Stany aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Wyświetlanie dzienników aplikacji z chmury Azure wiosennej

Aby zapoznać się z listą dzienników aplikacji z chmury Azure wiosennej, posortowanej według czasu z najnowszymi wyświetlanymi dziennikami, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokaż wpisy dzienników zawierające błędy lub wyjątki

Aby przejrzeć niesortowane wpisy dziennika wskazujące błąd lub wyjątek, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Użyj tego zapytania, aby znaleźć błędy lub zmodyfikować terminy zapytania, aby znaleźć określone kody błędów lub wyjątki.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Pokaż liczbę błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

Aby utworzyć wykres kołowy, który wyświetla liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej o wysyłaniu zapytań do dzienników aplikacji

Azure Monitor zapewnia szeroką pomoc techniczną dotyczącą wykonywania zapytań dotyczących dzienników aplikacji przy użyciu Log Analytics. Aby dowiedzieć się więcej na temat tej usługi, zobacz [Rozpoczynanie pracy z kwerendami dzienników w Azure monitor](../azure-monitor/logs/get-started-queries.md). Aby uzyskać więcej informacji na temat tworzenia zapytań w celu analizowania dzienników aplikacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Jak skonwertować wielowierszowe ślady stosu Java w jeden wiersz?

Istnieje obejście, aby przekonwertować dane śledzenia stosu wielowierszowego w jeden wiersz. Można zmodyfikować dane wyjściowe dziennika Java w celu ponownego sformatowania komunikatów śledzenia stosu, zastępując znaki nowego wiersza tokenem. W przypadku korzystania z biblioteki Logback języka Java można ponownie sformatować komunikaty śledzenia stosu, dodając `%replace(%ex){'[\r\n]+', '\\n'}%nopex` w następujący sposób:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Następnie można zastąpić token ponownie znakami nowego wiersza w Log Analytics w następujący sposób:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Korzystanie z tej samej strategii dla innych bibliotek dzienników Java może być możliwe.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)