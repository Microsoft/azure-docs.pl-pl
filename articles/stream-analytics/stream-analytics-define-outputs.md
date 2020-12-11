---
title: Dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano opcje danych wyjściowych dostępne dla Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: c11e0702a7825b719417288c5ebbf93defde186c
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029248"
---
# <a name="outputs-from-azure-stream-analytics"></a>Dane wyjściowe z Azure Stream Analytics

Zadanie Azure Stream Analytics składa się z danych wejściowych, zapytań i danych wyjściowych. Istnieje kilka typów danych wyjściowych, do których można wysłać przekształcone dane. W tym artykule wymieniono obsługiwane Stream Analytics dane wyjściowe. Podczas projektowania zapytania o Stream Analytics należy odwołać się do nazwy danych wyjściowych za pomocą [klauzuli into](/stream-analytics-query/into-azure-stream-analytics). Można użyć jednego danych wyjściowych na zadanie lub wielu wyjść na zadanie przesyłania strumieniowego (jeśli są potrzebne) przez dodanie wielu klauzul INTO do zapytania.

Aby tworzyć, edytować i testować Stream Analytics dane wyjściowe zadań, możesz użyć [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [interfejsu API platformy .NET](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), interfejsu API [rest](/rest/api/streamanalytics/)i [programu Visual Studio](stream-analytics-quick-create-vs.md).

Niektóre typy danych wyjściowych obsługują [partycjonowanie](#partitioning), a [wyjściowe rozmiary partii](#output-batch-size) różnią się w celu zoptymalizowania przepływności. W poniższej tabeli przedstawiono funkcje, które są obsługiwane dla każdego typu danych wyjściowych:

| Typ danych wyjściowych | Partycjonowanie | Zabezpieczenia | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Tak|Azure Active Directory użytkownika </br> , Tożsamość zarządzana|
|[Azure SQL Database](sql-database-output.md)|Tak, opcjonalnie.|Uwierzytelnianie użytkownika SQL, </br> Tożsamość zarządzana (wersja zapoznawcza)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Tak|Uwierzytelnianie użytkownika SQL, </br> Tożsamość zarządzana (wersja zapoznawcza)|
|[BLOB Storage i Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Tak|Klucz dostępu, </br> Tożsamość zarządzana (wersja zapoznawcza)|
|[Azure Event Hubs](event-hubs-output.md)|Tak, należy ustawić kolumnę klucza partycji w konfiguracji wyjściowej.|Klucz dostępu, </br> Tożsamość zarządzana (wersja zapoznawcza)|
|[Power BI](power-bi-output.md)|Nie|Azure Active Directory użytkownika, </br> Tożsamość zarządzana|
|[Azure Table storage](table-storage-output.md)|Tak|Klucz konta|
|[Kolejki Azure Service Bus](service-bus-queues-output.md)|Tak|Klucz dostępu|
|[Tematy Azure Service Bus](service-bus-topics-output.md)|Tak|Klucz dostępu|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Tak|Klucz dostępu|
|[Azure Functions](azure-functions-output.md)|Tak|Klucz dostępu|

## <a name="partitioning"></a>Partycjonowanie

Stream Analytics obsługuje partycje dla wszystkich danych wyjściowych z wyjątkiem Power BI. Aby uzyskać więcej informacji o kluczach partycji i liczbie modułów zapisywania danych wyjściowych, zobacz artykuł dotyczący określonego typu danych wyjściowych. Wszystkie artykuły wyjściowe są połączone w poprzedniej sekcji.  

Ponadto w celu zapewnienia bardziej zaawansowanego dostrajania partycji liczba modułów zapisywania danych wyjściowych może być kontrolowana za pomocą `INTO <partition count>` klauzuli (patrz [do](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) w zapytaniu, co może być przydatne w osiągnięciu odpowiedniej topologii zadań. Jeśli karta wyjściowa nie zostanie podzielona na partycje, Brak danych w jednej partycji wejściowej powoduje opóźnienie do późnego przybycia czasu. W takich przypadkach dane wyjściowe są scalane z pojedynczym składnikiem zapisywania, co może powodować wąskie gardła w potoku. Aby dowiedzieć się więcej na temat zasad późnego przybycia, zobacz temat [Azure Stream Analytics uwagi dotyczące kolejności zdarzeń](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Wszystkie dane wyjściowe obsługują przetwarzanie wsadowe, ale tylko niektóre z nich są obsługiwane. Azure Stream Analytics używa partii o zmiennym rozmiarze do przetwarzania zdarzeń i zapisywania do danych wyjściowych. Zwykle aparat Stream Analytics nie zapisuje jednej wiadomości w danym momencie i używa ich do zwiększenia wydajności. Gdy częstotliwość dla zdarzeń przychodzących i wychodzących jest wysoka, Stream Analytics używa większych partii. Gdy szybkość ruchu wychodzącego jest niska, używa mniejszych partii, aby zachować niski czas oczekiwania.

## <a name="parquet-output-batching-window-properties"></a>Właściwości okna wyprowadzania danych wyjściowych Parquet

W przypadku używania wdrożenia szablonu Azure Resource Manager lub interfejsu API REST dwa właściwości okna wsadowego są następujące:

1. *timeWindow*

   Maksymalny czas oczekiwania na partię. Wartość powinna być ciągiem przedziału czasu. Na przykład "00:02:00" przez dwie minuty. Po upływie tego czasu partia jest zapisywana w danych wyjściowych, nawet jeśli minimalne wymagania dotyczące wierszy nie są spełnione. Wartość domyślna to 1 minuta, a maksymalna dozwolona liczba to 2 godziny. Jeśli dane wyjściowe obiektu BLOB mają częstotliwość tworzenia wzorców ścieżki, czas oczekiwania nie może być wyższy niż zakres czasu partycji.

2. *sizeWindow*

   Liczba minimalnych wierszy na partię. Dla Parquet każda partia tworzy nowy plik. Bieżąca wartość domyślna to 2 000 wierszy, a maksymalna dozwolona liczba wierszy to 10 000.

Te właściwości okna wsadowego są obsługiwane tylko przez interfejs API w wersji **2017-04-01-Preview**. Poniżej znajduje się przykład ładunku JSON dla wywołania interfejsu API REST:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
