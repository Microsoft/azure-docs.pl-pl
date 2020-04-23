---
title: Analizowanie dzienników i danych w chmurze Azure Spring Cloud | Dokumenty firmy Microsoft
description: Dowiedz się, jak analizować dane diagnostyczne w usłudze Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870409"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizowanie dzienników i danych za pomocą ustawień diagnostycznych

Korzystając z funkcji diagnostyki usługi Azure Spring Cloud, można analizować dzienniki i metryki za pomocą dowolnej z następujących usług:

* Użyj usługi Azure Log Analytics, gdzie dane są zapisywane w usłudze Azure Storage. Występuje opóźnienie podczas eksportowania dzienników do usługi Log Analytics.
* Zapisz dzienniki na koncie magazynu w celu inspekcji lub ręcznej inspekcji. Można określić czas przechowywania (w dniach).
* Przesyłaj strumieniowo dzienniki do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne.

Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować.

> [!TIP]
> Chcesz tylko przesyłać strumieniowo dzienniki? Zapoznaj się z tym [poleceniem interfejsu wiersza polecenia platformy Azure!](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)

## <a name="logs"></a>Dzienniki

|Log | Opis |
|----|----|
| **ApplicationConsole** | Dziennik konsoli wszystkich aplikacji klientów. | 
| **Dzienniki systemu** | Obecnie w tej kategorii loguje się tylko [serwer konfiguracyjny Spring Cloud.](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) |

## <a name="metrics"></a>Metryki

