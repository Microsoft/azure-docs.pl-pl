---
title: Planowanie i wykonywanie przy użyciu Data Factory
description: Poznaj aspekty planowania i wykonywania Azure Data Factory modelu aplikacji.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780008"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory planowanie i wykonywanie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz artykuł [wykonywanie potoku i wyzwalacze](../concepts-pipeline-execution-triggers.md) .

W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory. W tym artykule założono, że rozumiesz podstawowe informacje dotyczące Data Factory pojęć modelu aplikacji, w tym działań, potoków, połączonych usług i zestawów danych. Podstawowe pojęcia dotyczące Azure Data Factory można znaleźć w następujących artykułach:

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Zestawy danych](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Czasy rozpoczęcia i zakończenia potoku
Potok jest aktywny tylko między jego czasem **rozpoczęcia** i czasem **zakończenia** . Nie jest wykonywane przed czasem rozpoczęcia lub po zakończeniu. Jeśli potok jest wstrzymany, nie jest wykonywany niezależnie od jego czasu rozpoczęcia i zakończenia. Do uruchomienia potoku nie należy wstrzymywać. Te ustawienia można znaleźć w definicji potoku: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Aby uzyskać więcej informacji o tych właściwościach, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . 


## <a name="specify-schedule-for-an-activity"></a>Określ harmonogram działania
Nie jest to potok, który jest wykonywany. Są to działania w potoku, które są wykonywane w ogólnym kontekście potoku. Możesz określić harmonogram cykliczny dla działania przy użyciu sekcji **Scheduler** w formacie JSON działania. Na przykład można zaplanować działanie do uruchomienia co godzinę w następujący sposób:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak pokazano na poniższym diagramie, Określanie harmonogramu dla działania powoduje utworzenie serii okien wirowania z w czasie rozpoczęcia i zakończenia potoku. Wirowania systemu Windows to seria nienakładających się na stałe przedziałów czasu. Te logiczne wirowania okna dla działania są nazywane **oknami aktywności**.

![Przykład harmonogramu działań](media/data-factory-scheduling-and-execution/scheduler-example.png)

Właściwość **Scheduler** dla działania jest opcjonalna. Jeśli określisz tę właściwość, musi ona być zgodna z erzeą określoną w definicji wyjściowego zestawu danych dla działania. Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. W związku z tym należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie wygenerowało żadnych danych wyjściowych. 

## <a name="specify-schedule-for-a-dataset"></a>Określ harmonogram dla zestawu danych
Działanie w potoku Data Factory może pobrać zero lub więcej wejściowych **zestawów** danych i utworzyć co najmniej jeden wyjściowy zestaw danych. Dla działania można określić erze, w którym dane wejściowe są dostępne lub dane wyjściowe są generowane przy użyciu sekcji **dostępność** w definicjach zestawu danych. 

**Częstotliwość** w sekcji **dostępność** określa jednostkę czasu. Dozwolone wartości dla częstotliwości to: minuta, godzina, dzień, tydzień i miesiąc. Właściwość **Interval** w sekcji Availability określa mnożnik dla częstotliwości. Na przykład: Jeśli częstotliwość jest ustawiona na dzień, a interwał jest ustawiony na 1 dla wyjściowego zestawu danych, dane wyjściowe są tworzone codziennie. Jeśli określisz częstotliwość jako minutowa, zalecamy ustawienie interwału na wartość nie mniejszą niż 15. 

W poniższym przykładzie dane wejściowe są dostępne co godzinę, a dane wyjściowe są generowane co godzinę ( `"frequency": "Hour", "interval": 1` ). 

**Wejściowy zestaw danych:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Wyjściowy zestaw danych**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Obecnie **wyjściowy zestaw danych steruje harmonogramem**. Innymi słowy, harmonogram określony dla wyjściowego zestawu danych jest używany do uruchamiania działania w czasie wykonywania. W związku z tym należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie wygenerowało żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. 

W poniższej definicji potoku Właściwość **Scheduler** służy do określenia harmonogramu dla działania. Ta właściwość jest opcjonalna. Obecnie harmonogram działania musi być zgodny z harmonogramem określonym dla wyjściowego zestawu danych.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

W tym przykładzie działanie jest uruchamiane co godzinę między godzinami rozpoczęcia i zakończenia potoku. Dane wyjściowe są generowane co godzinę dla trzech godzin (8 AM-9 AM, 9 am-10 am i 10 AM-11 AM). 

Każda jednostka danych zużywana lub generowana przez uruchomienie działania jest nazywana **wycinkem danych**. Na poniższym diagramie przedstawiono przykład działania z jednym wejściowym zestawem danych i jednym wyjściowym zestawem danych: 

![Harmonogram dostępności](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Na diagramie przedstawiono wyjściowe wycinki danych dla wejściowego i wyjściowego zestawu danych. Diagram przedstawia trzy wycinki wejściowe, które są gotowe do przetworzenia. Działanie AM 10-11 jest w toku, generując wycinek danych wyjściowych 10-11. 

Możesz uzyskać dostęp do interwału czasu skojarzonego z bieżącym wycinkem w pliku JSON zestawu danych, używając zmiennych: [parametru slicestart](data-factory-functions-variables.md#data-factory-system-variables) i [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobnie można uzyskać dostęp do interwału czasu skojarzonego z oknem działania przy użyciu WindowStart i WindowEnd. Harmonogram działania musi być zgodny z harmonogramem wyjściowego zestawu danych dla działania. W związku z tym wartości parametru slicestart i SliceEnd są takie same jak wartości WindowStart i WindowEnd. Aby uzyskać więcej informacji na temat tych zmiennych, zobacz [Data Factory funkcje i artykuły zmienne systemowe](data-factory-functions-variables.md#data-factory-system-variables) .  

Możesz użyć tych zmiennych do różnych celów w formacie JSON działania. Można na przykład użyć ich do wybrania danych z wejściowych i wyjściowych zestawów danych reprezentujących dane szeregów czasowych (na przykład: 8 AM do 9 AM). W tym przykładzie używa się także **WindowStart** i **WindowEnd** , aby wybrać odpowiednie dane dla działania uruchomienia i skopiować je do obiektu BLOB przy użyciu odpowiedniego **folderPath**. **FolderPath** jest sparametryzowane do oddzielnego folderu dla każdej godziny.  

W poprzednim przykładzie harmonogram określony dla wejściowych i wyjściowych zestawów danych jest taki sam (co godzinę). Jeśli wejściowy zestaw danych dla działania jest dostępny z inną częstotliwością, powiedz co 15 minut, działanie, które generuje ten wyjściowy zestaw danych, jest nadal uruchamiane raz na godzinę, ponieważ wyjściowy zestaw danych wskazuje na dysk harmonogramu działania. Aby uzyskać więcej informacji, zobacz [zestawy danych modelu z różnymi częstotliwościami](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostępność i zasady zestawu danych
Zaobserwowano użycie właściwości częstotliwości i interwału w sekcji dostępność definicji zestawu danych. Istnieje kilka innych właściwości, które wpływają na planowanie i wykonywanie działania. 

### <a name="dataset-availability"></a>Dostępność zestawu danych 
W poniższej tabeli opisano właściwości, których można użyć w sekcji **dostępność** :

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla produkcji wycinków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuta, godzina, dzień, tydzień, miesiąc |Tak |NA |
| interval |Określa mnożnik dla częstotliwości<br/><br/>"Interwał x częstotliwości" określa, jak często wycinek jest generowany.<br/><br/>Jeśli potrzebujesz zestawu danych, który ma być pofragmentowany co godzinę, ustawisz <b>częstotliwość</b> na <b>godzinę</b>, a <b>Interwał</b> na <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli określisz częstotliwość jako minutę, zalecamy ustawienie interwału na wartość nie mniejszą niż 15. |Tak |NA |
| styl |Określa, czy wycinek ma być tworzony na początku, czy na końcu interwału.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli częstotliwość jest ustawiona na wartość miesiąc, a w polu styl ustawiono wartość EndOfInterval, wycinek zostanie utworzony w ostatnim dniu miesiąca. Jeśli styl jest ustawiony na StartOfInterval, wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli częstotliwość jest ustawiona na dzień, a styl ustawiono na EndOfInterval, wycinek jest tworzony w ciągu ostatniej godziny dnia.<br/><br/>Jeśli częstotliwość jest ustawiona na wartość Godzina i styl ustawiono na EndOfInterval, wycinek zostanie utworzony na końcu godziny. Na przykład dla wycinka dla 1 PM — 2 PM, wycinek jest generowany na 2 PM. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanym przez harmonogram do obliczania granic wycinków zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowe niż częstotliwość, są ignorowane części bardziej szczegółowe. <br/><br/>Jeśli na przykład <b>Interwał</b> ma wartość <b>co godzinę</b> (częstotliwość: godzina i interwał: 1), a <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b>, wówczas części <b>minut i sekund</b> AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Przedział czasu, przez który początek i koniec wszystkich wycinków zestawu danych są przesunięte. <br/><br/><b>Uwaga</b>: Jeśli określono zarówno anchorDateTime, jak i przesunięcie, wynik jest połączonym przesunięciem. |Nie |NA |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie dzienne `"frequency": "Day", "interval": 1` wycinki () zaczynają się od 12 czasu UTC (północy). Jeśli chcesz, aby godzina rozpoczęcia była 6 czasem UTC, Ustaw przesunięcie, tak jak pokazano w poniższym fragmencie kodu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest generowany co 23 godziny. Pierwszy wycink rozpocznie się o czas określony przez anchorDateTime, który jest ustawiony na `2017-04-19T08:00:00` (czas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Przykład przesunięcia/stylu
Następujący zestaw danych jest miesięcznym zestawem danych i jest tworzony z 3. każdy miesiąc o 8:00 AM ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zasady zestawu danych
Zestaw danych może mieć zdefiniowane zasady sprawdzania poprawności, które określają, jak dane generowane przez wykonywanie wycinków mogą zostać sprawdzone, zanim będzie gotowe do użycia. W takich przypadkach po zakończeniu wykonywania wycinka stan wycinka danych wyjściowych zostanie zmieniony w celu **oczekiwania** na podstan **walidacji**. Po zweryfikowaniu wycinków stan wycinka zmieni się na **gotowe**. Jeśli wycinek danych został utworzony, ale nie przeszedł walidacji, uruchomienia działań dla wycinków podrzędnych, które są zależne od tego wycinka, nie są przetwarzane. [Potoki Monitoruj i zarządzaj nimi](data-factory-monitor-manage-pipelines.md) obejmują różne stany wycinków danych w Data Factory.

Sekcja **zasady** w definicji zestawu danych definiuje kryteria lub warunek, który musi spełniać wycinki zestawu danych. W poniższej tabeli opisano właściwości, których można użyć w sekcji **zasad** :

| Policy Name (Nazwa zasad) | Opis | Zastosowane do | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Sprawdza, czy dane w **obiekcie blob platformy Azure** spełniają minimalne wymagania dotyczące rozmiaru (w megabajtach). |Obiekt bob Azure |Nie |NA |
| minimumRows | Sprawdza, czy dane w **Azure SQL Database** lub w **tabeli platformy Azure** zawierają minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |NA |

#### <a name="examples"></a>Przykłady
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Aby uzyskać więcej informacji o tych właściwościach i przykładach, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . 

## <a name="activity-policies"></a>Zasady dotyczące działań
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

Aby uzyskać więcej informacji, zobacz artykuł [potoki](data-factory-create-pipelines.md) . 

## <a name="parallel-processing-of-data-slices"></a>Równoległe przetwarzanie wycinków danych
Możesz ustawić datę początkową potoku w przeszłości. Gdy to zrobisz, Data Factory automatycznie oblicza (cofa) wszystkie wycinki danych w przeszłości i zacznie je przetwarzać. Na przykład: Jeśli utworzysz potok z datą rozpoczęcia 2017-04-01, a bieżąca data to 2017-04-10. Jeśli erze wyjściowego zestawu danych jest codziennie, Data Factory rozpoczyna przetwarzanie wszystkich wycinków od 2017-04-01 do 2017-04-09, ponieważ Data rozpoczęcia przypada w przeszłości. Wycinek z 2017-04-10 nie został jeszcze przetworzony, ponieważ wartość właściwości style w sekcji dostępność jest domyślnie EndOfInterval. Najstarszy wycink jest przetwarzany jako pierwszy, ponieważ domyślna wartość executionPriorityOrder to OldestFirst. Aby uzyskać opis właściwości style, zobacz sekcję [dostępność zestawu danych](#dataset-availability) . Opis sekcji executionPriorityOrder można znaleźć w sekcji [zasady dotyczące działań](#activity-policies) . 

Wycinki danych z tyłu można skonfigurować tak, aby były przetwarzane równolegle przez ustawienie właściwości **współbieżności** w sekcji **zasady** w formacie JSON działania. Ta właściwość określa liczbę równoległych wykonań działań, które mogą być wykonywane na różnych wycinkach. Wartość domyślna właściwości współbieżności to 1. W związku z tym jeden wycinek jest przetwarzany domyślnie. Wartość maksymalna to 10. Gdy potok musi przechodzić przez duży zestaw dostępnych danych, dzięki czemu większa wartość współbieżności przyspiesza przetwarzanie danych. 

## <a name="rerun-a-failed-data-slice"></a>Ponowne uruchamianie wycinka danych zakończonych niepowodzeniem
Gdy wystąpi błąd podczas przetwarzania wycinka danych, można dowiedzieć się, dlaczego przetwarzanie wycinka zakończyło się niepowodzeniem za pomocą Azure Portalych lub monitorować aplikacje oraz zarządzać nimi. Aby uzyskać szczegółowe informacje [, zobacz Monitorowanie potoków i zarządzanie nimi za pomocą Azure Portalych](data-factory-monitor-manage-pipelines.md) lub [aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md) .

Rozważmy poniższy przykład, który pokazuje dwie działania. Zakończeniu i działanie 2. Zakończeniu zużywa wycinka pozycję DataSet1 i tworzy wycinek Dataset2, który jest używany jako dane wejściowe przez Activity2 w celu utworzenia wycinka końcowego zestawu danych.

![Plasterek zakończonych niepowodzeniem](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram pokazuje, że z trzech ostatnich wycinków Wystąpił błąd podczas tworzenia wycinka 9-10 AM dla Dataset2. Data Factory automatycznie śledzi zależność dla zestawu danych szeregów czasowych. W związku z tym nie uruchamia uruchomienia działania dla wycinka 9-10 AM.

Data Factory narzędzia do monitorowania i zarządzania umożliwiają przejście do dzienników diagnostycznych wycinków uszkodzonych w celu łatwego znalezienia głównej przyczyny problemu i jego rozwiązania. Po rozwiązaniu problemu można łatwo uruchomić uruchomienie działania w celu utworzenia wycinka uszkodzonego. Aby uzyskać więcej informacji o tym, jak ponownie uruchomić i zrozumieć przejścia stanu dla wycinków danych, zobacz [monitorowanie potoków i zarządzanie nimi przy użyciu Azure Portal](data-factory-monitor-manage-pipelines.md) [aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md).

Po ponownym uruchomieniu wycinka 9-10 AM dla **Dataset2**, Data Factory uruchamia uruchomienie dla wycinka zależnego 9-10 w końcowym zestawie danych.

![Ponowne uruchamianie wycinka zakończonego niepowodzeniem](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Potok może obejmować więcej niż jedno działanie. Jeśli masz wiele działań w potoku, a dane wyjściowe działania nie są danymi wejściowymi innego działania, działania mogą działać równolegle, jeśli wycinki danych wejściowych dla działań są gotowe.

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Działania mogą znajdować się w tym samym potoku lub w różnych potokach. Drugie działanie jest wykonywane tylko wtedy, gdy pierwszy z nich zakończy się pomyślnie.

Rozważmy na przykład następujące przypadki, w których potok ma dwie działania:

1. Działanie a1, które wymaga zewnętrznego wejściowego zestawu danych D1 i tworzy wyjściowy zestaw danych D2.
2. Działanie a2, które wymaga danych wejściowych z zestawu danych D2 i tworzy wyjściowy zestaw danych D3.

W tym scenariuszu działania a1 i a2 znajdują się w tym samym potoku. Działanie a1 działa, gdy dane zewnętrzne są dostępne i osiągnięto częstotliwość zaplanowanej dostępności. Działanie a2 działa, gdy planowane wycinki z D2 staną się dostępne i osiągnięto częstotliwość zaplanowanej dostępności. Jeśli wystąpi błąd w jednym z wycinków w zestawie danych D2, a2 nie zostanie uruchomiony dla tego wycinka, dopóki nie będzie dostępny.

Widok diagramu z obiema działaniami w tym samym potoku będzie wyglądać podobnie jak na poniższym diagramie:

![Łączenie działań w tym samym potoku](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak wspomniano wcześniej, działania mogą znajdować się w różnych potokach. W takim scenariuszu widok diagramu będzie wyglądał jak na poniższym diagramie:

![Łączenie działań w dwóch potokach](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zapoznaj się z sekcją Kopiuj sekwencyjnie w dodatku na przykład.

## <a name="model-datasets-with-different-frequencies"></a>Zestawy danych modelu z różnymi częstotliwościami
W przykładach częstotliwości dla wejściowych i wyjściowych zestawów danych oraz okno harmonogramu działania były takie same. Niektóre scenariusze wymagają możliwości wygenerowania danych wyjściowych z częstotliwością różną od częstotliwości jednego lub większej liczby wejść. Data Factory obsługuje modelowanie tych scenariuszy.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Przykład 1: Tworzenie dziennego raportu wyjściowego dla danych wejściowych, które są dostępne co godzinę
Rozważmy scenariusz, w którym dane pomiarów danych wejściowych z czujników są dostępne co godzinę w usłudze Azure Blob Storage. Chcesz utworzyć dzienny raport zagregowany ze statystykami, takimi jak średnia, wartość maksymalna i minimum dla dnia, [Data Factory działania programu Hive](data-factory-hive-activity.md).

Oto jak można modelować ten scenariusz przy użyciu Data Factory:

**Wejściowy zestaw danych**

Pliki wejściowe godzinowe są porzucane w folderze dla danego dnia. Dostępność danych wejściowych jest ustawiona na **godzinę** (częstotliwość: godzina, interwał: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Wyjściowy zestaw danych**

Jeden plik wyjściowy jest tworzony codziennie w folderze dnia. Dostępność danych wyjściowych jest ustawiana na **dzień** (częstotliwość: dzień i interwał: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Działanie: działanie Hive w potoku**

Skrypt Hive otrzymuje odpowiednie informacje o wartościach *DateTime* jako parametry, które używają zmiennej **WindowStart** , jak pokazano w poniższym fragmencie kodu. Skrypt programu Hive używa tej zmiennej do ładowania danych z poprawnego folderu dla danego dnia i uruchamiania agregacji w celu wygenerowania danych wyjściowych.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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

Na poniższym diagramie przedstawiono scenariusz z punktu widzenia zależności danych.

![Zależność danych](./media/data-factory-scheduling-and-execution/data-dependency.png)

Wycinek danych wyjściowych dla każdego dnia zależy od 24-godzinnych wycinków z wejściowego zestawu danych. Data Factory oblicza te zależności automatycznie, wprowadzając wycinki danych wejściowych, które znajdują się w tym samym okresie, co wycinek wyjściowy. Jeśli którykolwiek z 24 wycinków wejściowych nie jest dostępny, Data Factory czeka na przygotowanie wycinka danych wejściowych przed rozpoczęciem codziennego uruchomienia działania.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Przykład 2: Określanie zależności za pomocą wyrażeń i funkcji Data Factory
Rozważmy inny scenariusz. Załóżmy, że masz działanie Hive, które przetwarza dwa wejściowe zestawy danych. Jeden z nich ma nowe dane codziennie, ale jeden z nich otrzymuje nowe dane co tydzień. Załóżmy, że chcesz wykonać sprzężenie między dwoma danymi wejściowymi i utworzyć dane wyjściowe codziennie.

Prostym podejściem, w którym Data Factory są automatycznie rysowane odpowiednie wycinków danych wejściowych do przetworzenia przez wyrównanie do przedziału czasu wycinka danych wyjściowych, nie działa.

Należy określić, że dla każdego uruchomienia działania Data Factory powinien używać wycinka danych w ostatnim tygodniu dla tygodniowego zestawu danych wejściowych. Użyj funkcji Azure Data Factory, jak pokazano w poniższym fragmencie kodu, aby zaimplementować to zachowanie.

**Input1: obiekt blob platformy Azure**

Pierwsze dane wejściowe to obiekt blob platformy Azure, który jest aktualizowany codziennie.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: obiekt blob platformy Azure**

Input2 to obiekt blob platformy Azure aktualizowany co tydzień.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Dane wyjściowe: obiekt blob platformy Azure**

Jeden plik wyjściowy jest tworzony codziennie w folderze dla tego dnia. Ustawienie dostępność danych wyjściowych to **dzień** (częstotliwość: dzień, interwał: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Działanie: działanie Hive w potoku**

Działanie programu Hive przyjmuje dwa dane wejściowe i generuje wycinek danych wyjściowych codziennie. Można określić, aby wycinek danych wyjściowych każdego dnia zależał od wycinka wejściowego poprzedniego tygodnia w celu uzyskania tygodniowego wejścia w następujący sposób.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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

Zobacz [Data Factory funkcje i zmienne systemowe](data-factory-functions-variables.md) dla listy funkcji i zmiennych systemowych, które Data Factory obsługiwane przez program.

## <a name="appendix"></a>Dodatek

### <a name="example-copy-sequentially"></a>Przykład: Kopiuj sekwencyjnie
Istnieje możliwość uruchomienia wielu operacji kopiowania jeden po drugim w sekwencyjny/uporządkowany sposób. Na przykład można mieć dwie działania kopiowania w potoku (plik copyactivity1 i CopyActivity2) z następującymi wejściowymi wyjściowymi zestawami danych:   

Plik copyactivity1

Dane wejściowe: zestaw danych. Wynik: Dataset2.

CopyActivity2

Dane wejściowe: Dataset2.  Wynik: Dataset3.

CopyActivity2 będzie działać tylko wtedy, gdy plik copyactivity1 została pomyślnie uruchomiona i Dataset2 jest dostępna.

Oto przykładowy kod JSON potoku:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Zwróć uwagę, że w przykładzie wyjściowy zestaw danych pierwszego działania kopiowania (Dataset2) jest określony jako dane wejściowe dla drugiego działania. W związku z tym drugie działanie jest uruchamiane tylko wtedy, gdy wyjściowy zestaw danych z pierwszego działania jest gotowy.  

W przykładzie CopyActivity2 może mieć inne dane wejściowe, takie jak Dataset3, ale jako dane wejściowe do CopyActivity2 należy określić Dataset2, więc działanie nie jest uruchamiane do momentu zakończenia plik copyactivity1. Na przykład:

Plik copyactivity1

Dane wejściowe: pozycję DataSet1. Wynik: Dataset2.

CopyActivity2

Dane wejściowe: Dataset3, Dataset2. Wynik: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Zwróć uwagę, że w przykładzie dla drugiego działania kopiowania określono dwa wejściowe zestawy danych. Gdy określono wiele danych wejściowych, do kopiowania danych jest używany tylko pierwszy wejściowy zestaw danych, ale inne zestawy są używane jako zależności. CopyActivity2 będzie uruchamiany dopiero po spełnieniu następujących warunków:

* Plik copyactivity1 zakończyła się pomyślnie i Dataset2 jest dostępna. Ten zestaw danych nie jest używany podczas kopiowania danych do Dataset4. Działa tylko jako zależność planowania dla CopyActivity2.   
* Dataset3 jest dostępny. Ten zestaw danych reprezentuje dane, które są kopiowane do miejsca docelowego. 
