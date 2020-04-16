---
title: Działanie przepływu danych
description: Jak wykonać przepływy danych z wewnątrz potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 32088dd712cd0c70fc01de48add17a0b6a828dc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415327"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Działanie przepływu danych w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Przepływ danych umożliwia przekształcanie i przenoszenie danych za pomocą przepływów danych mapowania. Jeśli jesteś nowym użytkownikem przepływów danych, zobacz [Omówienie mapowania przepływu danych](concepts-data-flow-overview.md)

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

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
Dataflow | Odwołanie do wykonywanego przepływu danych | DataFlowReference (DataFlowReference) | Tak
integracjaRuntime | Środowisko obliczeniowe, na które działa przepływ danych. Jeśli nie zostanie określony, zostanie użyty środowisko uruchomieniowe integracji platformy Azure | IntegracjaRuntimeReference | Nie
compute.coreCount | Liczba rdzeni używanych w klastrze iskrowym. Można określić tylko wtedy, gdy używany jest automatyczny program runtime integracji platformy Azure | 8, 16, 32, 48, 80, 144, 272 | Nie
compute.computeType | Typ obliczeń używany w klastrze iskrowym. Można określić tylko wtedy, gdy używany jest automatyczny program runtime integracji platformy Azure | "Ogólne", "ComputeOptimized", "MemoryOptimized" | Nie
staging.linkedSługa | Jeśli używasz źródła lub ujścia SQL DW, konto magazynu używane do przemieszczania PolyBase | Wniosek w serwisie LinkedService | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje do SQL DW
staging.folderPath | Jeśli używasz źródła lub ujścia SQL DW, ścieżka folderu na koncie magazynu obiektów blob używanym do przemieszczania Bazy polybase | Ciąg | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje do SQL DW

![Wykonywanie przepływu danych](media/data-flow/activity-data-flow.png "Wykonywanie przepływu danych")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamicznie rozmiar przepływu danych obliczania w czasie wykonywania

Właściwości Liczba rdzeni i Typ obliczeń można ustawić dynamicznie, aby dostosować do rozmiaru przychodzących danych źródłowych w czasie wykonywania. Użyj działań potoku, takich jak wyszukiwanie lub Pobierz metadane, aby znaleźć rozmiar danych źródłowego zestawu danych. Następnie użyj dodaj zawartość dynamiczną we właściwościach działania Przepływ danych.

![Dynamiczny przepływ danych](media/data-flow/dyna1.png "Dynamiczny przepływ danych")

