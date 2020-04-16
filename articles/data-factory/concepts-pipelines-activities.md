---
title: Potoki i działania w usłudze Azure Data Factory
description: Informacje na temat potoków i działań w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ad4ffa71480a5af06c31872cbafcaab7719c55e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418341"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Potoki i działania w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-pipelines.md)
> * [Aktualna wersja](concepts-pipelines-activities.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz z konstruowaniem za ich pomocą pełnych przepływów pracy dla scenariuszy przenoszenia i przetwarzania danych.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Na przykład potok może zawierać zestaw działań, które pozyskiwania i czyszczenia danych dziennika, a następnie rozpocząć przepływ danych mapowania do analizy danych dziennika. Potok umożliwia zarządzanie działaniami jako zestaw, a nie każdy z nich indywidualnie. Można wdrożyć i zaplanować potoku zamiast działań niezależnie.

Działania w potoku definiują akcje do wykonania na danych. Możesz na przykład użyć działania kopiowania w celu skopiowania danych z lokalnego programu SQL Server do usługi Azure Blob Storage. Następnie użyj działania przepływu danych lub działania notesu Databricks do przetwarzania i przekształcania danych z magazynu obiektów blob do puli usługi Azure Synapse Analytics, na której są tworzone rozwiązania do raportowania analizy biznesowej.

Fabryka danych ma trzy grupy działań: działania związane z [przenoszeniem danych,](copy-activity-overview.md) [działania związane z przekształcaniem danych](transform-data.md)i działania [kontrolne.](control-flow-web-activity.md) Dane działanie może — ale nie musi — korzystać z wejściowych [zestawów danych](concepts-datasets-linked-services.md) i generować co najmniej jeden wyjściowy [zestaw danych](concepts-datasets-linked-services.md). Na poniższym diagramie przedstawiono relację między potokiem, działaniem i zestawem danych w usłudze Data Factory:

![Relacja między zestawem danych, działaniem i potokiem](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Wejściowy zestaw danych reprezentuje dane wejściowe dla działania w potoku, a wyjściowy zestaw danych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Po utworzeniu zestawu danych można go użyć z działaniami w potoku. Na przykład zestaw danych może być zestawem danych wejściowych/wyjściowych działania kopiowania lub działania HDInsightHive. Aby uzyskać więcej informacji na temat zestawów danych, zobacz artykuł [Datasets in Azure Data Factory](concepts-datasets-linked-services.md) (Zestawy danych w usłudze Azure Data Factory).

## <a name="data-movement-activities"></a>Działania dotyczące przenoszenia danych

Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje magazyny danych wymienione w tabeli w tej sekcji. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Aby uzyskać więcej informacji, zobacz artykuł [Działanie kopiowania — omówienie](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Działania dotyczące przekształcania danych
Usługa Azure Data Factory obsługuje następujące działania przekształcania, które można dodawać indywidualnie do potoków lub łączyć je z innymi działaniami.

Działanie przekształcania danych | Środowisko obliczeniowe
---------------------------- | -------------------
[Przepływ danych](control-flow-execute-data-flow-activity.md) | Usługa Azure Databricks zarządzana przez usługę Azure Data Factory
[Funkcja platformy Azure](control-flow-azure-function-activity.md) | Azure Functions
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce (Mapa)](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Połączenia strumieniowe usługi Hadoop](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[platforma Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](transform-data-using-machine-learning.md) | Maszyna wirtualna platformy Azure
[Procedura składowana](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse lub SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Działanie niestandardowe](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Notes usługi Databricks](transform-data-databricks-notebook.md) | Azure Databricks
[Działanie pliku Jar usługi Databricks](transform-data-databricks-jar.md) | Azure Databricks
[Działanie języka Python w usłudze Databricks](transform-data-databricks-python.md) | Azure Databricks

Aby uzyskać więcej informacji, zobacz artykuł z [działaniami dotyczącymi przekształcania danych](transform-data.md).

## <a name="control-flow-activities"></a>Sterowanie działaniami przepływu
Obsługiwane są następujące działania przepływu sterowania:

Działanie sterowania | Opis
---------------- | -----------
[Dołącz zmienną](control-flow-append-variable-activity.md) | Dodaj wartość do istniejącej zmiennej tablicowej.
[Wykonywanie potoku](control-flow-execute-pipeline-activity.md) | Działanie Execute Pipeline umożliwia potokowi usługi Data Factory wywoływanie innego potoku.
[Filtr](control-flow-filter-activity.md) | Stosowanie wyrażenia filtru do tablicy wejściowej
[Dla każdego](control-flow-for-each-activity.md) | Działanie ForEach definiuje powtarzający się przepływ sterowania w potoku. To działanie służy do wykonywania iteracji po kolekcji i wykonuje określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania.
[Pobierz metadane](control-flow-get-metadata-activity.md) | Działanie GetMetadata umożliwia pobieranie metadanych dowolnych danych z usługi Azure Data Factory.
[Działanie If Condition](control-flow-if-condition-activity.md) | Działanie If Condition umożliwia tworzenie gałęzi na podstawie warunków, które są obliczane na wartość true lub false. Działanie If Condition pełni taką samą rolę, co instrukcja if w językach programowania. Ocenia zestaw działań, gdy warunek jest `true` oceniany do i inny zestaw działań, gdy warunek ocenia`false.`
[Działanie wyszukiwania](control-flow-lookup-activity.md) | Działanie Lookup może być używane do odczytywania lub wyszukiwania rekordu/nazwy tabeli/wartości z dowolnego źródła zewnętrznego. Do tych danych wyjściowych mogą także odwoływać się kolejne działania.
[Ustaw zmienną](control-flow-set-variable-activity.md) | Ustaw wartość istniejącej zmiennej.
[Działanie Until](control-flow-until-activity.md) | Wprowadza pętlę Do-Until, przypominającą strukturę pętli Do-Until w językach programowania. Służy do wykonywania zestawu działań w pętli do momentu, gdy warunek skojarzony z działaniem zostanie obliczony na wartość true. W usłudze Data Factory można określić wartość limitu czasu działania Until.
[Działanie weryfikacji](control-flow-validation-activity.md) | Upewnij się, że potok kontynuuje wykonywanie tylko wtedy, gdy istnieje zestaw danych referencyjnych, spełnia określone kryteria lub osiągnięto limit czasu.
[Działanie Wait](control-flow-wait-activity.md) | Podczas korzystania z Wait działania w potoku, potok czeka na określony czas przed kontynuowaniem wykonywania kolejnych działań.
[Działanie internetowe](control-flow-web-activity.md) | Działanie WebActivity może być używane do wywoływania niestandardowego punktu końcowego REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie.
[Działanie elementu webhook](control-flow-webhook-activity.md) | Za pomocą działania elementu webhook, wywołać punkt końcowy i przekazać adres URL wywołania zwrotnego. Uruchomienie potoku czeka na wywołanie zwrotne, które ma zostać wywołane przed przejściem do następnego działania.

## <a name="pipeline-json"></a>Format JSON potoku
Poniżej przedstawiono sposób definiowania potoku w formacie JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

Tag | Opis | Typ | Wymagany
--- | ----------- | ---- | --------
name | Nazwa potoku. Określ nazwę, która reprezentuje akcję wykonywaną przez potok. <br/><ul><li>Maksymalna liczba znaków: 140</li><li>Musi zaczynać się od litery,\_liczby lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">","*"," %",," &,"":","\" </li></ul> | Ciąg | Tak
description | Wprowadź tekst opisujący przeznaczenie potoku. | Ciąg | Nie
activities | W sekcji **activities** można zdefiniować jedno lub więcej działań. Sprawdź sekcję [Format JSON działania](#activity-json), aby uzyskać szczegółowe informacje na temat elementu JSON activities. | Tablica | Tak
parameters | Sekcja **parameters** może zawierać jeden lub kilka parametrów zdefiniowanych w potoku, co zwiększa elastyczność i możliwość ponownego zastosowania potoku. | List | Nie
współbieżność | Maksymalna liczba równoczesnych uruchomień potoku może mieć. Domyślnie nie ma maksimum. Jeśli zostanie osiągnięty limit współbieżności, dodatkowe przebiegi potoku są umieszczane w kolejce do momentu zakończenia wcześniejszych | Liczba | Nie 
Adnotacje | Lista znaczników skojarzonych z rurociągiem | Tablica | Nie

## <a name="activity-json"></a>Format JSON działania
W sekcji **activities** można zdefiniować jedno lub więcej działań. Istnieją dwa główne typy działań: działania wykonywania i sterowania.

### <a name="execution-activities"></a>Działania wykonywania
Działania wykonywania obejmują [działania przenoszenia danych](#data-movement-activities) i [działania przekształcania danych](#data-transformation-activities). Ich struktura najwyższego poziomu wygląda następująco:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

Poniższa tabela zawiera opis właściwości w definicji JSON działania:

Tag | Opis | Wymagany
--- | ----------- | ---------
name | Nazwa działania. Określ nazwę, która reprezentuje akcję wykonywaną przez działanie. <br/><ul><li>Maksymalna liczba znaków: 55</li><li>Musi zaczynać się od cyfry\_literowej lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">","*"," %",," &,"":","\" | Tak</li></ul>
description | Tekst opisujący przeznaczenie działania | Tak
type | Typ działania. Zobacz [działania przenoszenia danych,](#data-movement-activities) [działania przekształcania danych](#data-transformation-activities)i [działania kontrolne](#control-flow-activities) dla różnych typów działań. | Tak
linkedServiceName | Nazwa połączonej usługi używana na potrzeby działania.<br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. | Tak dla działań HDInsight, oceny partii Azure Machine Learning i procedury składowanej. <br/><br/>Nie dla wszystkich innych
typeProperties | Właściwości w sekcji typeProperties zależą od typu działania. Aby wyświetlić właściwości typu dla działania, kliknij linki do działań w poprzedniej sekcji. | Nie
policy | Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Ta właściwość zawiera limit czasu i ponowić próbę zachowania. Jeśli nie jest określony, używane są wartości domyślne. Więcej informacji można znaleźć w sekcji [Zasady działania](#activity-policy). | Nie
dependsOn | Ta właściwość jest używana do definiowania zależności działania oraz sposobu, w jaki kolejne działania zależą od poprzednich działań. Więcej informacji można znaleźć w sekcji [Zależności działania](#activity-dependency) | Nie

### <a name="activity-policy"></a>Zasady działania
Zasady wpływają na zachowanie działania w czasie wykonania, określając opcje konfiguracji. Zasady działania są dostępne tylko dla działań wykonywania.

### <a name="activity-policy-json-definition"></a>Definicja JSON zasad działania

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

Nazwa JSON | Opis | Dozwolone wartości | Wymagany
--------- | ----------- | -------------- | --------
timeout | Określa limit czasu pracy działania. | Zakres czasu | Nie. Domyślny limit czasu wynosi 7 dni.
retry | Maksymalna liczba ponownych prób | Liczba całkowita | Nie. Wartość domyślna to 0
retryIntervalInSeconds | Opóźnienie między ponownymi próbami w sekundach | Liczba całkowita | Nie. Wartość domyślna to 30 sekund
secureOutput | Po ustawieniu na true, dane wyjściowe z działania jest uważany za bezpieczne i nie są rejestrowane do monitorowania. | Wartość logiczna | Nie. Wartość domyślna to false.

### <a name="control-activity"></a>Działanie sterowania
Działania sterowania mają następującą strukturę najwyższego poziomu:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tag | Opis | Wymagany
--- | ----------- | --------
name | Nazwa działania. Określ nazwę, która reprezentuje akcję wykonywaną przez działanie.<br/><ul><li>Maksymalna liczba znaków: 55</li><li>Musi zaczynać się od cyfry\_litery lub podkreślenia ( )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<",">","*"," %",," &,"":","\" | Tak</li><ul>
description | Tekst opisujący przeznaczenie działania | Tak
type | Typ działania. Poszczególne typy działań opisano w sekcjach [Działania przenoszenia danych](#data-movement-activities), [Działania przekształcania danych](#data-transformation-activities) i [Działania sterowania](#control-flow-activities). | Tak
typeProperties | Właściwości w sekcji typeProperties zależą od typu działania. Aby wyświetlić właściwości typu dla działania, kliknij linki do działań w poprzedniej sekcji. | Nie
dependsOn | Ta właściwość jest używana do definiowania zależności działania oraz sposobu, w jaki kolejne działania zależą od poprzednich działań. Aby uzyskać więcej informacji, zobacz [zależność działania](#activity-dependency). | Nie

### <a name="activity-dependency"></a>Zależność działania
Zależność działania definiuje, jak kolejne działania zależą od poprzednich działań, określając warunek, czy kontynuować wykonywanie następnego zadania. Działanie może zależeć od jednego lub wielu poprzednich działań, z różnymi warunkami zależności.

Różne warunki zależności to: Succeeded (powodzenie), Failed (niepowodzenie), Skipped (pominięto) i Completed (ukończono).

Na przykład jeśli potok zawiera zależność Działanie A -> Działanie B, różne możliwe scenariusze to:

- Działanie B zawiera warunek zależności dla Działanie A z parametrem **succeeded**: Działanie B jest uruchamiane tylko wtedy, gdy ostateczny stan Działania A to succeeded
- Działanie B zawiera warunek zależności dla Działanie A z parametrem **failed**: Działanie B jest uruchamiane tylko wtedy, gdy ostateczny stan Działania A to failed
- Działanie B zawiera warunek zależności dla Działanie A z parametrem **completed**: Działanie B jest uruchamiane tylko wtedy, gdy ostateczny stan Działania A to succeeded lub failed
- Działanie B ma warunek zależności w działaniu A z **pominiętym:** Działanie B jest uruchamiane, jeśli działanie A ma ostateczny stan pominięty. Stan skipped występuje w scenariuszu Działanie X -> Działanie Y -> Działanie Z, w którym każde działanie jest uruchamiane tylko w przypadku pomyślnego zakończenia poprzedniego działania. Jeśli działanie X nie powiedzie się, a następnie działanie Y ma stan "Pominięte", ponieważ nigdy nie jest wykonywana. Podobnie działanie Z ma również stan "Pominięty".

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Przykład: Działanie 2 zależy od pomyślnego zakończenia Działania 1

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

`"

## Sample copy pipeline
In the following sample pipeline, there is one activity of type **Copy** in the **activities** section. In this sample, the [copy activity](copy-activity-overview.md) copies data from an Azure Blob storage to an Azure SQL database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Pamiętaj o następujących kwestiach:

- W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**.
- Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. Definiowanie zestawów danych w formacie JSON opisano w artykule [Zestawy danych](concepts-datasets-linked-services.md).
- W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. W sekcji [działania ruchu danych](#data-movement-activities) kliknij magazyn danych, którego chcesz użyć jako źródła lub ujścia, aby uzyskać dodatkowe informacje na temat przenoszenia danych z/do tego magazynu danych.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [Szybki start: tworzenie fabryki danych](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Przykładowy potok przekształcania
W poniższym przykładowym potoku występuje jedno działanie typu **HDInsightHive** w sekcji **activities**. W tym przykładzie [działanie HDInsight Hive](transform-data-using-hadoop-hive.md) przekształca dane z usługi Azure Blob Storage przez uruchomienie pliku skryptu Hive na klastrze usługi Azure HDInsight Hadoop.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Pamiętaj o następujących kwestiach:

- W sekcji działań jest tylko jedno działanie, którego parametr **type** został ustawiony na wartość **HDInsightHive**.
- Plik skryptu Hive, **partitionweblogs.hql**, jest przechowywany na koncie usługi Azure Storage (określonym przez scriptLinkedService, o `adfgetstarted`nazwie AzureStorageLinkedService) i w folderze skryptu w kontenerze .
- Sekcja `defines` służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu Hive jako wartości konfiguracyjne magazynu Hive (np. $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Sekcja **typeProperties** jest inna dla każdego działania przekształcania. Aby uzyskać informacje na temat właściwości typu obsługiwanych dla działania przekształcania, kliknij działanie przekształcania w sekcji [Działania przekształcania danych](#data-transformation-activities).

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [Tutorial: transform data using Spark](tutorial-transform-data-spark-powershell.md) (Szybki start: przekształcanie danych przy użyciu usługi Spark).

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Poprzednie dwa przykładowe potoki zawierają tylko po jednym działaniu. Potok może obejmować więcej niż jedno działanie. Jeśli potok zawiera wiele działań, a kolejne działania nie są zależne od poprzednich, działania mogą być wykonywane równolegle.

Można połączyć dwa działania przy użyciu [zależności działania](#activity-dependency), która definiuje, w jaki sposób kolejne działania zależą od poprzednich działań, określając warunek kontynuowania wykonywania kolejnego działania. Działanie może zależeć od jednego lub kilku poprzednich działań, z różnymi warunkami zależności.

## <a name="scheduling-pipelines"></a>Planowanie potoków
Planowanie potoków odbywa się przy użyciu wyzwalaczy. Istnieją różne typy wyzwalaczy (wyzwalacz harmonogramu, który umożliwia wyzwalanie potoków zgodnie z harmonogramem zegara ściennego, a także ręczny wyzwalacz, który wyzwala potoki na żądanie). Więcej informacji na temat wyzwalaczy zawiera artykuł na temat [wykonywania potoku i wyzwalaczy](concepts-pipeline-execution-triggers.md).

Aby wyzwalacz uruchamiał potok, należy dołączyć odwołanie do konkretnego potoku do definicji wyzwalacza. Między potokami i wyzwalaczami występuje relacja typu „wiele do wielu”. Wiele wyzwalaczy można uruchomić jeden potok, a ten sam wyzwalacz może rozpocząć wiele potoków. Po zdefiniowaniu wyzwalacza należy go uruchomić, aby rozpoczął wyzwalanie potoków. Więcej informacji na temat wyzwalaczy zawiera artykuł na temat [wykonywania potoku i wyzwalaczy](concepts-pipeline-execution-triggers.md).

Na przykład załóżmy, że masz wyzwalacz harmonogramu "Wyzwalacz A", który chcę uruchomić mój potok "MyCopyPipeline". Wyzwalacz można zdefiniować, jak pokazano w poniższym przykładzie:

### <a name="trigger-a-definition"></a>Definicja wyzwalacza Trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki
Następujące samouczki zawierają instrukcje krok po kroku tworzenia potoków z działaniami:

- [Tworzenie potoku z działaniem kopiowania](quickstart-create-data-factory-powershell.md)
- [Tworzenie potoku z działaniem przekształcania danych](tutorial-transform-data-spark-powershell.md)
