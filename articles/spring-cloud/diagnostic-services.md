---
title: Analizowanie dzienników i metryk w Azure Spring Cloud | Microsoft Docs
description: Dowiedz się, jak analizować dane diagnostyczne w Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874121"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizowanie dzienników i metryk za pomocą ustawień diagnostycznych

**Ten artykuł dotyczy: ✔️** Java ✔️ C #

Za pomocą funkcji diagnostyki Azure Spring Cloud można analizować dzienniki i metryki za pomocą dowolnej z następujących usług:

* Użyj usługi Azure Log Analytics, w której dane są zapisywane w usłudze Azure Storage. Eksportowanie dzienników do usługi Log Analytics jest opóźnione.
* Zapisz dzienniki na koncie magazynu w celu inspekcji lub ręcznej inspekcji. Możesz określić czas przechowywania (w dniach).
* Przesyłanie strumieniowe dzienników do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne.

Wybierz kategorię dziennika i kategorię metryk, które chcesz monitorować.

> [!TIP]
> Chcesz tylko przesyłać strumieniowo dzienniki? Zapoznaj się z tym [poleceniem interfejsu wiersza polecenia platformy Azure!](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)

## <a name="logs"></a>Dzienniki

|Dziennik | Opis |
|----|----|
| **ApplicationConsole** | Dziennik konsoli wszystkich aplikacji klienta. |
| **SystemLogs** | Obecnie tylko [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) w tej kategorii. |

## <a name="metrics"></a>Metryki