[Oto krótki film instruktażowy wyjaśniający tę technikę](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Środowisko wykonawcze integracji przepływu danych

Wybierz środowisko uruchomieniowe integracji do wykonania działania przepływu danych. Domyślnie usługa Data Factory będzie używać środowiska uruchomieniowego integracji platformy Azure z czterema rdzeniami procesu roboczego i bez czasu na przeżywanie (TTL). To środowisko IR ma typ obliczeń ogólnego przeznaczenia i działa w tym samym regionie co fabryka. Można utworzyć własne środowiska wykonawcze integracji platformy Azure, które definiują określone regiony, typ obliczeń, liczby rdzeni i czasu wygaśnięcia dla wykonywania działania przepływu danych.

W przypadku wykonywania potoku klaster jest klastrem zadań, który trwa kilka minut przed rozpoczęciem wykonywania. Jeśli nie określono czasu wygaśnięcia, ten czas uruchamiania jest wymagany przy każdym uruchomieniu potoku. Jeśli określisz czas wygaśnięcia, pula klastra ciepłego pozostanie aktywny przez czas określony po ostatnim wykonaniu, co spowoduje skrócenie czasu uruchamiania. Na przykład jeśli masz czas wygaśnięcia 60 minut i uruchomić przepływ danych na nim raz na godzinę, pula klastra pozostanie aktywny. Aby uzyskać więcej informacji, zobacz [środowisko wykonawcze integracji platformy Azure](concepts-integration-runtime.md).

![Środowisko wykonawcze integracji platformy Azure](media/data-flow/ir-new.png "Środowisko wykonawcze integracji platformy Azure")

> [!NOTE]
> Wybór integration runtime w działaniu przepływ danych ma zastosowanie tylko do *wyzwolonych wykonań* potoku. Debugowanie potoku z przepływami danych uruchamia się w klastrze określonym w sesji debugowania.

### <a name="polybase"></a>PolyBase

Jeśli używasz usługi Azure SQL Data Warehouse jako ujścia lub źródła, należy wybrać lokalizację przejściową dla obciążenia wsadowego PolyBase. PolyBase umożliwia ładowanie wsadowe zbiorczo zamiast ładowania danych wiersz po wierszu. PolyBase drastycznie skraca czas ładowania do SQL DW.

## <a name="parameterizing-data-flows"></a>Parametryzacja przepływów danych

### <a name="parameterized-datasets"></a>Sparametryzowane zestawy danych

Jeśli przepływ danych używa sparametryzowanych zestawów danych, ustaw wartości parametrów na karcie **Ustawienia.**

![Wykonywanie parametrów przepływu danych](media/data-flow/params.png "Parametry")

### <a name="parameterized-data-flows"></a>Parametryzowane przepływy danych

Jeśli przepływ danych jest sparametryzowany, ustaw wartości dynamiczne parametrów przepływu danych na karcie **Parametry.** Do przypisywania wartości parametrów dynamicznych lub dosłownych można użyć języka wyrażenia potoku ADF (tylko dla typów ciągów) lub języka wyrażenia przepływu danych. Aby uzyskać więcej informacji, zobacz [Parametry przepływu danych](parameters-data-flow.md).

![Przykład parametru przepływu danych](media/data-flow/parameter-example.png "Przykład parametru")

### <a name="parameterized-compute-properties"></a>Sparametryzowane właściwości obliczeniowe.

Można sparametryzować liczbę rdzeni lub typ obliczeń, jeśli używasz środowiska uruchomieniowego integracji platformy Azure i określasz wartości dla pliku compute.coreCount i pliku computeType.

![Przykład parametru przepływu danych](media/data-flow/parameterize-compute.png "Przykład parametru")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debugowanie potoku działania przepływu danych

Aby wykonać potok debugowania uruchamiany za pomocą działania przepływ danych, należy włączyć tryb debugowania przepływu danych za pomocą suwaka **debugowania przepływu danych** na górnym pasku. Tryb debugowania umożliwia uruchomienie przepływu danych względem aktywnego klastra platformy Spark. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

![Przycisk Debugowanie](media/data-flow/debugbutton.png "Przycisk Debugowanie")

Potok debugowania działa względem aktywnego klastra debugowania, a nie środowiska wykonawczego integracji określonego w ustawieniach działania przepływ danych. Podczas uruchamiania trybu debugowania można wybrać środowisko obliczeniowe debugowania.

## <a name="monitoring-the-data-flow-activity"></a>Monitorowanie aktywności przepływu danych

Działanie Przepływ danych ma specjalne środowisko monitorowania, w którym można wyświetlić partycjonowanie, czas etapu i informacje o linii danych. Otwórz okienko monitorowania za pomocą ikony okularów w obszarze **Akcje**. Aby uzyskać więcej informacji, zobacz [Monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Użyj działania Przepływ danych powoduje kolejne działanie

Działanie przepływu danych wyprowadza metryki dotyczące liczby wierszy zapisanych do każdego ujścia i wierszy odczytywanych z każdego źródła. Wyniki te są `output` zwracane w sekcji wyniku uruchomienia działania. Zwracane metryki są w formacie poniżej json.

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

Na przykład, aby uzyskać liczbę wierszy zapisanych do ujścia o nazwie "sink1" w `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`działaniu o nazwie "dataflowActivity", użyj .

Aby uzyskać liczbę wierszy odczytanych ze źródła o nazwie "source1", który był używany w tym zlewie, użyj programu `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Jeśli zlew ma zero wierszy napisane, nie pojawi się w metryki. Istnienie można zweryfikować `contains` za pomocą funkcji. Na przykład `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` sprawdzi, czy wszystkie wiersze zostały zapisane do zatonięcia1.

## <a name="next-steps"></a>Następne kroki

Zobacz działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