Aby uzyskać pełną listę danych, zobacz [Spring Cloud Metrics](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

Aby rozpocząć, włącz jedną z tych usług, aby odbierać dane. Aby dowiedzieć się więcej o konfigurowaniu usługi Log Analytics, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurowanie ustawień diagnostyki

1. W witrynie Azure portal przejdź do wystąpienia usługi Azure Spring Cloud.
1. Wybierz opcję **Ustawienia diagnostyki,** a następnie wybierz **pozycję Dodaj ustawienie Diagnostyka**.
1. Wprowadź nazwę ustawienia, a następnie wybierz miejsce, w którym chcesz wysłać dzienniki. Można wybrać dowolną kombinację następujących trzech opcji:
    * **Archiwizuj na koncie magazynu**
    * **Przesyłanie strumieniowe do centrum zdarzeń**
    * **Wysyłanie do usługi Log Analytics**

1. Wybierz kategorię dziennika i kategorię metryki, którą chcesz monitorować, a następnie określ czas przechowywania (w dniach). Czas przechowywania dotyczy tylko konta magazynu.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Może istnieć luka do 15 minut między po emisji dzienników lub metryk i gdy pojawiają się one na koncie magazynu, centrum zdarzeń lub usługi Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Wyświetlanie dzienników i danych
Istnieją różne metody wyświetlania dzienników i metryk, zgodnie z opisem w poniższych nagłówkach.

### <a name="use-logs-blade"></a>Użyj kłód bloku

1. W witrynie Azure portal przejdź do wystąpienia usługi Azure Spring Cloud.
1. Aby otworzyć **okienko Wyszukiwanie** **dzienników,** wybierz pozycję Dzienniki .
1. W polu wyszukiwania **dzienników**
   * Aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Aby wyświetlić dane, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Aby wyświetlić wynik wyszukiwania, wybierz pozycję **Uruchom**.

### <a name="use-log-analytics"></a>Korzystanie z usługi Log Analytics

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Usługa Log Analytics**.
1. Wybierz obszar roboczy usługi Log Analytics wybrany podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć **okienko Wyszukiwanie** **dzienników,** wybierz pozycję Dzienniki .
1. W polu wyszukiwania **Dziennik**
   * aby wyświetlić dzienniki, wprowadź proste zapytanie, takie jak:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * aby wyświetlić dane, wprowadź proste zapytanie, takie jak:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Aby wyświetlić wynik wyszukiwania, wybierz pozycję **Uruchom**.
1. Można przeszukiwać dzienniki określonej aplikacji lub wystąpienia, ustawiając warunek filtru:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`wielkość liter, `=~` ale nie jest.

Aby dowiedzieć się więcej o języku zapytań używanym w usłudze Log Analytics, zobacz [Kwerendy dziennika usługi Azure Monitor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Korzystanie z konta magazynu 

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Konta magazynu**.

1. Wybierz konto magazynu wybrane podczas dodawania ustawień diagnostycznych.
1. Aby otworzyć okienko **Kontener obiektów blob,** wybierz pozycję **Obiekty blob**.
1. Aby przejrzeć dzienniki aplikacji, wyszukaj kontener o nazwie **insights-logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, wyszukaj kontener o nazwie **insights-metrics-pt1m**.

Aby dowiedzieć się więcej o wysyłaniu informacji diagnostycznych do konta magazynu, zobacz [Przechowywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Korzystanie z centrum zdarzeń

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Centrum zdarzeń**.

1. Wyszukaj i wybierz centrum zdarzeń wybrane podczas dodawania ustawień diagnostyki.
1. Aby otworzyć okienko **Lista Centrum zdarzeń,** wybierz pozycję **Centra zdarzeń**.
1. Aby przejrzeć dzienniki aplikacji, wyszukaj centrum zdarzeń o nazwie **insights-logs-applicationconsole**.
1. Aby przejrzeć metryki aplikacji, wyszukaj centrum zdarzeń o nazwie **insights-metrics-pt1m**.

Aby dowiedzieć się więcej na temat wysyłania informacji diagnostycznych do centrum zdarzeń, zobacz [Przesyłanie strumieniowe danych diagnostyki platformy Azure w ścieżce gorąca przy użyciu centrum zdarzeń](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analizowanie dzienników

Usługa Azure Log Analytics jest uruchomiona z aparatem Kusto, dzięki czemu można wysyłać zapytania do dzienników do analizy. Aby uzyskać szybkie wprowadzenie do wykonywania zapytań dzienników przy użyciu Kusto, przejrzyj [samouczek analizy dzienników](../azure-monitor/log-query/get-started-portal.md).

Dzienniki aplikacji zawierają krytyczne informacje i pełne dzienniki dotyczące kondycji, wydajności i innych danych aplikacji. W następnych sekcjach są kilka prostych zapytań, które pomogą Ci zrozumieć bieżących i przeszłych stanów aplikacji.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Pokaż dzienniki aplikacji z usługi Azure Spring Cloud

Aby przejrzeć listę dzienników aplikacji z usługi Azure Spring Cloud, posortowane według czasu z najnowszymi dziennikami pokazano najpierw, uruchom następującą kwerendę:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Pokazywanie wpisów dzienników zawierających błędy lub wyjątki

Aby przejrzeć nieposortowane wpisy dziennika, które wspominają o błędzie lub wyjątku, uruchom następującą kwerendę:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ta kwerenda służy do znajdowania błędów lub modyfikowania terminów kwerendy w celu znalezienia określonych kodów błędów lub wyjątków. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Pokaż liczbę błędów i wyjątków zgłoszonych przez aplikację w ciągu ostatniej godziny

Aby utworzyć wykres kołowy, który wyświetla liczbę błędów i wyjątków zarejestrowanych przez aplikację w ciągu ostatniej godziny, uruchom następującą kwerendę:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Dowiedz się więcej o wyszukiwaniu dzienników aplikacji

Usługa Azure Monitor zapewnia szeroką obsługę wykonywania zapytań o dzienniki aplikacji przy użyciu usługi Log Analytics. Aby dowiedzieć się więcej o tej usłudze, zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor.](../azure-monitor/log-query/get-started-queries.md) Aby uzyskać więcej informacji na temat tworzenia kwerend do analizowania dzienników aplikacji, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor.](../azure-monitor/log-query/log-query-overview.md)