Aby uzyskać pełną listę metryk, zobacz [Spring Cloud Metrics](./spring-cloud-concept-metrics.md#user-metrics-options).

Aby rozpocząć, włącz jedną z tych usług, aby odbierać dane. Aby dowiedzieć się więcej na temat konfigurowania usługi Log Analytics, zobacz [Wprowadzenie do usługi Log Analytics w Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurowanie ustawień diagnostycznych

1. W Azure Portal przejdź do swojego Azure Spring Cloud wystąpienia.
1. Wybierz **opcję ustawień diagnostycznych,** a następnie wybierz **pozycję Dodaj ustawienie diagnostyczne.**
1. Wprowadź nazwę ustawienia, a następnie wybierz, dokąd chcesz wysłać dzienniki. Możesz wybrać dowolną kombinację następujących trzech opcji:
    * **Archiwizowanie na koncie magazynu**
    * **Przesyłanie strumieniowe do centrum zdarzeń**
    * **Wysyłanie do usługi Log Analytics**

1. Wybierz kategorię dziennika i kategorię metryk, które chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania dotyczy tylko konta magazynu.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> 1. Może wystąpić do 15 minut przerwy między emitami dzienników lub metryk oraz ich pojawiania się na koncie magazynu, w centrum zdarzeń lub w u usługi Log Analytics.
> 1. Jeśli Azure Spring Cloud zostanie usunięte lub przeniesione, operacja nie będzie kaskadowo niosła zasobów **ustawień diagnostycznych.** Zasoby **ustawień diagnostycznych** należy usunąć ręcznie przed operacją względem jej elementu nadrzędnego, tj. Azure Spring Cloud wystąpienia. W przeciwnym razie, jeśli nowe wystąpienie Azure Spring Cloud zostanie zaaprowizowane z tym samym identyfikatorem zasobu co  usunięte wystąpienie lub jeśli wystąpienie usługi Azure Spring Cloud zostanie przeniesione z powrotem, poprzednie zasoby ustawień diagnostycznych będą rozszerzać je w dalszym ciągu.

## <a name="view-the-logs-and-metrics"></a>Wyświetlanie dzienników i metryk
Istnieją różne metody wyświetlania dzienników i metryk zgodnie z opisem w poniższych nagłówkach.

### <a name="use-the-logs-blade"></a>Korzystanie z bloku Dzienniki

1. W Azure Portal przejdź do swojego Azure Spring Cloud wystąpienia.
1. Aby otworzyć okienko **Przeszukiwanie dzienników,** wybierz pozycję **Dzienniki**.
1. W polu **wyszukiwania** Tabele
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
1. Aby wyświetlić wynik wyszukiwania, wybierz pozycję **Uruchom**.

### <a name="use-log-analytics"></a>Korzystanie z usługi Log Analytics

1. W okienku Azure Portal po lewej stronie wybierz pozycję **Log Analytics.**
1. Wybierz obszar roboczy usługi Log Analytics wybrany podczas dodano ustawienia diagnostyczne.
1. Aby otworzyć okienko **Przeszukiwanie dzienników,** wybierz pozycję **Dzienniki**.
1. W polu **wyszukiwania** Tabele
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

1. Aby wyświetlić wynik wyszukiwania, wybierz pozycję **Uruchom**.
1. Dzienniki określonej aplikacji lub wystąpienia można przeszukiwać, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` Wielkość liter jest zróżnicowa, ale `=~` nie.

Aby dowiedzieć się więcej na temat języka zapytań używanego w uciece Log Analytics, zobacz [Azure Monitor zapytań dziennika](/azure/data-explorer/kusto/query/). Aby wysyłać zapytania do wszystkich dzienników usługi Log Analytics ze scentralizowanego klienta, zapoznaj się [z Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

### <a name="use-your-storage-account"></a>Korzystanie z konta magazynu

1. W okienku Azure Portal konta **magazynu** w lewym panelu nawigacyjnym lub w polu wyszukiwania.
1. Wybierz konto magazynu wybrane podczas dodaniu ustawień diagnostycznych.
1. Aby otworzyć okienko **Kontener obiektów blob,** wybierz **pozycję Obiekty blob**.
1. Aby przejrzeć dzienniki aplikacji, wyszukaj kontener o nazwie **insights-logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, wyszukaj kontener o nazwie **insights-metrics-pt1m.**

Aby dowiedzieć się więcej na temat wysyłania informacji diagnostycznych na konto magazynu, zobacz Przechowywanie i wyświetlanie danych diagnostycznych [w usłudze Azure Storage.](../storage/common/storage-introduction.md)

### <a name="use-your-event-hub"></a>Korzystanie z centrum zdarzeń

1. W okienku Azure Portal **znajdź** Event Hubs panelu nawigacyjnym po lewej stronie lub w polu wyszukiwania.

1. Wyszukaj i wybierz centrum zdarzeń wybrane po dodaniu ustawień diagnostycznych.
1. Aby otworzyć okienko **Lista centrum zdarzeń,** wybierz pozycję **Event Hubs**.
1. Aby przejrzeć dzienniki aplikacji, wyszukaj centrum zdarzeń o nazwie **insights-logs-applicationkonsole**.
1. Aby przejrzeć metryki aplikacji, wyszukaj centrum zdarzeń o nazwie **insights-metrics-pt1m.**

Aby dowiedzieć się więcej na temat wysyłania informacji diagnostycznych do centrum zdarzeń, zobacz [Streaming Diagnostyka Azure data in the hot path by using Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)( Przesyłanie strumieniowe danych w ścieżce gorąca przy użyciu Event Hubs .

## <a name="analyze-the-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics działa z aparatem Kusto, dzięki czemu można rejestrować zapytania dotyczące dzienników w celu ich analizy. Aby zapoznać się z szybkim wprowadzeniem do wykonywania zapytań o dzienniki przy użyciu usługi Kusto, zapoznaj się z [samouczkiem usługi Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

Dzienniki aplikacji zawierają informacje o krytycznym znaczeniu i pełne dzienniki dotyczące kondycji, wydajności i nie tylko. W następnych sekcjach znajdują się proste zapytania, które ułatwiają zrozumienie bieżących i przeszłych stanów aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Wyświetlanie dzienników aplikacji z Azure Spring Cloud

Aby przejrzeć listę dzienników aplikacji z Azure Spring Cloud, posortowanych według czasu przy użyciu najnowszych dzienników pokazanych jako pierwsze, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokazywanie wpisów dzienników zawierających błędy lub wyjątki

Aby przejrzeć niesortowane wpisy dziennika, które wspominają o błędzie lub wyjątku, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Użyj tego zapytania, aby znaleźć błędy, lub zmodyfikuj terminy zapytania, aby znaleźć określone kody błędów lub wyjątki.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Wyświetlanie liczby błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

Aby utworzyć wykres kołowy wyświetlacy liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny, uruchom następujące zapytanie:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej na temat wykonywania zapytań dotyczących dzienników aplikacji

Azure Monitor zapewnia rozbudowaną obsługę wykonywania zapytań o dzienniki aplikacji przy użyciu usługi Log Analytics. Aby dowiedzieć się więcej na temat tej usługi, zobacz [Rozpoczynanie pracy z zapytaniami dziennika w Azure Monitor](../azure-monitor/logs/get-started-queries.md). Aby uzyskać więcej informacji na temat tworzenia zapytań w celu analizowania dzienników aplikacji, zobacz Overview of log queries in Azure Monitor (Omówienie zapytań [dziennika w programie Azure Monitor](../azure-monitor/logs/log-query-overview.md)).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Jak przekonwertować ślady stosu wielo wierszowego języka Java w jeden wiersz?

Istnieje obejście, które pozwala przekonwertować ślady stosu wielo liniowego na jeden wiersz. Możesz zmodyfikować dane wyjściowe dziennika języka Java, aby ponownie sformatować komunikaty śledzenia stosu, zastępując znaki nowego linii tokenem. Jeśli używasz biblioteki Logback języka Java, możesz ponownie sformatować komunikaty śledzenia stosu, dodając `%replace(%ex){'[\r\n]+', '\\n'}%nopex` następujące wartości:

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
Następnie możesz ponownie zastąpić token znakami nowego linii w u usługi Log Analytics, jak podano poniżej:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
W przypadku innych bibliotek dzienników języka Java może być możliwe użycie tej samej strategii.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: wdrażanie pierwszej Azure Spring Cloud aplikacji](spring-cloud-quickstart.md)
