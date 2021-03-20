---
title: Działanie przepływu danych
description: Jak wykonywać przepływy danych z wnętrza potoku usługi Fabryka danych.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 01/03/2021
ms.openlocfilehash: 0663690318773ccad3bddfaaa03e456c2f58895e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383385"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Aktywność przepływu danych w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj działania przepływu danych do przekształcania i przenoszenia danych za pośrednictwem mapowania przepływów danych. Jeśli dopiero zaczynasz przepływy danych, zobacz [Mapowanie przepływu danych — omówienie](concepts-data-flow-overview.md)

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
przepływu danych | Odwołanie do przepływu danych, który jest wykonywany | DataFlowReference | Tak
integrationRuntime | Środowisko obliczeniowe, w którym działa przepływ danych. Jeśli nie zostanie określony, zostanie użyta funkcja rozwiązywania problemów z rozwiązaniem Azure Integration Runtime. | IntegrationRuntimeReference | Nie
COMPUTE. coreCount | Liczba rdzeni używanych w klastrze Spark. Można określić tylko wtedy, gdy używane jest automatycznie rozwiązanie Azure Integration Runtime | 8, 16, 32, 48, 80, 144, 272 | Nie
COMPUTE. computetype | Typ obliczeń użytych w klastrze Spark. Można określić tylko wtedy, gdy używane jest automatycznie rozwiązanie Azure Integration Runtime | "Ogólne", "ComputeOptimized", "MemoryOptimized" | Nie
przemieszczanie. linkedService | Jeśli używasz źródła lub ujścia usługi Azure Synapse Analytics, określ konto magazynu używane na potrzeby wstępnego przemieszczania.<br/><br/>Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ korzystania z punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Należy również zapoznać się z wymaganymi konfiguracjami [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .<br/> | LinkedServiceReference | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje dane w usłudze Azure Synapse Analytics
przemieszczanie. folderPath | Jeśli używasz źródła lub ujścia usługi Azure Synapse Analytics, ścieżka folderu na koncie usługi BLOB Storage używanym do przemieszczania podstawowego | Ciąg | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje dane w usłudze Azure Synapse Analytics
traceLevel | Ustaw poziom rejestrowania wykonywania działania przepływu danych | Precyzyjna, Gruba, brak | Nie

![Wykonaj przepływ danych](media/data-flow/activity-data-flow.png "Wykonaj przepływ danych")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamiczne rozmiary obliczeń przepływu danych w czasie wykonywania

Właściwości liczba rdzeni i typ obliczeń można skonfigurować dynamicznie, aby dostosować rozmiar przychodzących danych źródłowych w czasie wykonywania. Użyj działań potoku, takich jak odnośnik lub Pobierz metadane, aby znaleźć rozmiar danych źródłowego zestawu danych. Następnie użyj Dodaj zawartość dynamiczną we właściwościach działania przepływu danych.

![Dynamiczny przepływ danych](media/data-flow/dyna1.png "Dynamiczny przepływ danych")

[Oto krótki samouczek wideo objaśniający tę technikę](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Środowisko Integration Runtime

Wybierz Integration Runtime, które mają być używane do wykonywania działań przepływu danych. Domyślnie Data Factory będzie używać automatycznie rozwiązywania środowiska Azure Integration Runtime z czterema rdzeniami procesów roboczych i bez czasu wygaśnięcia (TTL). Ten IR ma typ obliczeń ogólnego przeznaczenia i działa w tym samym regionie, w którym znajduje się fabryka. Możesz tworzyć własne środowiska Azure Integration Runtime, które definiują określone regiony, typ obliczeń, liczniki rdzeni i czas wygaśnięcia dla wykonywania działania przepływu danych.

W przypadku wykonań potoku klaster jest klastrem zadań, co potrwa kilka minut, zanim uruchomienie zostanie rozpoczęte. Jeśli nie określono czasu wygaśnięcia, ten czas uruchamiania jest wymagany dla każdego uruchomienia potoku. W przypadku określenia czasu wygaśnięcia (TTL) w czasie określonym po ostatnim wykonaniu zostanie uaktywniona bezczynna Pula klastra, co spowoduje skrócenie czasu uruchomienia. Jeśli na przykład czas wygaśnięcia wynosi 60 minut i uruchomisz na nim przepływ danych raz na godzinę, Pula klastrów pozostanie aktywna. Aby uzyskać więcej informacji, zobacz [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Integration Runtime wybór w działaniu przepływu danych dotyczy tylko *wyzwolonych wykonań* potoku. Debugowanie potoku za pomocą przepływów danych przebiega w klastrze określonym w sesji debugowania.

### <a name="polybase"></a>PolyBase

Jeśli korzystasz z usługi Azure Synapse Analytics jako ujścia lub źródło, musisz wybrać lokalizację przejściową dla obciążenia wsadowego z podstawową bazą danych. Baza danych umożliwia zbiorcze ładowanie wsadowe, zamiast ładować dane wiersz po wierszu. Podstawa podstawowa znacznie zmniejsza czas ładowania do usługi Azure Synapse Analytics.

## <a name="logging-level"></a>Poziom rejestrowania

Jeśli nie jest wymagane każde wykonanie potoku działań przepływu danych w celu pełnego rejestrowania wszystkich pełnych dzienników telemetrii, możesz opcjonalnie ustawić poziom rejestrowania na "podstawowa" lub "Brak". Podczas wykonywania przepływów danych w trybie "verbose" (ustawienie domyślne), żądanie ADF można w pełni rejestrować na poszczególnych poziomach partycji podczas przekształcania danych. Może to być kosztowna operacja, dzięki czemu można ją włączyć tylko wtedy, gdy Rozwiązywanie problemów może poprawić ogólny przepływ danych i wydajność potoku. Tryb "podstawowy" spowoduje rejestrowanie tylko czasów trwania transformacji, gdy wartość "Brak" spowoduje jedynie podsumowanie czasu trwania.

![Poziom rejestrowania](media/data-flow/logging.png "Ustaw poziom rejestrowania")

## <a name="sink-properties"></a>Właściwości ujścia

Funkcja grupowania w przepływach danych umożliwia zarówno Ustawianie kolejności wykonywania ujścia, jak i grupowanie obiektów ujścia przy użyciu tego samego numeru grupy. Aby ułatwić zarządzanie grupami, można zadawać ADF w celu uruchomienia ujścia, w tej samej grupie równolegle. Można również ustawić, aby grupa ujścia kontynuowała działanie nawet po napotkaniu błędu przez jeden z zlewów.

Domyślne zachowanie ujścia przepływu danych polega na wykonaniu każdego ujścia sekwencyjnie, w sposób szeregowy i do niepowodzenia przepływu danych w przypadku wystąpienia błędu w ujścia. Ponadto wszystkie ujścia są ustawiane domyślnie do tej samej grupy, o ile nie przejdziesz do właściwości przepływu danych i ustawisz różne priorytety dla ujścia.

![Właściwości ujścia](media/data-flow/sink-properties.png "Ustaw właściwości ujścia")

## <a name="parameterizing-data-flows"></a>Parametryzacja przepływy danych

### <a name="parameterized-datasets"></a>Sparametryzowane zestawy danych

Jeśli przepływ danych używa sparametryzowane zestawy danych, ustaw wartości parametrów na karcie **Ustawienia** .

![Parametry przepływu danych wykonywania](media/data-flow/params.png "Parametry")

### <a name="parameterized-data-flows"></a>Sparametryzowane przepływy danych

Jeśli przepływ danych jest sparametryzowane, ustaw wartości dynamiczne parametrów przepływu danych na karcie **Parametry** . Do przypisywania wartości parametrów dynamicznych lub literalnych można użyć języka wyrażeń potoku ADF lub języka wyrażeń przepływu danych. Aby uzyskać więcej informacji, zobacz [parametry przepływu danych](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Sparametryzowane właściwości obliczeniowe.

W przypadku korzystania z rozwiązania Azure Integration Runtime i określania wartości dla obliczeń COMPUTE. coreCount i COMPUTE. computetype można Sparametryzuj liczbę rdzeni lub typ obliczeń.

![Przykład parametru przepływu danych](media/data-flow/parameterize-compute.png "Przykład parametru")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debugowanie potoku działania przepływu danych

Aby wykonać uruchomienie potoku debugowania z działaniem przepływu danych, należy przełączyć tryb debugowania przepływu danych za pomocą suwaka **debugowania przepływu** danych na górnym pasku. Tryb debugowania umożliwia uruchomienie przepływu danych na aktywnym klastrze Spark. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

![Przycisk Debuguj](media/data-flow/debugbutton.png "Przycisk Debuguj")

Potok debugowania działa w odniesieniu do aktywnego klastra debugowania, a nie środowiska Integration Runtime określonego w ustawieniach działania przepływu danych. Podczas uruchamiania trybu debugowania można wybrać środowisko obliczeniowe debugowania.

## <a name="monitoring-the-data-flow-activity"></a>Monitorowanie działania przepływu danych

Działanie przepływu danych ma specjalne środowisko monitorowania, w którym można wyświetlać partycjonowanie, czas etapów i informacje o pomieszczeniu danych. Otwórz okienko monitorowanie za pomocą ikony okularów w obszarze **Akcje**. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Korzystanie z wyników działania przepływu danych w kolejnym działaniu

Działanie przepływu danych wyprowadza metryki dotyczące liczby wierszy zapisanych do każdego ujścia oraz wierszy odczytanych z każdego źródła. Wyniki są zwracane w `output` sekcji wyniku uruchomienia działania. Zwracane metryki są w formacie poniższego kodu JSON.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Aby na przykład uzyskać dostęp do liczby wierszy zapisanych w zlewie o nazwie "sink1" w działaniu o nazwie "Dataflow", użyj elementu `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Aby uzyskać informacje o liczbie wierszy odczytanych ze źródła o nazwie "source1", które zostały użyte w tym ujścia, użyj `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Jeśli ujścia nie ma zapisanych wierszy, nie będzie wyświetlana w metrykach. Istnienie można zweryfikować przy użyciu `contains` funkcji. Program sprawdzi na przykład, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` czy wszystkie wiersze zostały zapisaną w sink1.

## <a name="next-steps"></a>Następne kroki

Zobacz aktywność przepływu sterowania obsługiwaną przez Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
