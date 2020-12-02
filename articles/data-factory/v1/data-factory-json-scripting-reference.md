---
title: Dokumentacja skryptów Azure Data Factory-JSON
description: Zawiera schematy JSON dla jednostek Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3b4795a47f0e6dbf945bd4a1f9aaaa0df2137f91
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495586"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Dokumentacja skryptów Azure Data Factory-JSON
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory.


Ten artykuł zawiera schematy i przykłady JSON do definiowania jednostek Azure Data Factory (potok, działanie, zestaw danych i połączona usługa).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Potok
Struktura wysokiego poziomu dla definicji potoku jest następująca:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

W poniższej tabeli opisano właściwości w definicji JSON potoku:

| Właściwość | Opis | Wymagane
-------- | ----------- | --------
| name | Nazwa potoku. Określ nazwę reprezentującą akcję, która ma zostać skonfigurowana dla działania lub potoku<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się cyfrą lub znakiem podkreślenia ( \_ )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Tak |
| description (opis) |Tekst opisujący działanie lub potoki używane przez | Nie |
| activities | Zawiera listę działań. | Tak |
| start |Data i godzina rozpoczęcia potoku. Musi być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41. <br/><br/>Można określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00**-05:00` , czyli 6.<br/><br/>Właściwości Start i End wspólnie określają aktywny okres dla potoku. Wycinki wyjściowe są tworzone tylko w tym aktywnym okresie. |Nie<br/><br/>W przypadku określenia wartości właściwości End należy określić wartość właściwości Start.<br/><br/>Aby można było utworzyć potok, oba czasy rozpoczęcia i zakończenia mogą być puste. Należy określić obie wartości, aby ustawić aktywny okres uruchomienia potoku. Jeśli nie określisz godzin początkowych i końcowych podczas tworzenia potoku, możesz je ustawić przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod w późniejszym czasie. |
| end |Data i godzina zakończenia potoku. Jeśli ta wartość jest określona, musi być w formacie ISO. Na przykład: 2014-10-14T17:32:41 <br/><br/>Można określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00**-05:00` , czyli 6.<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. |Nie <br/><br/>W przypadku określenia wartości właściwości Start należy określić wartość właściwości end.<br/><br/>Zobacz uwagi dotyczące właściwości **Start** . |
| isPaused |Jeśli wartość jest równa true, potok nie jest uruchamiany. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania. |Nie |
| potokmode |Metoda planowania przebiegów dla potoku. Dozwolone wartości to: zaplanowane (wartość domyślna), jednorazowej.<br/><br/>Wartość "zaplanowany" wskazuje, że potok jest uruchamiany w określonym przedziale czasu, zgodnie z jego aktywnym okresem (godzina rozpoczęcia i zakończenia). Element "jednorazowej" wskazuje, że potok jest uruchamiany tylko raz. Nie można obecnie modyfikować/aktualizować potoków jednorazowej utworzonych po utworzeniu. Zobacz [potok jednorazowej](data-factory-create-pipelines.md#onetime-pipeline) , aby uzyskać szczegółowe informacje na temat ustawienia jednorazowej. |Nie |
| expirationTime |Czas, po którym proces tworzenia potoku jest prawidłowy i powinien pozostać zainicjowany. Jeśli nie ma żadnych aktywnych, zakończonych niepowodzeniem lub oczekujących uruchomień, potok jest automatycznie usuwany po osiągnięciu czasu wygaśnięcia. |Nie |


## <a name="activity"></a>Działanie
Struktura wysokiego poziomu dla działania w ramach definicji potoku (element activitys) jest następująca:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

Poniższa tabela zawiera opis właściwości w ramach definicji JSON działania:

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania. Określ nazwę reprezentującą akcję, która ma zostać skonfigurowana dla działania<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się cyfrą lub znakiem podkreślenia ( \_ )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Tak |
| description (opis) |Tekst opisujący, do czego służy działanie. |Nie |
| typ |Określa typ działania. Zapoznaj się z sekcjami [magazyny danych](#data-stores) i [działania przekształcania danych](#data-transformation-activities) dla różnych typów działań. |Tak |
| danych wejściowych |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nie dla działań HDInsightStreaming i SqlServerStoredProcedure <br/> <br/> Tak dla wszystkich innych |
| wydajności |Tabele wyjściowe używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Tak |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak dla działań usługi HDInsight, działania Azure Machine Learning Studio (klasyczne) i działania procedury składowanej. <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji typeProperties zależą od typu działania. |Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określona, używane są zasady domyślne. |Nie |
| scheduler |Właściwość "Scheduler" służy do definiowania żądanego planowania dla działania. Jego właściwości własne są takie same jak te we [właściwości Availability w zestawie danych](data-factory-create-datasets.md#dataset-availability). |Nie |

### <a name="policies"></a>Zasady
Zasady mają wpływ na zachowanie działania w czasie wykonywania, w odróżnieniu od tego, czy wycinek tabeli jest przetwarzany. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| współbieżność |Liczba całkowita <br/><br/>Wartość maksymalna: 10 |1 |Liczba współbieżnych wykonań działania.<br/><br/>Określa liczbę równoległych wykonań działań, które mogą być wykonywane na różnych wycinkach. Na przykład, jeśli działanie wymaga przechodzenia przez duży zestaw dostępnych danych, dzięki czemu większa wartość współbieżności przyspiesza przetwarzanie danych. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Określa kolejność wycinkiów danych, które są przetwarzane.<br/><br/>Na przykład, jeśli masz 2 wycinków (jedno zdarza się o 16:00, a drugi w 17:00), i oba są oczekujące na wykonanie. Jeśli ustawisz executionPriorityOrder jako NewestFirst, najpierw zostanie przetworzony wycink o wartości 5 PM. Podobnie, jeśli ustawisz executionPriorityORder jako OldestFIrst, zostanie przetworzony wycinek o godzinie 4 PM. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może być równa 10 |0 |Liczba ponownych prób przed przetworzeniem danych dla wycinka jest oznaczona jako niepowodzenie. Wykonanie działania dla wycinka danych zostanie ponowione z określoną liczbą ponownych prób. Ponowienie próby jest wykonywane najszybciej, jak to możliwe po awarii. |
| timeout |przedział_czasu |00:00:00 |Limit czasu dla działania. Przykład: 00:10:00 (implikuje limit czasu 10 min)<br/><br/>Jeśli wartość nie jest określona lub jest równa 0, limit czasu jest nieskończony.<br/><br/>Jeśli czas przetwarzania danych na wycinku przekracza wartość limitu czasu, zostanie anulowany, a system próbuje ponowić próbę przetwarzania. Liczba ponownych prób zależy od właściwości retry. Gdy wystąpi limit czasu, stan jest ustawiany na TimedOut. |
| opóźnienie |przedział_czasu |00:00:00 |Określ opóźnienie przed rozpoczęciem przetwarzania danych wycinka.<br/><br/>Wykonywanie działań dla wycinka danych jest uruchamiane po upływie oczekiwanego czasu wykonania.<br/><br/>Przykład: 00:10:00 (oznacza opóźnienie 10 min) |
| longRetry |Liczba całkowita<br/><br/>Wartość maksymalna: 10 |1 |Liczba długotrwałych ponownych prób przed wykonaniem wycinka nie powiodła się.<br/><br/>longRetry próbuje longRetryInterval. Dlatego jeśli musisz określić godzinę między ponownymi próbami, użyj longRetry. Jeśli określono zarówno ponowną próbę, jak i longRetry, każda próba longRetry obejmuje ponowną próbę i maksymalną liczbę prób ponawiania prób * longRetry.<br/><br/>Na przykład jeśli w zasadach działania istnieją następujące ustawienia:<br/>Ponów próbę: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Załóżmy, że istnieje tylko jeden wycinek do wykonania (stan oczekuje), a wykonywanie działania kończy się za każdym razem. Początkowo będą podejmowane trzy kolejne próby wykonania. Po każdej próbie będzie można ponowić próbę. Po wykonaniu pierwszych 3 prób zostanie wyświetlony stan wycinka LongRetry.<br/><br/>Po godzinie (czyli longRetryInteval wartość) może istnieć kolejny zestaw trzech kolejnych prób wykonania. Po tym stanie wycinka zostanie zakończona niepowodzeniem i nie będzie podejmowanych dalszych prób. W związku z tym wykonano ogólne 6 prób.<br/><br/>Jeśli jakiekolwiek wykonanie zakończy się pomyślnie, stan wycinka będzie gotowy i nie będą podejmowane żadne dalsze próby.<br/><br/>longRetry mogą być używane w sytuacjach, gdy dane zależne docierają do niedeterministycznych czasów lub w ogólnym środowisku, w którym odbywa się przetwarzanie danych. W takich przypadkach ponawianie próby jedna po drugiej może nie pomóc i wykonać to po upływie przedziału czasu w żądanych danych wyjściowych.<br/><br/>Uwaga: nie ustawiaj wysokich wartości dla longRetry lub longRetryInterval. Zazwyczaj wyższe wartości oznaczają inne problemy systemowe. |
| longRetryInterval |przedział_czasu |00:00:00 |Opóźnienie między długimi próbami ponowienia |

### <a name="typeproperties-section"></a>Sekcja typeProperties
Sekcja typeProperties jest inna dla każdego działania. Działania transformacji mają tylko właściwości typu. Zobacz sekcję [działania przekształcania danych](#data-transformation-activities) w tym artykule dotyczące przykładów JSON, które definiują działania transformacji w potoku.

**Działanie kopiowania** ma dwie podsekcje w sekcji typeProperties: **Source** i **ujścia**. W sekcji [magazyny danych](#data-stores) w tym artykule znajdują się informacje dotyczące przykładów JSON, które pokazują, jak używać magazynu danych jako źródła i/lub ujścia.

### <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
W poniższym przykładowym potoku występuje jedno działanie typu **Copy** w sekcji **activities**. W tym przykładzie [działanie Copy](data-factory-data-movement-activities.md) kopiuje dane z usługi Azure Blob storage do Azure SQL Database.

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
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**.
* Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**.
* W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia.

W sekcji [magazyny danych](#data-stores) w tym artykule znajdują się informacje dotyczące przykładów JSON, które pokazują, jak używać magazynu danych jako źródła i/lub ujścia.

Aby uzyskać kompletny przewodnik tworzenia tego potoku, zobacz [Samouczek: kopiowanie danych z BLOB Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Przykładowy potok przekształcania
W poniższym przykładowym potoku występuje jedno działanie typu **HDInsightHive** w sekcji **activities**. W tym przykładzie [działanie HDInsight Hive](data-factory-hive-activity.md) przekształca dane z usługi Azure Blob Storage przez uruchomienie pliku skryptu Hive na klastrze usługi Azure HDInsight Hadoop.

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
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** został ustawiony na wartość **HDInsightHive**.
* Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie usługi Azure Storage (określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService**) oraz w folderze **script** w kontenerze **adfgetstarted**.
* Sekcja **defines** służy do określania ustawień środowiska uruchomieniowego, które są przesyłane do skryptu programu Hive jako wartości konfiguracyjne programu Hive (np `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` .).

Zobacz sekcję [działania przekształcania danych](#data-transformation-activities) w tym artykule dotyczące przykładów JSON, które definiują działania transformacji w potoku.

Aby uzyskać kompletny przewodnik tworzenia tego potoku, zobacz [Samouczek: Tworzenie pierwszego potoku w celu przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Połączona usługa
Struktura wysokiego poziomu dla definicji połączonej usługi jest następująca:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Poniższa tabela zawiera opis właściwości w ramach definicji JSON działania:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| name | Nazwa połączonej usługi. | Tak |
| właściwości — Typ | Typ połączonej usługi. Na przykład: Azure Storage, Azure SQL Database. |
| typeProperties | Sekcja typeProperties zawiera elementy, które różnią się w zależności od magazynu danych lub środowiska obliczeniowego. Zobacz sekcję magazyny danych dla wszystkich połączonych usług magazynu danych i [środowisk obliczeniowych](#compute-environments) dla wszystkich połączonych usług obliczeniowych |

## <a name="dataset"></a>Zestaw danych
Zestaw danych w Azure Data Factory jest zdefiniowany w następujący sposób:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| name | Nazwa zestawu danych. Zobacz [reguły](data-factory-naming-rules.md) nazewnictwa Azure Data Factory zasad nazewnictwa. |Tak |Nie dotyczy |
| typ | Typ zestawu danych. Określ jeden z typów obsługiwanych przez Azure Data Factory (na przykład: AzureBlob, wartość azuresqltable). Zobacz sekcję [magazyny danych](#data-stores) dla wszystkich magazynów danych i typów zestawów danych obsługiwanych przez Data Factory. |
| — struktura | Schemat zestawu danych. Zawiera kolumny, ich typy itd. | Nie |Nie dotyczy |
| typeProperties | Właściwości odpowiadające wybranemu typowi. Zobacz sekcję [magazyny danych](#data-stores) dla obsługiwanych typów i ich właściwości. |Tak |Nie dotyczy |
| external | Flaga logiczna określająca, czy zestaw danych jest jawnie tworzony przez potok fabryki danych, czy nie. |Nie |fałsz |
| availability | Definiuje przedział czasu przetwarzania lub model odcięć dla środowiska produkcyjnego zestawu danych. Aby uzyskać szczegółowe informacje na temat modelu tworzenia wycinków danych, zobacz artykuł dotyczący [planowania i wykonywania](data-factory-scheduling-and-execution.md) . |Tak |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który musi spełniać wycinki zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz sekcję zasady zestawu danych. |Nie |Nie dotyczy |

Każda kolumna w sekcji **Structure** zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| typ |Typ danych kolumny.  |Nie |
| kultura |Kultura oparta na platformie .NET, która ma zostać użyta, gdy typ jest określony i jest typem .NET `Datetime` lub `Datetimeoffset` . Wartość domyślna to `en-us`. |Nie |
| format |Ciąg formatu, który ma być używany, gdy typ jest określony i jest typem .NET `Datetime` lub `Datetimeoffset` . |Nie |

W poniższym przykładzie zestaw danych ma trzy kolumny `slicetimestamp` , `projectname` i `pageviews` i są typu: String, String i Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

W poniższej tabeli opisano właściwości, których można użyć w sekcji **dostępność** :

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla produkcji wycinków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuta, godzina, dzień, tydzień, miesiąc |Tak |Nie dotyczy |
| interval |Określa mnożnik dla częstotliwości<br/><br/>"Interwał x częstotliwości" określa, jak często wycinek jest generowany.<br/><br/>Jeśli potrzebujesz zestawu danych, który ma być pofragmentowany co godzinę, ustawisz <b>częstotliwość</b> na <b>godzinę</b>, a <b>Interwał</b> na <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli określisz częstotliwość jako minutę, zalecamy ustawienie interwału na wartość nie mniejszą niż 15. |Tak |Nie dotyczy |
| styl |Określa, czy wycinek ma być tworzony na początku, czy na końcu interwału.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli częstotliwość jest ustawiona na wartość miesiąc, a w polu styl ustawiono wartość EndOfInterval, wycinek zostanie utworzony w ostatnim dniu miesiąca. Jeśli styl jest ustawiony na StartOfInterval, wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli częstotliwość jest ustawiona na dzień, a styl ustawiono na EndOfInterval, wycinek jest tworzony w ciągu ostatniej godziny dnia.<br/><br/>Jeśli częstotliwość jest ustawiona na wartość Godzina i styl ustawiono na EndOfInterval, wycinek zostanie utworzony na końcu godziny. Na przykład dla wycinka dla 1 PM — 2 PM, wycinek jest generowany na 2 PM. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanym przez harmonogram do obliczania granic wycinków zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowe niż częstotliwość, są ignorowane części bardziej szczegółowe. <br/><br/>Jeśli na przykład <b>Interwał</b> ma wartość <b>co godzinę</b> (częstotliwość: godzina i interwał: 1), a <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b> , części <b>minut i sekund</b> AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Przedział czasu, przez który początek i koniec wszystkich wycinków zestawu danych są przesunięte. <br/><br/><b>Uwaga</b>: Jeśli określono zarówno anchorDateTime, jak i przesunięcie, wynik jest połączonym przesunięciem. |Nie |Nie dotyczy |

W poniższej sekcji dostępności określono, że wyjściowy zestaw danych jest generowany co godzinę (lub) zestaw danych wejściowych jest dostępny co godzinę:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Sekcja **zasady** w definicji zestawu danych definiuje kryteria lub warunek, który musi spełniać wycinki zestawu danych.

| Policy Name (Nazwa zasad) | Opis | Zastosowane do | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **obiekcie blob platformy Azure** spełniają minimalne wymagania dotyczące rozmiaru (w megabajtach). |Obiekt bob Azure |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **Azure SQL Database** lub **tabeli platformy Azure** zawierają minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

**Przykład:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Jeśli zestaw danych nie jest tworzony przez Azure Data Factory, powinien być oznaczony jako **zewnętrzny**. To ustawienie ma zwykle zastosowanie do danych wejściowych pierwszego działania w potoku, chyba że jest używane tworzenie łańcucha aktywności lub potoku.

| Nazwa | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| datadelay |Czas oczekiwania na sprawdzenie dostępności danych zewnętrznych dla danego wycinka. Na przykład, jeśli dane są dostępne co godzinę, sprawdzenie, czy dane zewnętrzne są dostępne, a odpowiedni wycink jest gotowy, można opóźnić przy użyciu datadelay.<br/><br/>Dotyczy tylko obecnego czasu.  Na przykład jeśli jest to 1:00 PM teraz, a ta wartość wynosi 10 minut, sprawdzanie poprawności rozpocznie się o 1:10 PM.<br/><br/>To ustawienie nie ma wpływu na wycinki w przeszłości (wycinki z czasem zakończenia wycinka + datadelay < teraz) są przetwarzane bez opóźnień.<br/><br/>Czas dłuższy niż 23:59 godzin musi być określony przy użyciu `day.hours:minutes:seconds` formatu. Na przykład, aby określić 24 godziny, nie należy używać 24:00:00; Zamiast tego należy użyć 1,00:00:00. Jeśli używasz 24:00:00, jest on traktowany jako 24 dni (24.00:00:00). Przez 1 dzień i 4 godziny należy określić 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarią a kolejną próbą ponowienia. Jeśli próba nie powiedzie się, następna próba będzie po retryInterval. <br/><br/>Jeśli teraz jest 1:00 PM, rozpoczynamy pierwszą próbę. Jeśli czas trwania pierwszego sprawdzania poprawności wynosi 1 minuta, a operacja nie powiodła się, kolejna ponowna próba jest równa 1:00 + 1 min (czas trwania) + 1 min (interwał ponawiania) = 1:02 PM. <br/><br/>W przypadku wycinków w przeszłości nie ma opóźnień. Ponowna próba nastąpi natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdej próbnej próby.<br/><br/>Jeśli ta właściwość ma wartość 10 minut, walidacja musi zostać zakończona w ciągu 10 minut. Jeśli sprawdzanie poprawności będzie możliwe dopiero po upływie 10 minut, ponów próbę.<br/><br/>Jeśli wszystkie próby sprawdzania poprawności przekraczają limit czasu, wycinek zostanie oznaczony jako TimedOut. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba prób sprawdzenia dostępności danych zewnętrznych. Dozwolona maksymalna wartość to 10. |Nie |3 |


## <a name="data-stores"></a>MAGAZYNY DANYCH
Sekcja [połączona usługa](#linked-service) udostępnia opisy elementów JSON, które są wspólne dla wszystkich typów połączonych usług. Ta sekcja zawiera szczegółowe informacje na temat elementów JSON, które są specyficzne dla poszczególnych magazynów danych.

Sekcja [zestawu danych](#dataset) dostarczyła opisy elementów JSON, które są wspólne dla wszystkich typów zestawów danych. Ta sekcja zawiera szczegółowe informacje na temat elementów JSON, które są specyficzne dla poszczególnych magazynów danych.

Sekcja [Activity](#activity) zawiera opisy elementów JSON, które są wspólne dla wszystkich typów działań. Ta sekcja zawiera szczegółowe informacje dotyczące elementów JSON, które są specyficzne dla każdego magazynu danych, gdy jest używany jako źródło/ujścia w działaniu kopiowania.

Kliknij link do szukanego sklepu, aby wyświetlić schematy JSON dla połączonej usługi, zestawu danych i źródła/ujścia dla działania kopiowania.

| Kategoria | Magazyn danych
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Bazy danych** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Plik** |[Amazon S3](#amazon-s3) |
| &nbsp; |[System plików](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Inne** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Tabela internetowa |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: połączona usługa Azure Storage i połączona usługa Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto usługi Azure Storage z fabryką danych przy użyciu **klucza konta**, Utwórz połączoną usługę Azure Storage. Aby zdefiniować połączoną usługę Azure Storage, ustaw **Typ** połączonej usługi na **AzureStorage**. Następnie można określić następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z usługą Azure Storage dla właściwości connectionString. |Tak |

##### <a name="example"></a>Przykład

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Połączona usługa Azure Storage SAS
Połączona usługa Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia ona fabrykę danych z ograniczonym dostępnym przez czas dostępem do wszystkich/konkretnych zasobów (BLOB/Container) w magazynie. Aby połączyć konto usługi Azure Storage z fabryką danych przy użyciu sygnatury dostępu współdzielonego, Utwórz połączoną usługę Azure Storage SAS. Aby zdefiniować połączoną usługę Azure Storage SAS, ustaw **Typ** połączonej usługi na **AzureStorageSas**. Następnie można określić następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| sasUri |Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów usługi Azure Storage, takich jak obiekt BLOB, kontener lub tabela. |Tak |

##### <a name="example"></a>Przykład

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł dotyczący [łącznika usługi Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych obiektów blob platformy Azure, ustaw **Typ** zestawu danych na **AzureBlob**. Następnie określ następujące właściwości specyficzne dla obiektów blob platformy Azure w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów BLOB. Przykład: myblobcontainer\myblobfolder\ |Tak |
| fileName |Nazwa obiektu BLOB. Nazwa pliku jest opcjonalna i uwzględnia wielkość liter.<br/><br/>W przypadku określenia nazwy pliku działanie (łącznie z kopią) działa w określonym obiekcie blob.<br/><br/>Jeśli nazwa pliku nie jest określona, Copy zawiera wszystkie obiekty blob w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można jej użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika obiektów blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) .

### <a name="blobsource-in-copy-activity"></a>BlobSource w działaniu kopiowania
W przypadku kopiowania danych z usługi Azure Blob Storage Ustaw **Typ Source** działania Copy na **BlobSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |True (wartość domyślna), FAŁSZ |Nie |

#### <a name="example-blobsource"></a>Przykład: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>Wartość blobsink w działaniu kopiowania
Jeśli kopiujesz dane do Blob Storage platformy Azure, ustaw **Typ ujścia** działania kopiowania na **wartość blobsink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub system plików. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><br/><b>MergeFiles (domyślnie):</b> scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu BLOB jest określona, scaloną nazwą pliku będzie określona nazwa; w przeciwnym razie zostanie automatycznie wygenerowana nazwa pliku. |Nie |

#### <a name="example-blobsink"></a>Przykład: wartość blobsink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika obiektów blob platformy Azure](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure Data Lake Store połączoną usługę, ustaw typ połączonej usługi na **AzureDataLakeStore** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzureDataLakeStore** | Tak |
| dataLakeStoreUri | Określ informacje o koncie Azure Data Lake Store. Jest w następującym formacie: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/` . | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy Data Lake Store. | Wymagane dla ujścia |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak (w przypadku uwierzytelniania nazwy głównej usługi) |
| servicePrincipalKey | Określ klucz aplikacji. | Tak (w przypadku uwierzytelniania nazwy głównej usługi) |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak (w przypadku uwierzytelniania nazwy głównej usługi) |
| autoryzacja | Kliknij przycisk **Autoryzuj** w **Edytorze Data Factory** i wprowadź poświadczenia, które PRZYPISUJE automatycznie wygenerowany adres URL autoryzacji do tej właściwości. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika)|
| sessionId | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być użyty tylko raz. To ustawienie jest generowane automatycznie, gdy jest używany Edytor Data Factory. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika) |

#### <a name="example-using-service-principal-authentication"></a>Przykład: używanie uwierzytelniania nazwy głównej usługi
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Przykład: używanie uwierzytelniania poświadczeń użytkownika
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Azure Data Lake Store, ustaw **Typ** zestawu danych na **AzureDataLakeStore** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| folderPath |Ścieżka do kontenera i folderu w magazynie Azure Data Lake. |Tak |
| fileName |Nazwa pliku w magazynie Azure Data Lake. Nazwa pliku jest opcjonalna i uwzględnia wielkość liter. <br/><br/>W przypadku określenia nazwy pliku działanie (łącznie z kopią) działa w określonym pliku.<br/><br/>Jeśli nazwa pliku nie jest określona, Copy zawiera wszystkie pliki w folderPath dla wejściowego zestawu danych.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest właściwością opcjonalną. Można jej użyć do określenia dynamicznego folderPath i nazwy pliku dla danych szeregów czasowych. Na przykład folderPath może być sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example"></a>Przykład
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) .

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Źródło Azure Data Lake Store w działaniu kopiowania
W przypadku kopiowania danych z Azure Data Lake Store Ustaw **Typ Source** działania Copy na **AzureDataLakeStoreSource** i określ następujące właściwości w sekcji **Source** :

**AzureDataLakeStoreSource** obsługuje następujące właściwości **typeProperties** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |True (wartość domyślna), FAŁSZ |Nie |

#### <a name="example-azuredatalakestoresource"></a>Przykład: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store ujścia w działaniu kopiowania
Jeśli kopiujesz dane do Azure Data Lake Store, ustaw **Typ ujścia** działania kopiowania na **AzureDataLakeStoreSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone przy użyciu pierwszego poziomu folderu docelowego. Pliki docelowe są tworzone z automatycznie wygenerowaną nazwą.<br/><br/><b>MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu BLOB jest określona, scaloną nazwą pliku będzie określona nazwa; w przeciwnym razie zostanie automatycznie wygenerowana nazwa pliku. |Nie |

#### <a name="example-azuredatalakestoresink"></a>Przykład: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) .

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure Cosmos DB połączoną usługę, ustaw **Typ** połączonej usługi na **DocumentDb** i określ następujące właściwości w sekcji **typeProperties** :

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z bazą danych Azure Cosmos DB. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Azure Cosmos DB, ustaw **Typ** zestawu danych na **DocumentDbCollection** i określ następujące właściwości w sekcji **typeProperties** :

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| CollectionName |Nazwa kolekcji Azure Cosmos DB. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) .

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Źródło kolekcji Azure Cosmos DB w działaniu kopiowania
W przypadku kopiowania danych z Azure Cosmos DB Ustaw **Typ Source** działania Copy na **DocumentDbCollectionSource** i określ następujące właściwości w sekcji **Source** :


| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie, aby odczytać dane. |Ciąg zapytania obsługiwany przez Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znak specjalny wskazujący, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Azure Cosmos DB to Magazyn NoSQL dla dokumentów JSON, w którym zagnieżdżone struktury są dozwolone. Azure Data Factory umożliwia użytkownikowi odróżnienie hierarchii za pośrednictwem nestingSeparator, czyli "." w powyższych przykładach. Po separatorze działanie kopiowania będzie generować obiekt "name" z trzema elementami podrzędnymi od pierwszej, Środkowej i ostatnich, zgodnie z parametrami "name. First", "name. Middle" i "name. Last" w definicji tabeli. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB ujścia kolekcji w działaniu kopiowania
Jeśli kopiujesz dane do Azure Cosmos DB, ustaw **Typ ujścia** działania kopiowania na **DocumentDbCollectionSink** i określ następujące właściwości w sekcji **ujścia** :

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Znak specjalny w nazwie kolumny źródłowej, aby wskazać, że jest wymagany dokument zagnieżdżony. <br/><br/>Na przykład powyżej: `Name.First` w tabeli danych wyjściowych powstaje następująca struktura JSON w Cosmos DB dokumencie:<br/><br/>"Name": {<br/>    "First": "Jan"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do Azure Cosmos DB usługi do tworzenia dokumentów.<br/><br/>Można dostosować wydajność podczas kopiowania danych do/z Azure Cosmos DB za pomocą tej właściwości. Po zwiększeniu writeBatchSize można oczekiwać większej wydajności, ponieważ wysyłane są bardziej równoległe żądania do Azure Cosmos DB. Należy jednak unikać ograniczania, które może zgłosić komunikat o błędzie: "częstotliwość żądań jest duża".<br/><br/>Ograniczanie przepustowości zależy od wielu czynników, w tym rozmiaru dokumentów, liczby warunków w dokumentach, zasad indeksowania kolekcji docelowej itd. W przypadku operacji kopiowania można użyć lepszej kolekcji (na przykład S3), aby uzyskać dostęp do najbardziej dostępnej przepływności (2 500 jednostek żądań/sekundę). |Liczba całkowita |Nie (domyślnie: 5) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) .

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure SQL Database połączoną usługę, ustaw **Typ** połączonej usługi na **AzureSqlDatabase** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure SQL Database dla właściwości connectionString. |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Azure SQL Database, ustaw **Typ** zestawu danych na **wartość azuresqltable** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu Azure SQL Database, do którego odwołuje się połączona usługa. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w działaniu kopiowania
W przypadku kopiowania danych z Azure SQL Database Ustaw **Typ Source** działania Copy na **sqlsource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Obiekt sink SQL w działaniu kopiowania
Jeśli kopiujesz dane do Azure SQL Database, ustaw **Typ ujścia** działania Copy na **sqlsink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowaną identyfikatorem wycinka, która jest używana do czyszczenia danych określonego wycinka po ponownym uruchomieniu. |Nazwa kolumny kolumny zawierającej dane typu binary (32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, która upserts (aktualizuje/wstawia) dane do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) .

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Azure Synapse Analytics, ustaw **Typ** połączonej usługi na **AzureSqlDW** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem usługi Azure Synapse Analytics dla właściwości connectionString. |Tak |



#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Azure Synapse Analytics, ustaw **Typ** zestawu danych na **AzureSqlDWTable** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych usługi Azure Synapse Analytics, do której odwołuje się połączona usługa. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) .

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Źródło analizy usługi Azure Synapse w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Synapse Analytics, ustaw **Typ Source** działania Copy na **SqlDWSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Ujścia usługi Azure Synapse Analytics w działaniu kopiowania
Jeśli kopiujesz dane do usługi Azure Synapse Analytics, ustaw **Typ ujścia** działania Copy na **SqlDWSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy należy używać elementu Base (jeśli ma zastosowanie) zamiast mechanizmu BULKINSERT. <br/><br/> **Korzystanie z bazy danych Base jest zalecanym sposobem ładowania do usługi Azure Synapse Analytics.** |Prawda <br/>False (domyślnie) |Nie |
| polyBaseSettings |Grupa właściwości, które można określić, gdy właściwość **allowPolybase** ma **wartość true**. |&nbsp; |Nie |
| rejectValue |Określa liczbę lub procent wierszy, które można odrzucić przed zakończeniem wykonywania zapytania. <br/><br/>Dowiedz się więcej o opcjach odrzucenia bazy danych w sekcji **argumenty** w temacie [Create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (wartość domyślna), 1, 2,... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału, czy wartość procentowa. |Wartość (domyślnie), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed ponownym obliczeniem procentu odrzuconych wierszy. |1, 2,... |Tak, w przypadku **odrzucenia** **wartości procentowej** |
| useTypeDefault |Określa, jak obsługiwać brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego.<br/><br/>Więcej informacji na temat tej właściwości znajduje się w sekcji argumentów w temacie [Create External File Format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |Prawda, FAŁSZ (wartość domyślna) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Azure Wyszukiwanie poznawcze, ustaw **Typ** połączonej usługi na **AzureSearch** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| url | Adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora dla usługi wyszukiwania. | Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure wyszukiwanie poznawcze](data-factory-azure-search-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Wyszukiwanie poznawcze platformy Azure, ustaw **Typ** zestawu danych na **AzureSearchIndex** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureSearchIndex**.| Tak |
| indexName | Nazwa indeksu wyszukiwania. Data Factory nie tworzy indeksu. Indeks musi istnieć na platformie Azure Wyszukiwanie poznawcze. | Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure wyszukiwanie poznawcze](data-factory-azure-search-connector.md#dataset-properties) .

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Działanie ujścia indeksu Wyszukiwanie poznawcze platformy Azure w działaniu kopiowania
Jeśli kopiujesz dane do indeksu wyszukiwania, ustaw **Typ ujścia** działania kopiowania na **AzureSearchIndexSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub zamienić, gdy dokument już istnieje w indeksie. | Scal (domyślnie)<br/>Przekazywanie| Nie |
| WriteBatchSize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. | od 1 do 1 000. Wartość domyślna to 1000. | Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Azure wyszukiwanie poznawcze](data-factory-azure-search-connector.md#copy-activity-properties) .

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: połączona usługa Azure Storage i połączona usługa Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto usługi Azure Storage z fabryką danych przy użyciu **klucza konta**, Utwórz połączoną usługę Azure Storage. Aby zdefiniować połączoną usługę Azure Storage, ustaw **Typ** połączonej usługi na **AzureStorage**. Następnie można określić następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **AzureStorage** |Tak |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z usługą Azure Storage dla właściwości connectionString. |Tak |

**Przykład:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Połączona usługa Azure Storage SAS
Połączona usługa Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia ona fabrykę danych z ograniczonym dostępnym przez czas dostępem do wszystkich/konkretnych zasobów (BLOB/Container) w magazynie. Aby połączyć konto usługi Azure Storage z fabryką danych przy użyciu sygnatury dostępu współdzielonego, Utwórz połączoną usługę Azure Storage SAS. Aby zdefiniować połączoną usługę Azure Storage SAS, ustaw **Typ** połączonej usługi na **AzureStorageSas**. Następnie można określić następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **AzureStorageSas** |Tak |
| sasUri |Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów usługi Azure Storage, takich jak obiekt BLOB, kontener lub tabela. |Tak |

**Przykład:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł dotyczący [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych tabeli platformy Azure, ustaw **Typ** zestawu danych na **Azure**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, do której odwołuje się połączona usługa. |Tak. Gdy tabelaname jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytanie, są kopiowane do miejsca docelowego. |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł dotyczący [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) .

### <a name="azure-table-source-in-copy-activity"></a>Źródło tabeli platformy Azure w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Table Storage, ustaw **Typ źródła** działania Copy na **AzureTableSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania w tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy tabelaname jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytanie, są kopiowane do miejsca docelowego. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy w trakcie połknięcia wyjątek tabeli nie istnieje. |Prawda<br/>Fałsz |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł dotyczący [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) .

### <a name="azure-table-sink-in-copy-activity"></a>Obiekt sink tabeli platformy Azure w działaniu kopiowania
Jeśli kopiujesz dane do usługi Azure Table Storage, ustaw **Typ ujścia** działania Copy na **AzureTableSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez ujścia. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli wyjściowej ze zgodnymi partycjami i kluczami wierszy mają zamienione lub scalone wartości. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (Scalanie i zamienianie), zobacz sekcję [Wstawianie lub scalanie jednostek](/rest/api/storageservices/Insert-Or-Merge-Entity) oraz [Wstawianie lub zastępowanie](/rest/api/storageservices/Insert-Or-Replace-Entity) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna opcja usuwa wiersze w tabeli wyjściowej, które nie istnieją w danych wejściowych. |Scal (domyślnie)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure po trafieniu writeBatchSize lub writeBatchTimeout. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure po trafieniu writeBatchSize lub writeBatchTimeout |zakres czasu<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (Domyślnie wartość domyślna limitu czasu klienta magazynu 90 s) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz artykuł dotyczący [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) .

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Amazon RedShift, ustaw **Typ** połączonej usługi na **AmazonRedshift** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Adres IP lub nazwa hosta serwera Amazon RedShift. |Tak |
| port |Numer portu TCP używanego przez serwer Amazon RedShift do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 5439 |
| database |Nazwa bazy danych Amazon RedShift. |Tak |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych programu. |Tak |
| hasło |Hasło konta użytkownika. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika Amazon RedShift.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Amazon RedShift, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Amazon RedShift, do której odwołuje się połączona usługa. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona) |


#### <a name="example"></a>Przykład

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika Amazon RedShift.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z Amazon RedShift, ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie (Jeśli określono element **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika Amazon RedShift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę IBM DB2, ustaw **Typ** połączonej usługi na **OnPremisesDB2** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera bazy danych DB2. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| schema |Nazwa schematu w bazie danych programu. Nazwa schematu jest uwzględniana wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych programu DB2. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych DB2. |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika programu IBM DB2.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych DB2, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych DB2, do której odwołuje się połączona usługa. W tabeli TableName jest rozróżniana wielkość liter. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona)

#### <a name="example"></a>Przykład
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika programu IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z programu IBM DB2 Ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `"query": "select * from "MySchema"."MyTable""`. |Nie (Jeśli określono element **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika programu IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę MySQL, ustaw **Typ** połączonej usługi na **OnPremisesMySql** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera MySQL. |Tak |
| database |Nazwa bazy danych MySQL. |Tak |
| schema |Nazwa schematu w bazie danych programu. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych MySQL. Możliwe wartości to: `Basic` . |Tak |
| userName |Określ nazwę użytkownika w celu nawiązania połączenia z bazą danych MySQL. |Tak |
| hasło |Określ hasło dla podanego konta użytkownika. |Tak |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych MySQL. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych MySQL, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, do której odwołuje się połączona usługa. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych MySQL Ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie (Jeśli określono element **TableName** **zestawu danych** ) |


#### <a name="example"></a>Przykład
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) .

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Oracle, ustaw **Typ** połączonej usługi na **OnPremisesOracle** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| drivertype | Określ, który sterownik ma być używany do kopiowania danych z/do Oracle Database. Dozwolone wartości to **Microsoft** lub **ODP** (wartość domyślna). Zobacz sekcję obsługiwana wersja i instalacja w artykule Szczegóły sterownika. | Nie |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Oracle Database dla właściwości connectionString. | Tak |
| gatewayName | Nazwa bramy, która jest używana do nawiązywania połączenia z lokalnym serwerem Oracle |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Oracle, ustaw **Typ** zestawu danych na **Oracle**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w Oracle Database, do której odwołuje się połączona usługa. |Nie (Jeśli określono **oracleReaderQuery** of **OracleSource** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) .

### <a name="oracle-source-in-copy-activity"></a>Źródło Oracle w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych programu Oracle, ustaw **Typ Source** działania Copy na **OracleSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable` <br/><br/>Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: `select * from MyTable` |Nie (Jeśli określono element **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

### <a name="oracle-sink-in-copy-activity"></a>Działanie ujścia Oracle w działaniu kopiowania
Jeśli kopiujesz dane do bazy danych programu Oracle, ustaw **Typ ujścia** działania kopiowania na **OracleSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 100) |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowaną identyfikatorem wycinka, która jest używana do czyszczenia danych określonego wycinka po ponownym uruchomieniu. |Nazwa kolumny kolumny zawierającej dane typu binary (32). |Nie |

#### <a name="example"></a>Przykład
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę PostgreSQL, ustaw **Typ** połączonej usługi na **OnPremisesPostgreSql** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera PostgreSQL. |Tak |
| database |Nazwa bazy danych PostgreSQL. |Tak |
| schema |Nazwa schematu w bazie danych programu. Nazwa schematu jest uwzględniana wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych PostgreSQL. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych PostgreSQL. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych PostgreSQL, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, do której odwołuje się połączona usługa. W tabeli TableName jest rozróżniana wielkość liter. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych PostgreSQL należy ustawić **Typ źródła** działania kopiowania na **RelationalSource** i określić następujące właściwości w sekcji **Źródło** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: "Query": "SELECT * FROM \" webschema \" . \" MyTable \" ". |Nie (Jeśli określono element **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) .

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę SAP Business Warehouse (BW), ustaw **Typ** połączonej usługi na **SAPBW** i określ następujące właściwości w sekcji **typeProperties** :

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | ciąg | Tak
systemNumber | Numer systemu SAP BW. | Dwucyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak
clientId | Identyfikator klienta klienta w systemie SAP w. | 3-cyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
gatewayName | Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalnym wystąpieniem SAP BW. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

#### <a name="example"></a>Przykład

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych SAP BW, ustaw **Typ** zestawu danych na **relacyjny**. Brak właściwości specyficznych dla typu, które są obsługiwane dla SAP BWgo zestawu danych typu **relacyjnego**.

#### <a name="example"></a>Przykład

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z programu SAP Business Warehouse, ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie MDX do odczytu danych z wystąpienia SAP BW. | Zapytanie MDX. | Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) .

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SAP HANA połączoną usługę, ustaw **Typ** połączonej usługi na **SapHana** i określ następujące właściwości w sekcji **typeProperties** :

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa niestandardowego portu, określ `server:port` . | ciąg | Tak
authenticationType | Typ uwierzytelniania. | . "Podstawowa" lub "Windows" | Tak
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
gatewayName | Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalnym wystąpieniem SAP HANA. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

#### <a name="example"></a>Przykład

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych SAP HANA, ustaw **Typ** zestawu danych na **relacyjny**. Brak właściwości specyficznych dla typu, które są obsługiwane dla SAP HANAgo zestawu danych typu **relacyjnego**.

#### <a name="example"></a>Przykład

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z SAP HANA magazynu danych ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie SQL do odczytu danych z wystąpienia SAP HANA. | Zapytanie SQL. | Tak |


#### <a name="example"></a>Przykład


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) .


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Połączona usługa
Utworzysz połączoną usługę typu **OnPremisesSqlServer** , aby połączyć bazę danych SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna mieć wartość: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ informacje o connectionString, które są konieczne do nawiązania połączenia z bazą danych SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z bazą danych SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **nazwa_domeny \\ nazwa_użytkownika**. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować za pomocą polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (Właściwość **EncryptedCredential** ):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: kod JSON używany do uwierzytelniania SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: kod JSON używany do uwierzytelniania systemu Windows

Jeśli określono nazwę użytkownika i hasło, brama używa ich do personifikacji określonego konta użytkownika w celu nawiązania połączenia z bazą danych SQL Server. W przeciwnym razie Brama łączy się z SQL Server bezpośrednio z kontekstem zabezpieczeń bramy (jego konto uruchamiania).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych SQL Server, ustaw **Typ** zestawu danych na **SqlServerName** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Server, do której odwołuje się połączona usługa. |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SQL Server](data-factory-sqlserver-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych SQL Server, ustaw **Typ źródła** działania Copy na **sqlsource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. Może odwoływać się do wielu tabel z bazy danych, do której odwołuje się wejściowy zestaw danych. Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: select from MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla elementu sqlsource, działanie Copy uruchamia to zapytanie względem źródła bazy danych SQL Server, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).

Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Structure są używane do tworzenia zapytania select do uruchamiania w bazie danych SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> W przypadku korzystania z **sqlReaderStoredProcedureName**, nadal trzeba określić wartość właściwości **TableName** w kodzie JSON zestawu danych. Nie przeprowadzono żadnych operacji sprawdzania poprawności dla tej tabeli, chociaż.


#### <a name="example"></a>Przykład
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

W tym przykładzie **sqlReaderQuery** jest określony dla elementu sqlsource. Działanie kopiowania uruchamia to zapytanie względem źródła danych SQL Server, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry). SqlReaderQuery może odwoływać się do wielu tabel w bazie danych, do których odwołuje się wejściowy zestaw danych. Nie jest ograniczony tylko do zestawu tabel jako tabeli typeProperty.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select do uruchomienia względem bazy danych SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>Obiekt sink SQL w działaniu kopiowania
Jeśli kopiujesz dane do bazy danych SQL Server, ustaw **Typ ujścia** działania Copy na **sqlsink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. Aby uzyskać więcej informacji, zobacz sekcję powtarzalność. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowaną identyfikatorem wycinka, która jest używana do czyszczenia danych określonego wycinka po ponownym uruchomieniu. Aby uzyskać więcej informacji, zobacz sekcję powtarzalność. |Nazwa kolumny kolumny zawierającej dane typu binary (32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, która upserts (aktualizuje/wstawia) dane do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład
Potok zawiera działanie kopiowania, które jest skonfigurowane do używania tych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **sqlsink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) .

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Sybase, ustaw **Typ** połączonej usługi na **OnPremisesSybase** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera programu Sybase. |Tak |
| database |Nazwa bazy danych programu Sybase. |Tak |
| schema |Nazwa schematu w bazie danych programu. |Nie |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia z bazą danych programu Sybase. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych programu Sybase. |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych programu Sybase, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych programu Sybase, do której odwołuje się połączona usługa. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych programu Sybase Ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie (Jeśli określono element **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) .

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę programu Teradata, ustaw **Typ** połączonej usługi na **OnPremisesTeradata** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera programu Teradata. |Tak |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia z bazą danych programu Teradata. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych programu Teradata. |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych obiektów BLOB programu Teradata, ustaw **Typ** zestawu danych na **relacyjny**. Obecnie nie istnieją właściwości typu obsługiwane dla zestawu danych programu Teradata.

#### <a name="example"></a>Przykład
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
W przypadku kopiowania danych z bazy danych programu Teradata Ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Cassandra, ustaw **Typ** połączonej usługi na **OnPremisesCassandra** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| host |Co najmniej jeden adres IP lub nazwa hosta serwerów Cassandra.<br/><br/>Określ rozdzieloną przecinkami listę adresów IP lub nazw hostów, które mają być połączone jednocześnie ze wszystkimi serwerami. |Tak |
| port |Port TCP, którego serwer Cassandra używa do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowa lub anonimowa |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, Jeśli AuthenticationType ma wartość Basic. |
| hasło |Określ hasło dla konta użytkownika. |Tak, Jeśli AuthenticationType ma wartość Basic. |
| gatewayName |Nazwa bramy, która jest używana do nawiązywania połączenia z lokalną bazą danych Cassandra. |Tak |
| encryptedCredential |Poświadczenie zaszyfrowane przez bramę. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Cassandra, ustaw **Typ** zestawu danych na **CassandraTable** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Tak (jeśli nie zdefiniowano **kwerendy** dla **CassandraSource** ). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (jeśli nie zdefiniowano **kwerendy** dla **CassandraSource** ). |

#### <a name="example"></a>Przykład

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) .

### <a name="cassandra-source-in-copy-activity"></a>Źródło Cassandra w działaniu kopiowania
Jeśli kopiujesz dane z Cassandra, ustaw **Typ Source** działania Copy na **CassandraSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Zapytanie SQL-92 zapytania lub CQL. Zobacz [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>W przypadku korzystania z zapytania SQL określ **nazwę przestrzeni kluczy. nazwa tabeli** do reprezentowania tabeli, którą chcesz zbadać. |Nie (Jeśli określono element TableName i przestrzeń kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik dla danych, aby spełnić żądanie odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nie. Wartość domyślna to 1. |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) .

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę MongoDB, ustaw **Typ** połączonej usługi na **OnPremisesMongoDB** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP, którego serwer MongoDB używa do nasłuchiwania połączeń klientów. |Opcjonalna, wartość domyślna: 27017 |
| authenticationType |Podstawowa lub anonimowa. |Tak |
| nazwa użytkownika |Konto użytkownika do uzyskiwania dostępu do MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, która ma zostać użyta do sprawdzenia poświadczeń w celu uwierzytelnienia. |Opcjonalne (jeśli jest używane uwierzytelnianie podstawowe). Domyślnie: używa konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| Bazy |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| gatewayName |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Tak |
| encryptedCredential |Poświadczenie zaszyfrowane przez bramę. |Opcjonalne |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych MongoDB, ustaw **Typ** zestawu danych na **MongoDbCollection** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Źródło MongoDB w działaniu kopiowania
Jeśli kopiujesz dane z MongoDB, ustaw **Typ Source** działania Copy na **MongoDbSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL-92. Przykład: `select * from MyTable`. |Nie (Jeśli określono **CollectionName** **zestawu danych** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Amazon S3, ustaw **Typ** połączonej usługi na **typu awsaccesskey** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza dostępu tajnego. |ciąg |Tak |
| secretAccessKey |Sam klucz dostępu tajnego. |Zaszyfrowany ciąg tajny |Tak |

#### <a name="example"></a>Przykład
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Amazon S3, ustaw **Typ** zestawu danych na **AmazonS3** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| zasobnikname |Nazwa zasobnika S3. |Ciąg |Tak |
| key |Klucz obiektu S3. |Ciąg |Nie |
| prefiks |Prefiks klucza obiektu S3. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. Stosuje się tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| Wersja |Wersja obiektu S3 w przypadku włączenia obsługi wersji S3. |Ciąg |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie | |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> + Key określa lokalizację obiektu S3, gdzie zasobnik jest kontenerem głównym dla obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

#### <a name="example-sample-dataset-with-prefix"></a>Przykład: przykładowy zestaw danych z prefiksem

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Przykład: zestaw danych przykładowych (z wersją)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Przykład: ścieżki dynamiczne dla S3
W przykładzie używamy stałych wartości dla właściwości Key i datazasobnikname w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Można Data Factory obliczać klucz i zasobnikname dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak parametru slicestart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Można to zrobić w przypadku właściwości prefix zestawu danych Amazon S3. Zobacz [funkcje Data Factory i zmienne systemowe](data-factory-functions-variables.md) , aby uzyskać listę obsługiwanych funkcji i zmiennych.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli kopiujesz dane z usługi Amazon S3, ustaw **Typ Source** działania Copy na **FileSystemSource** i określ następujące właściwości w sekcji **Source** :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Określa, czy w katalogu mają być cykliczne listy obiektów S3. |PRAWDA/FAŁSZ |Nie |


#### <a name="example"></a>Przykład


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>System plików


### <a name="linked-service"></a>Połączona usługa
Lokalny system plików można połączyć z fabryką danych Azure przy użyciu połączonej usługi **lokalnego serwera plików** . Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla połączonej usługi lokalnego serwera plików.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Upewnij się, że Właściwość Type ma wartość **OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę katalogu głównego folderu, który ma zostać skopiowany. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych. |Tak |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (w przypadku wybrania opcji encryptedCredential) |
| hasło |Określ hasło użytkownika (UserID). |Nie (w przypadku wybrania opcji encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (Jeśli wybierzesz opcję określenia identyfikatora użytkownika i hasła w postaci zwykłego tekstu) |
| gatewayName |Określa nazwę bramy, która Data Factory powinna być używana do nawiązywania połączenia z lokalnym serwerem plików. |Tak |

#### <a name="sample-folder-path-definitions"></a>Przykładowe definicje ścieżek folderów

| Scenariusz | Definicja hosta w połączonej usłudze | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na maszynie Zarządzanie danymi bramy: <br/><br/>Przykłady: D: \\ \* lub D:\folder\subfolder\\* |D: \\ \\ (for Zarządzanie danymi Gateway 2,0 i nowsze wersje) <br/><br/> localhost (dla wcześniejszych wersji niż Zarządzanie danymi Gateway 2,0) |.\\\\ lub \\ \\ podfolder folderu (dla Zarządzanie danymi Gateway 2,0 i nowsze wersje) <br/><br/>D: \\ \\ lub d: podfolder \\ \\ folderu \\ \\ (dla wersji bramy poniżej 2,0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\ \\ udział \\ \* /folder \\ \\ \\ udziału \\ \\ w udziale\\* |\\\\\\\\\\ \\ Udostępnij udział |.\\\\ lub \\ \\ podfolder folderu |


#### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: używanie nazwy użytkownika i hasła w postaci zwykłego tekstu

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Przykład: korzystanie z encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika systemu plików](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych systemu plików, ustaw **Typ** zestawu danych na wartość **udziału** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa ścieżkę podrzędną do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "fileFilter": "*. log"<br/>Przykład 2: "fileFilter": 2016-1-?. zawierającego<br/><br/>Należy zauważyć, że fileFilter ma zastosowanie do wejściowego zestawu danych. |Nie |
| partitionedBy |Możesz użyć partitionedBy, aby określić dynamiczny folderPath/fileName dla danych szeregów czasowych. Przykładem jest folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**; i obsługiwane poziomy to: **optymalne** i **najszybszy**. Zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można jednocześnie używać fileName i fileFilter.

#### <a name="example"></a>Przykład

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika systemu plików](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
W przypadku kopiowania danych z systemu plików Ustaw **Typ Source** działania Copy na **FileSystemSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Ujścia systemu plików w działaniu kopiowania
W przypadku kopiowania danych do systemu plików Ustaw **Typ ujścia** działania kopiowania na **FileSystemSink** i określ następujące właściwości w sekcji **ujścia** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub system plików. |**PreserveHierarchy:** Zachowuje hierarchię plików w folderze docelowym. Oznacza to, że ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/>**FlattenHierarchy:** Wszystkie pliki z folderu źródłowego są tworzone przy użyciu pierwszego poziomu folderu docelowego. Pliki docelowe są tworzone z automatycznie wygenerowaną nazwą.<br/><br/>**MergeFiles:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. |Nie |

Funkcja

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę FTP, ustaw **Typ** połączonej usługi na **ftpserver** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| host |Nazwa lub adres IP serwera FTP |Tak |&nbsp; |
| authenticationType |Określanie typu uwierzytelniania |Tak |Podstawowa, anonimowa |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera FTP |Nie |&nbsp; |
| hasło |Hasło użytkownika (username) |Nie |&nbsp; |
| encryptedCredential |Zaszyfrowane poświadczenia w celu uzyskania dostępu do serwera FTP |Nie |&nbsp; |
| gatewayName |Nazwa bramy Zarządzanie danymi w celu nawiązania połączenia z lokalnym serwerem FTP |Nie |&nbsp; |
| port |Port, na którym nasłuchuje serwer FTP |Nie |21 |
| enableSsl |Określ, czy ma być używany protokół FTP za pośrednictwem protokołu SSL/TLS |Nie |true |
| enableServerCertificateValidation |Określ, czy włączyć weryfikację certyfikatu protokołu TLS/SSL serwera podczas korzystania z protokołu FTP za pośrednictwem protokołu SSL/TLS |Nie |true |

#### <a name="example-using-anonymous-authentication"></a>Przykład: używanie uwierzytelniania anonimowego

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Przykład: używanie nazwy użytkownika i hasła w postaci zwykłego tekstu na potrzeby uwierzytelniania podstawowego

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Przykład: Używanie portu, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Przykład: używanie encryptedCredential do uwierzytelniania i bramy

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika FTP](data-factory-ftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych FTP, ustaw **Typ** zestawu danych na wartość **udziału** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych. Ta właściwość nie jest obsługiwana w systemie plików HDFS. |Nie |
| partitionedBy |partitionedBy można użyć, aby określić dynamiczny folderPath, filename dla danych szeregów czasowych. Na przykład folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**; i obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy ma być używany tryb transferu danych binarnych. Wartość true dla trybu binarnego i false ASCII. Wartość domyślna: true. Tej właściwości można użyć tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwy pliku i fileFilter nie można jednocześnie używać.

#### <a name="example"></a>Przykład

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika FTP](data-factory-ftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
W przypadku kopiowania danych z serwera FTP Ustaw **Typ Source** działania Copy na **FileSystemSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika FTP](data-factory-ftp-connector.md#copy-activity-properties) .


## <a name="hdfs"></a>SYSTEM PLIKÓW HDFS

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę HDFS, ustaw **Typ** połączonej usługi na **HDFS** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **HDFS** |Tak |
| Url |Adres URL systemu plików HDFS |Tak |
| authenticationType |Anonimowe lub Windows. <br><br> Aby skorzystać z **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z tą sekcją, aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika dla uwierzytelniania systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z systemem plików HDFS. |Tak |
| encryptedCredential |[Nowe-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) dane wyjściowe poświadczeń dostępu. |Nie |

#### <a name="example-using-anonymous-authentication"></a>Przykład: używanie uwierzytelniania anonimowego

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Przykład: używanie uwierzytelniania systemu Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika systemu plików HDFS.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych systemu plików HDFS, ustaw **Typ** zestawu danych na wartość **udziału** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: dla folder\subfolder określ podfolder folderu \\ \\ i dla d:\samplefolder, określ d: \\ \\ samplefolder.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy można użyć, aby określić dynamiczny folderPath, filename dla danych szeregów czasowych. Przykład: folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> nazwy pliku i fileFilter nie można jednocześnie używać.

#### <a name="example"></a>Przykład

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika systemu plików HDFS.

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli kopiujesz dane z systemu plików HDFS, ustaw **Typ Source** działania Copy na **FileSystemSource** i określ następujące właściwości w sekcji **Source** :

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący łącznika systemu plików HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę SFTP, ustaw **Typ** połączonej usługi na **SFTP** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Basic**, **SshPublicKey**. <br><br> Zapoznaj się z sekcją uwierzytelnianie podstawowe i [przy użyciu opcji uwierzytelniania klucza publicznego SSH](#using-ssh-public-key-authentication) , aby uzyskać więcej właściwości i próbek JSON. |Tak |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ drukowanie odcisku palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` ma wartość false.  |
| gatewayName |Nazwa bramy Zarządzanie danymi w celu nawiązania połączenia z lokalnym serwerem SFTP. | Tak w przypadku kopiowania danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia w celu uzyskania dostępu do serwera SFTP. Generowane automatycznie po określeniu uwierzytelniania podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania SshPublicKey (nazwa użytkownika i Ścieżka klucza prywatnego lub zawartość) w Kreatorze kopiowania lub podręcznym oknie dialogowym ClickOnce. | Nie. Stosuje się tylko w przypadku kopiowania danych z lokalnego serwera SFTP. |

#### <a name="example-using-basic-authentication"></a>Przykład: używanie uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `Basic` i określ następujące właściwości, oprócz ogólnych ŁĄCZNIKa SFTP, które zostały wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| hasło | Hasło użytkownika (username). | Tak |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: **uwierzytelnianie podstawowe z szyfrowanym poświadczeniem**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Używanie uwierzytelniania klucza publicznego SSH:**

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `SshPublicKey` i określ następujące właściwości, oprócz ogólnych ŁĄCZNIKa SFTP, które zostały wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Tak |
| privateKeyPath | Określ ścieżkę bezwzględną do pliku klucza prywatnego, do którego Brama może uzyskać dostęp. | Określ albo `privateKeyPath` `privateKeyContent` . <br><br> Stosuje się tylko w przypadku kopiowania danych z lokalnego serwera SFTP. |
| privateKeyContent | Serializowany ciąg zawartości klucza prywatnego. Kreator kopiowania może odczytać plik klucza prywatnego i automatycznie wyodrębnić zawartość klucza prywatnego. Jeśli używasz innego narzędzia/zestawu SDK, zamiast tego użyj właściwości privateKeyPath. | Określ albo `privateKeyPath` `privateKeyContent` . |
| Danym | Określ wartość Przekaż frazę/hasło w celu odszyfrowania klucza prywatnego, jeśli plik klucza jest chroniony za pomocą frazy Pass. | Tak, jeśli plik klucza prywatnego jest chroniony przez frazę Pass. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: **uwierzytelnianie SshPublicKey przy użyciu zawartości klucza prywatnego**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SFTP](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych SFTP, ustaw **Typ** zestawu danych na wartość **udziału** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie przykładowe połączone usługi i zestawy danych.<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter ma zastosowanie do wejściowego zestawu danych. Ta właściwość nie jest obsługiwana w systemie plików HDFS. |Nie |
| partitionedBy |partitionedBy można użyć, aby określić dynamiczny folderPath, filename dla danych szeregów czasowych. Na przykład folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy ma być używany tryb transferu danych binarnych. Wartość true dla trybu binarnego i false ASCII. Wartość domyślna: true. Tej właściwości można użyć tylko wtedy, gdy skojarzony typ połączonej usługi jest typu: FtpServer. |Nie |

> [!NOTE]
> nazwy pliku i fileFilter nie można jednocześnie używać.

#### <a name="example"></a>Przykład

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SFTP](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli kopiujesz dane ze źródła SFTP, ustaw **Typ Source** działania Copy na **FileSystemSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |



#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SFTP](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę HTTP, ustaw **Typ** połączonej usługi na **http** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url | Podstawowy adres URL serwera sieci Web | Tak |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Zapoznaj się z sekcjami poniżej tej tabeli, aby uzyskać więcej właściwości i próbek JSON odpowiednio dla tych typów uwierzytelniania. | Tak |
| enableServerCertificateValidation | Określ, czy włączyć weryfikację certyfikatu protokołu TLS/SSL serwera, jeśli źródłem jest serwer sieci Web HTTPS | Nie, wartość domyślna to true |
| gatewayName | Nazwa bramy Zarządzanie danymi, aby połączyć się z lokalnym źródłem HTTP. | Tak w przypadku kopiowania danych z lokalnego źródła HTTP. |
| encryptedCredential | Zaszyfrowane poświadczenia w celu uzyskania dostępu do punktu końcowego HTTP. Generowane automatycznie podczas konfigurowania informacji o uwierzytelnianiu w Kreatorze kopiowania lub w podręcznym oknie dialogowym ClickOnce. | Nie. Stosuje się tylko w przypadku kopiowania danych z lokalnego serwera HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Przykład: używanie uwierzytelniania podstawowego, szyfrowanego lub systemu Windows
Ustaw `authenticationType` jako `Basic` , `Digest` , lub `Windows` i określ następujące właściwości poza ogólnymi łącznikami http, które zostały wprowadzone powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika | Nazwa użytkownika, aby uzyskać dostęp do punktu końcowego HTTP. | Tak |
| hasło | Hasło użytkownika (username). | Tak |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Przykład: używanie uwierzytelniania ClientCertificate

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `ClientCertificate` i określ następujące właściwości poza ogólnymi ŁĄCZNIKami http, które zostały wprowadzone powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zakodowana algorytmem Base64 zawartość danych binarnych pliku wymiany informacji osobistych (PFX). | Określ albo `embeddedCertData` `certThumbprint` . |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany w magazynie certyfikatów maszyny bramy. Stosuje się tylko w przypadku kopiowania danych z lokalnego źródła HTTP. | Określ albo `embeddedCertData` `certThumbprint` . |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli używasz `certThumbprint` do uwierzytelniania, a certyfikat jest instalowany w magazynie osobistym komputera lokalnego, musisz udzielić uprawnienia do odczytu usługi bramy:

1. Uruchom program Microsoft Management Console (MMC). Dodaj przystawkę **Certyfikaty** , która jest przeznaczona dla **komputera lokalnego**.
2. Rozwiń węzeł **Certyfikaty**, **osobiste**, a następnie kliknij pozycję **Certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat ze sklepu osobistego, a następnie wybierz pozycję **wszystkie zadania** -> **Zarządzaj kluczami prywatnymi...**
3. Na karcie **zabezpieczenia** Dodaj konto użytkownika, pod którym działa usługa hosta bramy zarządzanie danymi z dostępem do odczytu do certyfikatu.

**Przykład: Użycie certyfikatu klienta:** Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa certyfikatu klienta zainstalowanego na komputerze z zainstalowaną Zarządzanie danymi bramą.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Przykład: używanie certyfikatu klienta w pliku
Ta połączona usługa łączy fabrykę danych z lokalnym serwerem sieci Web HTTP. Używa on pliku certyfikatu klienta na komputerze z zainstalowaną Zarządzanie danymi bramą.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika http](data-factory-http-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych HTTP, ustaw **Typ** zestawu danych na **http** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie zostanie określona, zostanie użyta tylko adres URL określony w definicji połączonej usługi. <br><br> Aby skonstruować dynamiczny adres URL, można użyć [funkcji Data Factory i zmiennych systemowych](data-factory-functions-variables.md), przykład: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | Nie |
| requestMethod | Metoda http. Dozwolone wartości to **Get** i **post**. | Nie. Wartość domyślna to `GET`. |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść dla żądania HTTP. | Nie |
| format | Jeśli chcesz po prostu **pobrać dane z punktu końcowego http, tak jakby** nie przeanalizować go, Pomiń ustawienia tego formatu. <br><br> Jeśli chcesz przeanalizować zawartość odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatu: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example-using-the-get-default-method"></a>Przykład: użycie metody GET (default)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Przykład: użycie metody POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika http](data-factory-http-connector.md#dataset-properties) .

### <a name="http-source-in-copy-activity"></a>Źródło HTTP w działaniu kopiowania
Jeśli kopiujesz dane ze źródła HTTP, ustaw **Typ Source** działania Copy na **HttpSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (TimeSpan) żądania HTTP w celu uzyskania odpowiedzi. Jest to limit czasu, który umożliwia uzyskanie odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. | Nie. Wartość domyślna: 00:01:40 |


#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika http](data-factory-http-connector.md#copy-activity-properties) .

## <a name="odata"></a>OData

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę OData, ustaw **Typ** połączonej usługi na **OData** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url |Adres URL usługi OData. |Tak |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia ze źródłem danych OData. <br/><br/> W przypadku protokołu OData w chmurze możliwe wartości to Anonymous, Basic i OAuth (Uwaga Azure Data Factory obecnie obsługuje tylko Azure Active Directory uwierzytelniania OAuth). <br/><br/> W przypadku lokalnego protokołu OData możliwe wartości to anonimowe, podstawowe i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| authorizedCredential |Jeśli używasz uwierzytelniania OAuth, kliknij przycisk **Autoryzuj** w kreatorze kopiowania Data Factory lub edytorze i wprowadź poświadczenia, a następnie wartość tej właściwości zostanie wygenerowana automatycznie. |Tak (tylko w przypadku korzystania z uwierzytelniania OAuth) |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną usługą OData. Określ tylko w przypadku kopiowania danych z lokalnego źródła OData. |Nie |

#### <a name="example---using-basic-authentication"></a>Przykład — używanie uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Przykład — używanie uwierzytelniania anonimowego

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Przykład — używanie uwierzytelniania systemu Windows do lokalnego źródła danych OData

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Przykład — używanie uwierzytelniania OAuth do uzyskiwania dostępu do źródła danych OData w chmurze
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika OData](data-factory-odata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych OData, ustaw **Typ** zestawu danych na **ODataResource** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| path |Ścieżka do zasobu OData |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika OData](data-factory-odata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane ze źródła strumieniowego OData, ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Przykład | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |"? $select = nazwa, opis&$top = 5" |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika OData](data-factory-odata-connector.md#copy-activity-properties) .


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę ODBC, ustaw **Typ** połączonej usługi na **OnPremisesOdbc** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Część poświadczeń braku dostępu do parametrów połączenia i opcjonalne zaszyfrowane poświadczenia. Zobacz przykłady w poniższych sekcjach. |Tak |
| poświadczenia |Część poświadczeń dostępu do parametrów połączenia określona w formacie wartości właściwości specyficznej dla sterownika. Przykład: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nie |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia z magazynem danych ODBC. Możliwe wartości to: Anonymous i Basic. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z magazynem danych ODBC. |Tak |

#### <a name="example---using-basic-authentication"></a>Przykład — używanie uwierzytelniania podstawowego

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Przykład — używanie uwierzytelniania podstawowego z szyfrowanymi poświadczeniami
Poświadczenia można zaszyfrować za pomocą polecenia cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) .

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Przykład: używanie uwierzytelniania anonimowego

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika ODBC](data-factory-odbc-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych ODBC, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w magazynie danych ODBC. |Tak |


#### <a name="example"></a>Przykład

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika ODBC](data-factory-odbc-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z magazynu danych ODBC, ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika ODBC](data-factory-odbc-connector.md#copy-activity-properties) .

## <a name="salesforce"></a>SalesForce


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę Salesforce, ustaw **Typ** połączonej usługi na **Salesforce** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| environmentUrl | Określ adres URL wystąpienia usługi Salesforce. <br><br> -Wartość domyślna to "https: \/ /login.Salesforce.com". <br> -Aby skopiować dane z piaskownicy, określ " https://test.salesforce.com ". <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład "https://[domena]. my. Salesforce. com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| Obiektu |Określ token zabezpieczający dla konta użytkownika. Zobacz [pobieranie tokenu zabezpieczającego](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) , aby uzyskać instrukcje dotyczące resetowania/pobierania tokenu zabezpieczającego. Aby uzyskać ogólne informacje na temat tokenów zabezpieczających, zobacz [zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Salesforce](data-factory-salesforce-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Salesforce, ustaw **Typ** zestawu danych na **relacyjny**, a następnie określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli określono **zapytanie** o **RelationalSource** ) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Salesforce](data-factory-salesforce-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z usługi Salesforce, ustaw **Typ Source** działania Copy na **RelationalSource** i określ następujące właściwości w sekcji **Source** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Zapytanie dotyczące języka SQL-92 lub zapytania o [obiekt SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Przykład: `select * from MyTable__c`. |Nie (Jeśli określono wartość **TableName** **zestawu danych** ) |

#### <a name="example"></a>Przykład



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Część "__c" nazwy interfejsu API jest wymagana dla dowolnego obiektu niestandardowego.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika usługi Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) .

## <a name="web-data"></a>Dane sieci Web

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować połączoną usługę sieci Web, ustaw **Typ** połączonej usługi na **Web** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Url |Adres URL źródła sieci Web |Tak |
| authenticationType |Anonimowe. |Tak |


#### <a name="example"></a>Przykład


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika tabeli sieci Web](data-factory-web-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych sieci Web, ustaw **Typ** zestawu danych na **webtable** i określ następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Typ zestawu danych. musi być ustawiona na **Webtable** |Tak |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, zostanie użyta tylko adres URL określony w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz sekcję Pobieranie indeksu tabeli w sekcji strony HTML, aby poznać procedurę pobierania indeksu tabeli na stronie HTML. |Tak |

#### <a name="example"></a>Przykład

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika tabeli sieci Web](data-factory-web-table-connector.md#dataset-properties) .

### <a name="web-source-in-copy-activity"></a>Źródło sieci Web w działaniu kopiowania
W przypadku kopiowania danych z tabeli sieci Web należy ustawić **Typ źródła** działania kopiowania na **websource**. Obecnie, gdy źródło w działaniu kopiowania jest typu **websource**, nie są obsługiwane żadne dodatkowe właściwości.

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika tabeli sieci Web](data-factory-web-table-connector.md#copy-activity-properties) .

## <a name="compute-environments"></a>ŚRODOWISKA OBLICZENIOWE
W poniższej tabeli wymieniono środowiska obliczeniowe obsługiwane przez Data Factory oraz działania przekształcania, które mogą być na nich uruchomione. Kliknij link do odpowiednich obliczeń, aby wyświetlić schematy JSON dla połączonej usługi, aby połączyć je z fabryką danych.

| Środowisko obliczeniowe | Działania |
| --- | --- |
| [Klaster HDInsight na żądanie](#on-demand-azure-hdinsight-cluster) lub [własny klaster usługi HDInsight](#existing-azure-hdinsight-cluster) |[Działanie niestandardowe platformy .NET](#net-custom-activity), [działanie Hive](#hdinsight-hive-activity), aktywność [świń](#hdinsight-pig-activity), działanie [MapReduce](#hdinsight-mapreduce-activity), działanie usługi Hadoop streaming, [działanie Spark](#hdinsight-spark-activity) |
| [Usługa Azure Batch](#azure-batch) |[Niestandardowe działanie platformy .NET](#net-custom-activity) |
| [Azure Machine Learning Studio (klasyczny)](#azure-machine-learning-studio-classic) | [Azure Machine Learning Studio (klasyczny) działanie wykonywania wsadowego](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (klasyczne) aktualizowanie zasobów](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [usługa Azure Synapse Analytics](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[Procedura składowana](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Klaster usługi Azure HDInsight na żądanie
Usługa Azure Data Factory może automatycznie utworzyć klaster usługi HDInsight na żądanie z systemem Windows/Linux na potrzeby przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu (Właściwość linkedServiceName w formacie JSON) skojarzone z klastrem. W tej połączonej usłudze można uruchomić następujące działania przekształcania: [aktywność niestandardowa platformy .NET](#net-custom-activity), działanie [Hive](#hdinsight-hive-activity), aktywność [świń](#hdinsight-pig-activity), działanie [MapReduce](#hdinsight-mapreduce-activity), działanie usługi Hadoop streaming, [działanie platformy Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji usługi Azure JSON połączonej na żądanie.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna mieć wartość **HDInsightOnDemand**. |Tak |
| clusterSize |Liczba węzłów procesu roboczego/danych w klastrze. Klaster usługi HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesu roboczego określonych dla tej właściwości. Węzły mają rozmiar Standard_D3, które mają 4 rdzenie, więc klaster z 4 węzłami roboczymi zajmuje 24 rdzenie (4 rdzenie \* dla węzłów procesu roboczego, a 2 \* 4 = 8 rdzeni dla węzłów głównych). Aby uzyskać szczegółowe informacje na temat warstwy Standard_D3, zobacz [Tworzenie klastrów Hadoop opartych na systemie Linux w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . |Tak |
| TimeToLive |Dozwolony czas bezczynności dla klastra usługi HDInsight na żądanie. Określa, jak długo klaster usługi HDInsight na żądanie pozostaje aktywny po zakończeniu działania, jeśli nie ma żadnych innych aktywnych zadań w klastrze.<br/><br/>Na przykład, Jeśli uruchomienie działania trwa 6 minut, a TimeToLive jest ustawiony na 5 minut, klaster pozostaje aktywny przez 5 minut po 6 minutach przetwarzania działania. Jeśli zostanie wykonane inne uruchomienie działania z oknem 6 minut, jest ono przetwarzane przez ten sam klaster.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może to trochę potrwać), dlatego użyj tego ustawienia w razie potrzeby w celu zwiększenia wydajności fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość TimeToLive jest ustawiona na 0, klaster zostanie usunięty zaraz po przetworzeniu działania. Z drugiej strony, jeśli ustawisz wysoką wartość, klaster może pozostać niepotrzebnie niekoniecznie wynikający z wysokich kosztów. W związku z tym ważne jest, aby ustawić odpowiednią wartość na podstawie Twoich potrzeb.<br/><br/>Wiele potoków może współużytkować to samo wystąpienie klastra usługi HDInsight na żądanie, jeśli wartość właściwości TimeToLive jest odpowiednio ustawiona |Tak |
| Wersja |Wersja klastra usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [obsługiwane wersje usługi HDInsight w Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nie |
| linkedServiceName |Połączona usługa Azure Storage, która będzie używana przez klaster na żądanie do przechowywania i przetwarzania danych. <p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, który używa Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z przetwarzania HDInsight w Azure Data Lake Store, Użyj działania kopiowania, aby skopiować dane z usługi Azure Blob Storage do Azure Data Lake Store.</p>  | Tak |
| additionalLinkedServiceNames |Określa dodatkowe konta magazynu dla połączonej usługi HDInsight, aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. |Nie |
| osType |Typ systemu operacyjnego. Dozwolone wartości to: Windows (domyślne) i Linux |Nie |
| hcatalogLinkedServiceName |Nazwa połączonej usługi Azure SQL, która wskazuje bazę danych HCatalog. Klaster usługi HDInsight na żądanie jest tworzony przy użyciu Azure SQL Database jako magazynu metadanych. |Nie |

### <a name="json-example"></a>Przykład JSON
Poniższy kod JSON definiuje połączoną usługę HDInsight na żądanie z systemem Linux. Usługa Data Factory automatycznie tworzy klaster usługi HDInsight **oparty na systemie Linux** podczas przetwarzania wycinka danych.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Więcej informacji można znaleźć w artykule dotyczącym [połączonych usług obliczeniowych](data-factory-compute-linked-services.md) .

## <a name="existing-azure-hdinsight-cluster"></a>Istniejący klaster usługi Azure HDInsight
Możesz utworzyć połączoną usługę Azure HDInsight, aby zarejestrować własny klaster usługi HDInsight przy użyciu Data Factory. W tej połączonej usłudze można uruchomić następujące działania przekształcania danych: [aktywność niestandardowa platformy .NET](#net-custom-activity), działanie programu [Hive](#hdinsight-hive-activity), działanie dotyczące [świń](#hdinsight-pig-activity), działanie [MapReduce](#hdinsight-mapreduce-activity), działanie usługi Hadoop streaming i [działanie platformy Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji usługi Azure JSON połączonej z usługą Azure HDInsight.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna być ustawiona na **HDInsight**. |Tak |
| clusterUri |Identyfikator URI klastra usługi HDInsight. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, który ma być używany do nawiązywania połączenia z istniejącym klastrem usługi HDInsight. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| linkedServiceName | Nazwa połączonej usługi Azure Storage odwołująca się do magazynu obiektów blob platformy Azure używanego przez klaster usługi HDInsight. <p>Obecnie nie można określić Azure Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do Data Lake Store, możesz uzyskać dostęp do danych znajdujących się w Azure Data Lake Store ze skryptów Hive/wieprzowych. </p>  |Tak |

Aby uzyskać listę obsługiwanych wersji klastrów usługi HDInsight, zobacz [obsługiwane wersje usługi HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Usługa Azure Batch
Można utworzyć połączoną usługę Azure Batch, aby zarejestrować pulę usługi Batch maszyn wirtualnych z fabryką danych. Działania niestandardowe platformy .NET można uruchamiać przy użyciu Azure Batch lub usługi Azure HDInsight. Dla tej połączonej usługi można uruchomić [działanie niestandardowe platformy .NET](#net-custom-activity) .

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji JSON usługi Azure Azure Batch połączonej usłudze.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna mieć wartość **AzureBatch**. |Tak |
| accountName |Nazwa konta Azure Batch. |Tak |
| accessKey |Klucz dostępu dla konta Azure Batch. |Tak |
| poolName |Nazwa puli maszyn wirtualnych. |Tak |
| linkedServiceName |Nazwa połączonej usługi Azure Storage skojarzonej z tą Azure Batch połączoną usługą. Ta połączona usługa jest używana w przypadku plików tymczasowych wymaganych do uruchomienia działania i przechowywania dzienników wykonywania działania. |Tak |


#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasyczny)
Utworzysz połączoną usługę Azure Machine Learning Studio (klasyczną) w celu zarejestrowania punktu końcowego oceniania partii programu Studio (klasycznego) przy użyciu fabryki danych. Dwie działania przekształcania danych, które można uruchomić w ramach tej połączonej usługi: [Azure Machine Learning Studio (klasyczny) działanie wykonywania wsadowego](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (klasyczne) aktualizowanie zasobów](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używanych w definicji JSON usługi połączonej programu Studio (klasycznej).

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Typ |Właściwość Type powinna mieć wartość: **Azure**. |Tak |
| mlEndpoint |Adres URL oceniania partii. |Tak |
| apiKey |Interfejs API opublikowanego modelu obszaru roboczego. |Tak |

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Należy utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługę obliczeniową Azure Data Lake Analytics z fabryką danych Azure przed użyciem [działania programu Data Lake Analytics U-SQL](data-factory-usql-activity.md) w potoku.

### <a name="linked-service"></a>Połączona usługa

Poniższa tabela zawiera opisy właściwości używanych w definicji JSON Azure Data Lake Analytics połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Typ |Właściwość Type powinna mieć wartość: **AzureDataLakeAnalytics**. |Tak |
| accountName |Nazwa konta Azure Data Lake Analytics. |Tak |
| dataLakeAnalyticsUri |Azure Data Lake Analytics identyfikator URI. |Nie |
| autoryzacja |Kod autoryzacji jest pobierany automatycznie po kliknięciu przycisku **Autoryzuj** w edytorze Data Factory i zakończeniu logowania OAuth. |Tak |
| subscriptionId |Identyfikator subskrypcji platformy Azure |Nie (jeśli nie zostanie określony, zostanie użyta subskrypcja fabryki danych). |
| resourceGroupName |Nazwa grupy zasobów platformy Azure |Nie (jeśli nie zostanie określony, zostanie użyta Grupa zasobów fabryki danych). |
| sessionId |Identyfikator sesji z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być użyty tylko raz. W przypadku korzystania z edytora Data Factory ten identyfikator jest generowany automatycznie. |Tak |


#### <a name="json-example"></a>Przykład JSON
Poniższy przykład zawiera definicję JSON dla Azure Data Lake Analytics połączonej usługi.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server procedury składowanej

Utworzysz połączoną usługę SQL Server i użyj jej z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) , aby wywołać procedurę składowaną z potoku Data Factory.

### <a name="linked-service"></a>Połączona usługa
Utworzysz połączoną usługę typu **OnPremisesSqlServer** , aby połączyć bazę danych SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna mieć wartość: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ informacje o connectionString, które są konieczne do nawiązania połączenia z bazą danych SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z bazą danych SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **nazwa_domeny \\ nazwa_użytkownika**. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować za pomocą polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (Właściwość **EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: kod JSON używany do uwierzytelniania SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: kod JSON używany do uwierzytelniania systemu Windows

Jeśli określono nazwę użytkownika i hasło, brama używa ich do personifikacji określonego konta użytkownika w celu nawiązania połączenia z bazą danych SQL Server. W przeciwnym razie Brama łączy się z SQL Server bezpośrednio z kontekstem zabezpieczeń bramy (jego konto uruchamiania).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [łącznika SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

## <a name="data-transformation-activities"></a>DZIAŁANIA PRZEKSZTAŁCANIA DANYCH

Działanie | Opis
-------- | -----------
[Działanie programu Hive w usłudze HDInsight](#hdinsight-hive-activity) | Działanie programu Hive w usłudze HDInsight w potoku Data Factory wykonuje zapytania Hive na własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux.
[Działanie dotyczące usługi HDInsight świni](#hdinsight-pig-activity) | Działanie świni w usłudze HDInsight w potoku Data Factory wykonuje zapytania dotyczące trzody chlewnej we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux.
[Działania technologii MapReduce w usłudze HDInsight](#hdinsight-mapreduce-activity) | Działanie MapReduce usługi HDInsight w potoku Data Factory wykonuje programy MapReduce na własnym lub na żądanie w klastrze HDInsight opartym na systemie Windows/Linux.
[Działania przesyłania strumieniowego usługi HDInsight](#hdinsight-streaming-activity) | Działanie przesyłania strumieniowego usługi HDInsight w potoku Data Factory wykonuje programy przesyłania strumieniowego Hadoop we własnym lub lokalnym klastrze usługi HDInsight opartym na systemie Windows/Linux.
[Działania platformy Spark w usłudze HDInsight](#hdinsight-spark-activity) | Działanie usługi HDInsight Spark w potoku Data Factory wykonuje programy platformy Spark w ramach własnego klastra usługi HDInsight.
[Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)](#azure-machine-learning-studio-classic-batch-execution-activity) | Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z usług sieci Web opublikowanych Studio (klasycznych) na potrzeby analizy predykcyjnej. Za pomocą działania wykonywania wsadowego w potoku Azure Data Factory można wywołać usługę sieci Web programu Studio (klasyczną), aby tworzyć prognozy dotyczące danych w usłudze Batch.
[Działanie aktualizacji zasobów w usłudze Azure Machine Learning Studio (wersja klasyczna)](#azure-machine-learning-studio-classic-update-resource-activity) | W miarę upływu czasu modele predykcyjne w Azure Machine Learning Studio (klasyczne) eksperymenty oceniania muszą być ponownie przenoszone przy użyciu nowych wejściowych zestawów danych. Po wykonaniu ponownych szkoleń, chcesz zaktualizować usługę oceniania przy użyciu modelu uczenia maszynowego. Możesz użyć działania Aktualizuj zasób, aby zaktualizować usługę sieci Web przy użyciu nowo nauczonego modelu.
[Działania procedur składowanych](#stored-procedure-activity) | Możesz użyć działania procedury składowanej w potoku Data Factory, aby wywołać procedurę składowaną w jednym z następujących magazynów danych: Azure SQL Database, Azure Synapse Analytics, SQL Server Database w przedsiębiorstwie lub maszynie wirtualnej platformy Azure.
[Data Lake Analytics działanie U-SQL](#data-lake-analytics-u-sql-activity) | Data Lake Analytics działanie U-SQL uruchamia skrypt U-SQL w klastrze Azure Data Lake Analytics.
[Niestandardowe działanie platformy .NET](#net-custom-activity) | Jeśli zachodzi potrzeba przekształcenia danych w sposób, który nie jest obsługiwany przez Data Factory, można utworzyć niestandardowe działanie z własną logiką przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klastra usługi Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Działania technologii Hive w usłudze HDInsight
W definicji JSON działania programu Hive można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **HDInsightHive**. Najpierw należy utworzyć połączoną usługę HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na HDInsightHive:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ skrypt Hive w tekście |Nie |
| ścieżka skryptu |Zapisz skrypt Hive w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości "Script" lub "scriptPath". Nie można jednocześnie używać obu tych metod. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| definiuje |Określ parametry jako pary klucz/wartość dla odwołania w skrypcie Hive przy użyciu elementu "hiveconf" |Nie |

Te właściwości typu są specyficzne dla działania programu Hive. Inne właściwości (poza sekcją typeProperties) są obsługiwane przez wszystkie działania.

### <a name="json-example"></a>Przykład JSON
Poniższy kod JSON definiuje działanie programu Hive w usłudze HDInsight w potoku.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [aktywności Hive](data-factory-hive-activity.md) .

## <a name="hdinsight-pig-activity"></a>Działania technologii Pig w usłudze HDInsight
W definicji JSON działania świni można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **HDInsightPig**. Najpierw należy utworzyć połączoną usługę HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na HDInsightPig:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ skrypt dla trzody chlewnej |Nie |
| ścieżka skryptu |Zapisz skrypt trzody chlewnej w usłudze Azure Blob Storage i podaj ścieżkę do pliku. Użyj właściwości "Script" lub "scriptPath". Nie można jednocześnie używać obu tych metod. W nazwie pliku rozróżniana jest wielkość liter. |Nie |
| definiuje |Określ parametry jako pary klucz/wartość do odwołania w skrypcie świni |Nie |

Te właściwości typu są specyficzne dla działania świni. Inne właściwości (poza sekcją typeProperties) są obsługiwane przez wszystkie działania.

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący działań związanych z świnią.

## <a name="hdinsight-mapreduce-activity"></a>Działania technologii MapReduce w usłudze HDInsight
W definicji JSON działania MapReduce można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **HDInsightMapReduce**. Najpierw należy utworzyć połączoną usługę HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na HDInsightMapReduce:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| jarLinkedService | Nazwa połączonej usługi dla usługi Azure Storage, która zawiera plik JAR. | Tak |
| jarFilePath | Ścieżka do pliku JAR w usłudze Azure Storage. | Tak |
| Nazwą | Nazwa klasy głównej w pliku JAR. | Tak |
| argumentu | Lista argumentów oddzielonych przecinkami dla programu MapReduce. W czasie wykonywania zobaczysz kilka dodatkowych argumentów (na przykład: MapReduce. job. Tags) ze środowiska MapReduce Framework. Aby odróżnić argumenty od argumentów MapReduce, należy rozważyć użycie obu opcji i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s,--Input,--Output itp., są opcjami natychmiast po ich wartości). | Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [aktywności MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Działania przesyłania strumieniowego usługi HDInsight
W definicji JSON działania przesyłania strumieniowego Hadoop można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **HDInsightStreaming**. Najpierw należy utworzyć połączoną usługę HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na HDInsightStreaming:

| Właściwość | Opis |
| --- | --- |
| wzor | Nazwa pliku wykonywalnego mapowania. W przykładzie cat.exe jest plikiem wykonywalnym mapowania.|
| zmniejszenie | Nazwa pliku wykonywalnego. W przykładzie wc.exe jest plikiem wykonywalnym. |
| wejście | Plik wejściowy (łącznie z lokalizacją) dla mapowania. W przykładzie: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` : adfsample jest kontenerem obiektów blob, przykładem/danymi/Gutenberg jest folder, a davinci.txt jest obiektem BLOB. |
| output | Plik wyjściowy (łącznie z lokalizacją) dla elementu zmniejszającego liczbę. Dane wyjściowe zadania przesyłania strumieniowego Hadoop są zapisywane w lokalizacji określonej dla tej właściwości. |
| Parametrze filePaths | Ścieżki dla plików wykonywalnych mapowania i redukcji. W przykładzie: "adfsample/example/Apps/wc.exe", adfsample jest kontenerem obiektów blob, przykładem/aplikacjami jest folder, a wc.exe jest plikiem wykonywalnym. |
| fileLinkedService | Połączona usługa Azure Storage, która reprezentuje magazyn platformy Azure, który zawiera pliki określone w sekcji filePaths. |
| argumentu | Lista argumentów oddzielonych przecinkami dla programu MapReduce. W czasie wykonywania zobaczysz kilka dodatkowych argumentów (na przykład: MapReduce. job. Tags) ze środowiska MapReduce Framework. Aby odróżnić argumenty od argumentów MapReduce, należy rozważyć użycie obu opcji i wartości jako argumentów, jak pokazano w poniższym przykładzie (-s,--Input,--Output itp., są opcjami natychmiast po ich wartości). |
| GetDebugInfo — | Opcjonalny element. Jeśli jest ustawiona na niepowodzenie, dzienniki są pobierane tylko w przypadku niepowodzenia. Po ustawieniu opcji wszystkie dzienniki są zawsze pobierane niezależnie od stanu wykonania. |

> [!NOTE]
> Należy określić wyjściowy zestaw danych dla działania Hadoop Streaming **dla właściwości** Outputs. Ten zestaw danych może być tylko fikcyjnym zestawem danych, który jest wymagany do kierowania harmonogramu potoku (co godzinę, codziennie itd.). Jeśli działanie nie przyjmuje danych wejściowych, można pominąć Określanie wejściowego zestawu danych dla działania dla właściwości **dane wejściowe** .

## <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
W definicji JSON działania Spark można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **HDInsightSpark**. Najpierw należy utworzyć połączoną usługę HDInsight i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na HDInsightSpark:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| Właściwość RootPath | Kontener i folder obiektów blob platformy Azure, który zawiera plik Spark. W nazwie pliku rozróżniana jest wielkość liter. | Tak |
| entryFilePath | Ścieżka względna do folderu głównego kodu/pakietu platformy Spark. | Tak |
| Nazwą | Główna Klasa środowiska Java/Spark aplikacji | Nie |
| argumentu | Lista argumentów wiersza polecenia do programu Spark. | Nie |
| proxyUser | Konto użytkownika służące do personifikacji w celu wykonania programu Spark | Nie |
| sparkConfig | Właściwości konfiguracji platformy Spark. | Nie |
| GetDebugInfo — | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu platformy Azure używanego przez klaster usługi HDInsight (lub) określonego przez sparkJobLinkedService. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie |
| sparkJobLinkedService | Połączona usługa Azure Storage, która przechowuje plik zadania platformy Spark, zależności i dzienniki.  Jeśli nie określisz wartości tej właściwości, zostanie użyty magazyn skojarzony z klastrem usługi HDInsight. | Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Pamiętaj o następujących kwestiach:

- Właściwość **Type** jest ustawiona na wartość **HDInsightSpark**.
- **Właściwość RootPath** jest ustawiona na **adfspark \\ pyFiles** , gdzie Adfspark jest kontenerem obiektów blob platformy Azure, a pyFiles jest cienki folder w tym kontenerze. W tym przykładzie Blob Storage platformy Azure to ta, która jest skojarzona z klastrem Spark. Plik można przekazać do innej usługi Azure Storage. W takim przypadku należy utworzyć połączoną usługę Azure Storage w celu połączenia tego konta magazynu z fabryką danych. Następnie określ nazwę połączonej usługi jako wartość właściwości **sparkJobLinkedService** . Zobacz właściwości działania platformy Spark, aby uzyskać szczegółowe informacje o tej właściwości i innych właściwościach obsługiwanych przez działanie platformy Spark.
- **EntryFilePath** jest ustawiona na **test.py**, który jest plikiem języka Python.
- Właściwość **GetDebugInfo —** jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowane (sukces lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, aby nie ustawiać tej właściwości na zawsze w środowisku produkcyjnym, chyba że rozwiązywanie problemu nie jest możliwe.
- Sekcja **outputs** Outputs ma jeden wyjściowy zestaw danych. Musisz określić wyjściowy zestaw danych, nawet jeśli program Spark nie wygenerował żadnych danych wyjściowych. Wyjściowy zestaw danych steruje harmonogramem potoku (co godzinę, codziennie itd.).

Aby uzyskać więcej informacji na temat działania, zobacz artykuł dotyczący [aktywności platformy Spark](data-factory-spark.md) .

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)
Można określić następujące właściwości w definicji JSON działania wykonywania wsadowego w Azure Machine Learning Studio (klasyczną). Właściwość Type dla działania musi mieć wartość: **AzureMLBatchExecution**. Musisz najpierw utworzyć połączoną usługę programu Studio (klasyczną) i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na AzureMLBatchExecution:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
webServiceInput | Zestaw danych, który ma zostać przesłany jako dane wejściowe dla usługi sieci Web programu Studio (klasycznej). Ten zestaw danych musi być również uwzględniony w danych wejściowych dla działania. |Użyj opcji webServiceInput lub webServiceInputs. |
webServiceInputs | Określ zestawy danych, które mają być przesyłane jako dane wejściowe dla usługi sieci Web Studio (klasyczna). Jeśli usługa sieci Web przyjmuje wiele danych wejściowych, użyj właściwości webServiceInputs zamiast właściwości webServiceInput. Zestawy danych, do których odwołuje się **webServiceInputs** , muszą być również zawarte w **danych wejściowych** działania. | Użyj opcji webServiceInput lub webServiceInputs. |
webServiceOutputs | Zestawy danych, które są przypisane jako dane wyjściowe dla usługi sieci Web Studio (klasyczna). Usługa sieci Web zwraca dane wyjściowe z tego zestawu danych. | Tak |
globalParameters | Określ wartości parametrów usługi sieci Web w tej sekcji. | Nie |

### <a name="json-example"></a>Przykład JSON
W tym przykładzie działanie ma zestaw danych **MLSqlInput** jako dane wejściowe i **MLSqlOutput** jako dane wyjściowe. **MLSqlInput** jest przenoszona jako dane wejściowe do usługi sieci Web przy użyciu właściwości JSON **webServiceInput** . **MLSqlOutput** jest przekazywane jako dane wyjściowe do usługi sieci Web przy użyciu właściwości JSON **webServiceOutputs** .

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

W przykładzie JSON wdrożona usługa sieci Web programu Studio (klasyczna) używa czytnika i modułu składnika zapisywania do odczytu/zapisu danych z/do Azure SQL Database. Ta usługa sieci Web udostępnia następujące cztery parametry: Nazwa serwera bazy danych, nazwa bazy danych, nazwa konta użytkownika serwera i hasło konta użytkownika serwera.

> [!NOTE]
> Tylko dane wejściowe i wyjściowe działania AzureMLBatchExecution mogą być przesyłane jako parametry do usługi sieci Web. Na przykład w powyższym fragmencie kodu JSON MLSqlInput to dane wejściowe działania AzureMLBatchExecution, które są przesyłane jako dane wejściowe do usługi sieci Web za pośrednictwem parametru webServiceInput.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Działanie aktualizacji zasobów w usłudze Azure Machine Learning Studio (wersja klasyczna)
Można określić następujące właściwości w definicji JSON dotyczącej aktualizacji zasobów Azure Machine Learning Studio (klasycznej). Właściwość Type dla działania musi mieć wartość: **AzureMLUpdateResource**. Musisz najpierw utworzyć połączoną usługę programu Studio (klasyczną) i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na AzureMLUpdateResource:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
trainedModelName | Nazwa modelu, który został przemieszczony ponownie. | Tak |
trainedModelDatasetName | Zestaw danych wskazujący plik iLearner zwracany przez operację ponownego szkolenia. | Tak |

### <a name="json-example"></a>Przykład JSON
Potok ma dwie działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego programu Studio (klasycznej) pobiera dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje szkoleniową usługę sieci Web (eksperyment szkoleniowy ujawniony jako usługa sieci Web) z danymi szkolenia danych wejściowych i odbiera plik ilearner z usługi WebService. PlaceholderBlob to tylko fikcyjny wyjściowy zestaw danych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Można określić następujące właściwości w definicji JSON działania U-SQL. Właściwość Type dla działania musi być: **DataLakeAnalyticsU-SQL**. Należy utworzyć połączoną usługę Azure Data Lake Analytics i określić jej nazwę jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na DATALAKEANALYTICSU-SQL:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| scriptPath |Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. |Nie (Jeśli używasz skryptu) |
| Elementu scriptlinkedservice |Połączona usługa, która łączy magazyn zawierający skrypt z fabryką danych |Nie (Jeśli używasz skryptu) |
| skrypt |Określ skrypt wbudowany zamiast określania scriptPath i elementu scriptlinkedservice. Na przykład: "skrypt": "CREATE DATABASE test". |Nie (Jeśli używasz scriptPath i elementu scriptlinkedservice) |
| degreeOfParallelism |Maksymalna liczba węzłów jednocześnie używanych do uruchomienia zadania. |Nie |
| priority |Określa, które zadania z wszystkich znajdujących się w kolejce powinny zostać wybrane do uruchomienia jako pierwsze. Im niższa wartość, tym wyższy priorytet. |Nie |
| parameters |Parametry skryptu U-SQL |Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz [Data Lake Analytics działania U-SQL](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Działania procedur składowanych
W definicji JSON działania procedury składowanej można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **SqlServerStoredProcedure**. Należy utworzyć jedną z następujących połączonych usług i określić nazwę połączonej usługi jako wartość właściwości **linkedServiceName** :

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na SqlServerStoredProcedure:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| storedProcedureName |Określ nazwę procedury składowanej w Azure SQL Database lub analizy Synapse Azure, która jest reprezentowana przez połączoną usługę używaną przez tabelę wyjściową. |Tak |
| storedProcedureParameters |Określ wartości parametrów procedury składowanej. Jeśli musisz przekazać wartość null dla parametru, użyj składni: "param1": null (wszystkie małe litery). Zapoznaj się z poniższym przykładem, aby dowiedzieć się więcej o używaniu tej właściwości. |Nie |

Jeśli określisz wejściowy zestaw danych, musi on być dostępny (w stanie "gotowe") do uruchomienia działania procedury składowanej. Wejściowy zestaw danych nie może być używany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. Należy określić wyjściowy zestaw danych dla działania procedury składowanej.

Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc itd.). Wyjściowy zestaw danych musi używać **połączonej usługi** , która odwołuje się do Azure SQL Database lub analizy usługi Azure Synapse lub bazy danych SQL Server, w której ma zostać uruchomiona procedura składowana. Wyjściowy zestaw danych może stanowić sposób przekazania wyniku procedury składowanej w celu późniejszego przetwarzania przez inne działanie ([łączenie łańcucha](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) w potoku. Jednak Data Factory nie zapisuje automatycznie danych wyjściowych procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje w tabeli SQL, do której wskazuje wyjściowy zestaw danych. W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych**, który jest używany tylko do określenia harmonogramu uruchamiania działania procedury składowanej.

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł [działania procedury składowanej](data-factory-stored-proc-activity.md) .

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
W definicji JSON niestandardowego działania programu .NET można określić następujące właściwości. Właściwość Type dla działania musi mieć wartość: **dotnet**. Musisz utworzyć połączoną usługę Azure HDInsight lub połączoną usługę Azure Batch i określić nazwę połączonej usługi jako wartość właściwości **linkedServiceName** . Następujące właściwości są obsługiwane w sekcji **typeProperties** podczas ustawiania typu działania na działanie dotnet:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| AssemblyName | Nazwa zestawu. W tym przykładzie jest: **MyDotnetActivity.dll**. | Tak |
| EntryPoint |Nazwa klasy implementującej interfejs IDotNetActivity. W tym przykładzie jest to: **MyDotNetActivityNS...** MyDotNetActivityNS jest przestrzenią nazw i jest klasą.  | Tak |
| PackageLinkedService | Nazwa połączonej usługi Azure Storage, która wskazuje magazyn obiektów blob, który zawiera plik zip działania niestandardowego. W tym przykładzie jest to: **AzureStorageLinkedService**.| Tak |
| PackageFile | Nazwa pliku zip. W tym przykładzie jest to: **customactivitycontainer/MyDotNetActivity.zip**. | Tak |
| Właściwości ExtendedProperties | Rozszerzone właściwości, które można definiować i przekazywać do kodu platformy .NET. W tym przykładzie zmienna **parametru slicestart** jest ustawiona na wartość na podstawie zmiennej systemowej parametru slicestart. | Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Aby uzyskać szczegółowe informacje, zobacz [Korzystanie z działań niestandardowych w Data Factory](data-factory-use-custom-activities.md) artykule.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki:

- [Samouczek: Tworzenie potoku za pomocą działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Samouczek: Tworzenie potoku za pomocą działania programu Hive](data-factory-build-your-first-pipeline.md)