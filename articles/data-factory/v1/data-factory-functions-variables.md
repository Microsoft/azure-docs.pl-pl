---
title: Data Factory funkcje i zmienne systemowe
description: Zawiera listę funkcji Azure Data Factory i zmiennych systemowych
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786434"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory — funkcje i zmienne systemowe
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [zmienne systemowe w Data Factory](../control-flow-system-variables.md).

Ten artykuł zawiera informacje o funkcjach i zmiennych obsługiwanych przez Azure Data Factory.

## <a name="data-factory-system-variables"></a>Zmienne systemowe Data Factory

| Nazwa zmiennej | Opis | Zakres obiektów | Zakres i przypadki użycia JSON |
| --- | --- | --- | --- |
| WindowStart |Początek interwału czasu dla bieżącego okna uruchomienia działania |aktywność |<ol><li>Określ zapytania dotyczące wyboru danych. Zobacz artykuły dotyczące łączników, do których odwołuje się artykuł [działania przenoszenia danych](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Koniec interwału bieżącego okna uruchamiania działania |aktywność |Analogicznie jak WindowStart. |
| Parametru slicestart |Początek przedziału czasu dla generowanego wycinka danych |aktywność<br/>zestawu |<ol><li>Określ dynamiczne ścieżki folderów i nazwy plików podczas pracy z [zestawami danych](data-factory-onprem-file-system-connector.md) [Azure Blob](data-factory-azure-blob-connector.md) i system plików.</li><li>Określ zależności wejściowe z funkcjami fabryki danych w kolekcji danych wejściowych działania.</li></ol> |
| SliceEnd |Koniec interwału dla bieżącego wycinka danych. |aktywność<br/>zestawu |Analogicznie jak parametru slicestart. |

> [!NOTE]
> Obecnie Fabryka danych wymaga, aby harmonogram określony w działaniu był dokładnie zgodny z harmonogramem określonym w temacie dostępność wyjściowego zestawu danych. W związku z tym WindowStart, WindowEnd i parametru slicestart i SliceEnd zawsze są mapowane na ten sam okres i jeden wycinek wyjściowy.
> 

### <a name="example-for-using-a-system-variable"></a>Przykład użycia zmiennej systemowej
W poniższym przykładzie rok, miesiąc, dzień i czas **parametru slicestart** są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości **folderPath** i **filename** .

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funkcje Data Factory
Można używać funkcji w usłudze Fabryka danych wraz z zmiennymi systemowymi do następujących celów:

1. Określanie kwerend wyboru danych (zobacz artykuły dotyczące łączników, do których odwołuje się artykuł dotyczący [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md) .
   
   Składnia służąca do wywoływania funkcji fabryki danych to: **$$\<function>** w przypadku zapytań wyboru danych i innych właściwości w działaniu i zbiorach.  
2. Określanie zależności wejściowych z funkcjami fabryki danych w kolekcji danych wejściowych działania.
   
    $ $ nie jest wymagany do określenia wejściowych wyrażeń zależności.     

W poniższym przykładzie właściwość **sqlReaderQuery** w pliku JSON jest przypisana do wartości zwracanej przez `Text.Format` funkcję. Ten przykład używa również zmiennej systemowej o nazwie **WindowStart**, która reprezentuje godzinę rozpoczęcia okna uruchomienia działania.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zobacz temat [Niestandardowe ciągi formatujące datę i godzinę](/dotnet/standard/base-types/custom-date-and-time-format-strings) , które opisują różne opcje formatowania, których można użyć (na przykład: AY a rrrr). 

### <a name="functions"></a>Funkcje
W poniższej tabeli wymieniono wszystkie funkcje w Azure Data Factory:

| Kategoria | Funkcja | Parametry | Opis |
| --- | --- | --- | --- |
| Godzina |Addgodz. (X, Y) |X: DateTime <br/><br/>Y: int |Dodaje Y godz. do danego czasu X. <br/><br/>Przykład: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Godzina |Addminut (X, Y) |X: DateTime <br/><br/>Y: int |Dodaje Y minuty do X.<br/><br/>Przykład: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Godzina |Początek godziny (X) |X: DateTime |Pobiera godzinę rozpoczęcia dla godziny reprezentowanej przez składnik godziny X. <br/><br/>Przykład: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date (Data) |AddDays (X, Y) |X: DateTime<br/><br/>Y: int |Dodaje Y dni do X. <br/><br/>Przykład: 9/15/2013 12:00:00 PM + 2 dni = 9/17/2013 12:00:00 PM.<br/><br/>Liczbę dni można odjąć za pomocą wartości Y jako liczby ujemnej.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date (Data) |Addmiesiącach (X, Y) |X: DateTime<br/><br/>Y: int |Dodaje Y miesiące do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Za pomocą wartości Y można odjąć liczbę miesięcy.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date (Data) |Addćwiartki (X, Y) |X: DateTime <br/><br/>Y: int |Dodaje Y * 3 miesiące do X.<br/><br/>Przykład: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date (Data) |Addtygodnie (X, Y) |X: DateTime<br/><br/>Y: int |Dodaje Y * 7 dni do X<br/><br/>Przykład: 9/15/2013 12:00:00 PM + 1 tydzień = 9/22/2013 12:00:00 PM<br/><br/>Można odjąć tygodnie zbyt, określając wartość Y jako liczbę ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date (Data) |AddYears (X, Y) |X: DateTime<br/><br/>Y: int |Dodaje Y lata do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Za pomocą wartości Y można odjąć liczbę lat.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date (Data) |Dzień (X) |X: DateTime |Pobiera składnik Day X.<br/><br/>Przykład: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date (Data) |DayOfWeek (X) |X: DateTime |Pobiera składnik dnia tygodnia X.<br/><br/>Przykład: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date (Data) |Dzieńroku (X) |X: DateTime |Pobiera dzień w roku reprezentowany przez składnik Year X.<br/><br/>Przykłady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date (Data) |DaysInMonth (X) |X: DateTime |Pobiera dni w miesiącu reprezentowane przez składnik miesiąca parametru X.<br/><br/>Przykład: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date (Data) |EndOfDay (X) |X: DateTime |Pobiera datę i godzinę reprezentującą koniec dnia (składnik dnia) X.<br/><br/>Przykład: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date (Data) |EndOfMonth (X) |X: DateTime |Pobiera koniec miesiąca reprezentowanego przez składnik miesiąca parametru X. <br/><br/>Przykład: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (Data i godzina reprezentująca koniec miesiąca z września) |
| Date (Data) |StartOfDay (X) |X: DateTime |Pobiera początek dnia reprezentowanego przez składnik dnia parametru X.<br/><br/>Przykład: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Od (X) |X: ciąg |Przeanalizuj ciąg X do daty i godziny. |
| DateTime |Takty (X) |X: DateTime |Pobiera właściwość Ticks parametru X. Jeden takt jest równy 100 nanosekund. Wartość tej właściwości reprezentuje liczbę taktów, które upłynęły od 12:00:00 północy, 1 stycznia 0001. |
| Tekst |Format (X) |X: zmienna ciągu |Formatuje tekst (Użyj `\\'` kombinacji do znaku ucieczki `'` ).|

> [!IMPORTANT]
> W przypadku korzystania z funkcji w innej funkcji nie trzeba używać **$$** prefiksu dla wewnętrznej funkcji. Na przykład: $ $Text. Format ("PartitionKey EQ \\ " my_pkey_filter_value \\ "i RowKey GE \\ " {0: RRRR-MM-DD GG: mm: SS} \\ "", Time. AddHours (parametru slicestart,-6)). W tym przykładzie należy zauważyć, że **$$** prefiks nie jest używany przez funkcję **Time. AddHours** . 

#### <a name="example"></a>Przykład
W poniższym przykładzie parametry wejściowe i wyjściowe dla działania programu Hive są określane przy użyciu `Text.Format` funkcji i zmiennej systemowej parametru slicestart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Przykład 2

W poniższym przykładzie parametr DateTime dla działania procedury składowanej jest określany za pomocą tekstu. Funkcja format i zmienna parametru slicestart. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Przykład 3
Aby odczytać dane z poprzedniego dnia zamiast dnia reprezentowanego przez parametru slicestart, użyj funkcji addDays, jak pokazano w następującym przykładzie: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Zapoznaj się z tematami [niestandardowego formatu daty i godziny](/dotnet/standard/base-types/custom-date-and-time-format-strings) , który opisuje różne opcje formatowania, których można użyć (na przykład: yy a rrrr).