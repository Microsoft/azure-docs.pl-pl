---
title: Wyzwalacze i wykonywanie potoku w usłudze Azure Data Factory
description: Ten artykuł zawiera informacje na temat wykonywania potoku w usłudze Azure Data Factory na żądanie lub przy użyciu wyzwalacza.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: e090b4c3b4ecc3870f060aba4b03be3abe2942ec
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180714"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Wyzwalacze i wykonywanie potoku w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-scheduling-and-execution.md)
> * [Bieżąca wersja](concepts-pipeline-execution-triggers.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

_Uruchomienie potoku_ w bieżącej wersji usługi Azure Data Factory definiuje wystąpienie wykonania potoku. Załóżmy na przykład istnienie potoku, który jest wykonywany o godzinie 8:00, 9:00 i 10:00. W takim przypadku istnieją trzy osobne uruchomienia potoku lub przebiegów potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Identyfikator uruchomienia jest identyfikatorem GUID, który w sposób unikatowy definiuje to konkretne uruchomienie potoku.

Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które definiujesz w potoku. Potok możesz wykonywać ręcznie albo za pomocą _wyzwalacza_. Ten artykuł zawiera szczegółowe informacje na temat obu metod wykonywania potoku.

## <a name="manual-execution-on-demand"></a>Wykonywanie ręczne (na żądanie)

Ręczne wykonywanie potoku nazywane jest również wykonywaniem _na żądanie_.

Na przykład załóżmy, że masz podstawowy potok o nazwie **copyPipeline**, który chcesz wykonać. Ten potok ma jedno działanie polegające na kopiowaniu elementów z folderu źródłowego w magazynie Azure Blob Storage do folderu docelowego w tym samym magazynie. W następującej definicji JSON przedstawiono ten przykładowy potok:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

W definicji JSON potok przyjmuje dwa parametry: **sourceBlobContainer** i **sinkBlobContainer**. Wartości tych parametrów są przekazywane w czasie uruchomienia.

Możesz uruchomić potok ręcznie, używając jednej z następujących metod:
- Zestaw SDK .NET
- Moduł programu Azure PowerShell
- Interfejs API REST
- Zestaw SDK dla języka Python

### <a name="rest-api"></a>Interfejs API REST

Następujące przykładowe polecenie pokazuje, jak uruchomić potok przy użyciu interfejsu API REST ręcznie:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Pełny przykład można znaleźć w artykule [Szybki start: tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące przykładowe polecenie ilustruje, jak ręcznie uruchomić potok przy użyciu programu Azure PowerShell:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Parametry są przekazywane w treści ładunku żądania. W zestawie SDK platformy .NET, programie Azure PowerShell i zestawie SDK dla języka Python wartości przekazuje się w słowniku przekazywanym jako argument wywołania:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

Ładunek odpowiedzi to unikatowy identyfikator uruchomienia potoku:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Pełny przykład można znaleźć w artykule [Szybki start: tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>Zestaw SDK .NET

Następujące przykładowe wywołanie pokazuje, jak uruchomić potok przy użyciu zestawu .NET SDK ręcznie:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Pełny przykład można znaleźć w artykule [Szybki start: tworzenie fabryki danych przy użyciu zestawu SDK platformy .NET](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Zestaw SDK platformy .NET umożliwia wywoływanie potoków Data Factory z Azure Functions, z usług sieci Web i tak dalej.

## <a name="trigger-execution"></a>Wykonywanie wyzwalacza

Wyzwalacze to inny sposób wykonywania uruchomienia potoku. Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy należy rozpocząć wykonanie potoku. Obecnie usługa Data Factory obsługuje 3 typy wyzwalaczy:

- Wyzwalacz harmonogramu: wyzwalacz, który wywołuje potok zgodnie z harmonogramem zegarowym.

- Wyzwalacz okna wirowania: wyzwalacz działający w okresowych interwałach przy zachowaniu stanu.

- Wyzwalacz oparty na zdarzeniach: wyzwalacz, który odpowiada na zdarzenie.

Potoki i wyzwalacze mają relację wiele-do-wielu (z wyjątkiem wyzwalacza okna wirowania). Wiele wyzwalaczy może uruchamiać pojedynczy potok lub jeden wyzwalacz może uruchomić wiele potoków. W poniższej definicji wyzwalacza właściwość **pipelines** odnosi się do listy potoków wyzwalanych przez określony wyzwalacz. Definicja właściwości zawiera wartości dla parametrów potoku.
### <a name="basic-trigger-definition"></a>Podstawowa definicja wyzwalacza

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Wyzwalacz harmonogramu
Wyzwalacz harmonogramu uruchamia potoki zgodnie z harmonogramem zegarowym. Ten wyzwalacz obsługuje opcje okresowe i zaawansowane kalendarza. Obsługuje na przykład interwały takie jak „co tydzień” czy „w poniedziałek o godzinie 17:00 i czwartek o godzinie 21: 00”. Wyzwalacz harmonogramu jest elastyczny, ponieważ wzorzec zestawu jest niezależny, a wyzwalacz nie rozróżnia pomiędzy danymi serii czasowych a innymi.

Aby uzyskać więcej informacji o wyzwalaczach harmonogramu i przykładach, zobacz [Tworzenie wyzwalacza, który uruchamia potok zgodnie z harmonogramem](how-to-create-schedule-trigger.md).

## <a name="schedule-trigger-definition"></a>Definicja wyzwalacza harmonogramu
Tworząc wyzwalacz harmonogramu, należy określić planowanie i powtarzanie przy użyciu definicji JSON.

Aby wyzwalacz harmonogramu uruchamiał potok, należy dołączyć odwołanie do konkretnego potoku do definicji wyzwalacza. Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> Właściwość **parameters** jest obowiązkową wartością elementu właściwości **pipelines**. Jeśli potok nie przyjmuje żadnych parametrów, należy dodać pustą definicję JSON dla właściwości **parameters**.

### <a name="schema-overview"></a>Omówienie schematu
Poniższa tabela zawiera ogólne omówienie głównych elementów schematu odnoszących się do powtarzania i planowania wyzwalacza:

| Właściwość JSON | Opis |
| --- | --- |
| **Rozpoczęcia** | Wartość daty i godziny. W przypadku podstawowych harmonogramów wartość właściwości **startTime** dotyczy pierwszego wystąpienia. W przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany wcześniej niż określona wartość właściwości **startTime**. |
| **endTime** | Data i godzina zakończenia wyzwalacza. Wyzwalacz nie jest wykonywany po określonej dacie i godzinie zakończenia. Wartość właściwości nie może odnosić się do przeszłości. <!-- This property is optional. --> |
| **timeZone** | Strefa czasowa. Aby uzyskać listę obsługiwanych stref czasowych, zobacz [Tworzenie wyzwalacza uruchamiającego potok zgodnie z harmonogramem](how-to-create-schedule-trigger.md#time-zone-option). |
| **wystąpieniu** | Obiekt cyklu określający reguły powtarzania wyzwalacza. Obiekt cyklu obsługuje elementy właściwości **frequency**, **interval**, **endTime**, **count** i **schedule**. Po zdefiniowaniu obiektu cyklu wymagany jest element właściwości **frequency**. Inne elementy obiektu cyklu są opcjonalne. |
| **frequency** | Jednostka częstotliwości powtarzania wyzwalacza. Obsługiwane wartości to „minute” („minuta”), „hour” („godzina”), „day” („dzień”), „week” („tydzień”) i „month” („miesiąc”). |
| **dat** | Dodatnia liczba całkowita, która określa odstęp czasu dla wartości **frequency**. Wartość **frequency** określa, jak często jest uruchamiany wyzwalacz. Jeśli na przykład właściwość **interval** ma wartość 3, a właściwość **frequency** ma wartość „week” („tydzień”), wyzwalacz jest powtarzany co trzy tygodnie. |
| **rozkład** | Harmonogram cyklu wyzwalacza. Wyzwalacz z określoną wartością właściwości **frequency** zmienia swój cykl na podstawie harmonogramu cyklu. Właściwość **schedule** zawiera modyfikacje cyklu oparte na minutach, godzinach, dniach tygodnia, dniach miesiąca i numerze tygodnia. |

### <a name="schedule-trigger-example"></a>Przykładowy wyzwalacz harmonogramu

```json
{
  "properties": {
    "name": "MyTrigger",
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": "Hour",
        "interval": 1,
        "startTime": "2017-11-01T09:00:00-08:00",
        "endTime": "2017-11-02T22:00:00-08:00"
      }
    },
    "pipelines": [{
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToBlobPipeline"
        },
        "parameters": {}
      },
      {
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToAzureSQLPipeline"
        },
        "parameters": {}
      }
    ]
  }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Wartości domyślne, limity i przykłady harmonogramów

| Właściwość JSON | Typ | Wymagane | Wartość domyślna | Prawidłowe wartości | Przykład |
| --- | --- | --- | --- | --- | --- |
| **Rozpoczęcia** | ciąg | Tak | Brak | Daty i godziny ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **wystąpieniu** | object | Tak | Brak | Obiekt cyklu | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **dat** | liczba | Nie | 1 | Od 1 do 1000 | `"interval":10` |
| **endTime** | ciąg | Tak | Brak | Wartość daty i godziny reprezentująca godzinę w przyszłości | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **rozkład** | object | Nie | Brak | Obiekt harmonogramu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Właściwość startTime
W poniższej tabeli przedstawiono, w jaki sposób właściwość **startTime** kontroluje uruchamianie wyzwalacza:

| Wartość startTime | Cykl bez harmonogramu | Cykl z harmonogramem |
| --- | --- | --- |
| **Czas rozpoczęcia jest w przeszłości** | Oblicza pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchamia wyzwalacz w tym czasie.<br /><br />Uruchamia kolejne wykonania obliczone na podstawie ostatniego czasu wykonania.<br /><br />Zobacz przykład znajdujący się pod tabelą. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br /><br />Kolejne wykonania opierają na harmonogramie cyklu. |
| **Czas rozpoczęcia jest w przyszłości lub jest to bieżący czas** | Uruchamiany raz o określonym czasie rozpoczęcia.<br /><br />Uruchamia kolejne wykonania obliczone na podstawie ostatniego czasu wykonania. | Wyzwalacz jest uruchamiany _nie wcześniej_ niż w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br /><br />Kolejne wykonania opierają na harmonogramie cyklu. |

Rozpatrzmy przykład zdarzeń w przypadku, gdy czas rozpoczęcia przypada w przeszłości, z cyklem, ale bez harmonogramu. Załóżmy, że obecna data i godzina to 13:00 08-04-2017, czas rozpoczęcia to 14:00 07-04-2017, a cykl jest określony na 2 dni. (Wartość **cyklu** jest definiowana przez ustawienie właściwości **częstotliwość** na wartość "dzień" i Właściwość **Interwał** na 2). Należy zauważyć, że wartość **StartTime** jest w przeszłości i występuje przed bieżącą godziną.

W tych warunkach pierwsze wykonanie jest 2017-04-09 o 14:00. Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W tym scenariuszu godziną rozpoczęcia jest 14:00 w dniu 07-04-2017. Następne wystąpienie zostanie uruchomione za dwa dni od tego czasu, czyli o 14:00 w dniu 09-04-2017.

Czas pierwszego wykonania jest identyczny, nawet jeśli właściwość **startTime** ma wartość 14:00 05-04-2017 lub 14:00 01-04-2017. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. W związku z tym kolejne wykonania zostaną uruchomione o 14:00 w dniu 11-04-2017, a następnie o 14:00 w dniu 13-04-2017, o 14:00 w dniu 15-04-2017 i tak dalej.

Na koniec, gdy w harmonogramie dla wyzwalacza nie ustawiono godzin lub minut, godziny lub minuty pierwszego wykonania są używane jako wartości domyślne.

### <a name="schedule-property"></a>Wartość schedule
Właściwości **schedule** można użyć do *ograniczenia* liczby wykonań wyzwalacza. Jeśli na przykład uruchomienie wyzwalacza z częstotliwością miesięczną zaplanowano na 31. dzień, wyzwalacz będzie uruchamiany tylko w miesiącach, które mają trzydzieści jeden dni.

Właściwości **schedule** możesz też użyć do *zwiększenia* liczby wykonań wyzwalacza. Na przykład wyzwalacz z częstotliwością miesięczną, którego uruchomienie zaplanowano na 1. i 2. dzień miesiąca, będzie uruchamiany pierwszego i drugiego dnia miesiąca, a nie raz na miesiąc.

Jeśli określono wiele elementów **harmonogramu** , kolejność oceny jest od największej do najmniejszego ustawienia harmonogramu: numer tygodnia, dzień miesiąca, dzień tygodnia, godzina, minuta.

W poniższej tabeli opisano szczegółowo elementy właściwości **schedule**:

| Element JSON | Opis | Prawidłowe wartości |
| --- | --- | --- |
| **minut** | Minuty godziny, o których uruchamiany jest wyzwalacz. |- Liczba całkowita<br />- Tablica liczb całkowitych |
| **liczb** | Godziny dnia, o których uruchamiany jest wyzwalacz. |- Liczba całkowita<br />- Tablica liczb całkowitych |
| **weekDays** | Dni tygodnia, w które uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością tygodniową.|<br />- Poniedziałek<br />- Wtorek<br />- Środa<br />- Czwartek<br />- Piątek<br />- Sobota<br />- Niedziela<br />- Tablica wartości dni (maksymalny rozmiar tablicy to 7)<br /><br />W wartościach dni nie jest rozróżniana wielkość liter |
| **monthlyOccurrences** | Dni miesiąca, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. |-Tablica obiektów **monthlyOccurrence** : `{ "day": day, "occurrence": occurrence }`<br />- Atrybut **day** jest dniem tygodnia, w którym uruchamiany jest wyzwalacz. Na przykład właściwość **monthlyOccurrences** o wartości **day** wynoszącej `{Sunday}` oznacza każdą niedzielę miesiąca. Atrybut **day** jest wymagany.<br />- Atrybut **occurence** jest wystąpieniem określonej wartości **day** w miesiącu. Na przykład właściwość **monthlyOccurrences** o wartościach **day** i **occurence** wynoszących `{Sunday, -1}` oznacza ostatnią niedzielę miesiąca. Atrybut **occurence** jest opcjonalny. |
| **monthDays** | Dzień miesiąca, w którym uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. |- Dowolna wartość <= -1 i >= -31<br />- Dowolna wartość >= 1 i <= 31<br />- Tablica wartości |

## <a name="tumbling-window-trigger"></a>Wyzwalacz okna wirowania
Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach.

Aby uzyskać więcej informacji na temat wyzwalaczy okna wirowania i dla przykładów, zobacz [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md).

## <a name="event-based-trigger"></a>Wyzwalacz oparty na zdarzeniach

Wyzwalacz oparty na zdarzeniach uruchamia potoki w odpowiedzi na zdarzenie, takie jak nadejście pliku lub usuwanie pliku, w usłudze Azure Blob Storage.

Aby uzyskać więcej informacji dotyczących wyzwalaczy opartych na zdarzeniach, zobacz [Create a trigger that runs a pipeline in response to an event](how-to-create-event-trigger.md) (Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie).

## <a name="examples-of-trigger-recurrence-schedules"></a>Przykłady harmonogramów cyklu wyzwalaczy
Ta sekcja zawiera przykłady harmonogramów cyklu. Koncentruje się ona na obiekcie **schedule** i jego elementach.

W przykładach założono, że wartość **interwału** wynosi 1 i że wartość **częstotliwości** jest poprawna zgodnie z definicją harmonogramu. Na przykład nie można mieć wartości **frequency** równej „day” i jednocześnie mieć modyfikacji **monthDays** w obiekcie **schedule**. Tego rodzaju ograniczenia są opisane w tabeli w poprzedniej sekcji.

| Przykład | Opis |
| --- | --- |
| `{"hours":[5]}` | Uruchamiany o godz. 5:00 każdego dnia. |
| `{"minutes":[15], "hours":[5]}` | Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` | Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` | Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Uruchamiany co godzinę.<br /><br />Ten wyzwalacz jest uruchamiany co godzinę. Minuty są kontrolowane przez wartość właściwości **startTime**, gdy wartość jest określona. Jeśli wartość nie jest określona, minuty są kontrolowane przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od sytuacji) to 12:25, wyzwalacz jest uruchamiany o godzinie 0:25, 1:25, 2:25, ..., 23:25.<br /><br />Ten harmonogram jest odpowiednikiem wyzwalacza z wartością **częstotliwości** równą "Hour", wartości **interwału** 1 i bez **harmonogramu**. Tego harmonogramu można także używać z innymi wartościami właściwości **frequency** i **interval** do tworzenia innych wyzwalaczy. Na przykład, gdy wartość **częstotliwości** to "miesiąc", harmonogram jest uruchamiany tylko raz w miesiącu, a nie codziennie, gdy wartość **częstotliwości** to "dzień". |
| `{"minutes":[0]}` | Uruchamiany co godzinę o pełnej godzinie.<br /><br />Ten wyzwalacz jest uruchamiany co godzinę o pełnej godzinie począwszy od godz. 0:00, a następnie o 1:00, 2:00 itd.<br /><br />Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour” i właściwością **startTime** o wartości 0 minut lub bez właściwości **schedule**, ale z właściwością **frequency** o wartości „day”. Jeśli wartość właściwości **frequency** to „week” lub „month”, harmonogram jest wykonywany odpowiednio tylko raz w tygodniu lub raz w miesiącu. |
| `{"minutes":[15]}` | Uruchamiany 15 minut po pełnej godzinie.<br /><br />Ten wyzwalacz jest uruchamiany 15 minut po pełnej godzinie począwszy od godz. 0:15, a następnie o 1:15, 2:15 itd. aż do 23.15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Uruchamiany o godz. 17:00 w każdą sobotę. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:00 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze, między godz. 9:00 a 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Uruchamiany we wtorki i czwartki o określonej godzinie. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Uruchamiany o godz. 6:00 dwudziestego ósmego dnia każdego miesiąca (zakładając, że właściwość **frequency** ma wartość „month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Uruchamiany o godz. 6:00 ostatniego dnia miesiąca.<br /><br />Aby uruchomić wyzwalacz ostatniego dnia miesiąca, użyj wartości -1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Uruchamiany o godz. 6:00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,14]}` | Uruchamiany pierwszego i czternastego dnia każdego miesiąca o określonej godzinie. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Uruchamiany w trzeci piątek od końca każdego miesiąca o określonej godzinie. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Uruchamiany w piąty piątek każdego miesiąca o określonej godzinie.<br /><br />Gdy w miesiącu nie ma piątego piątku, potok nie zostanie uruchomiony. Aby uruchomić wyzwalacz w ostatni piątek miesiąca, rozważ użycie wartości -1 zamiast 5 dla właściwości **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="trigger-type-comparison"></a>Porównanie typów wyzwalaczy
Zarówno wyzwalacz okna wirowania, jak i wyzwalacz harmonogramu pracują w oparciu o pulsy czasu. Czym się od siebie różnią?

> [!NOTE]
> Uruchomienie wyzwalacza okna wirowaniaa *czeka na zakończenie wyzwalanego uruchomienia potoku* . Jego stan przebiegu odzwierciedla stan wyzwalanego uruchomienia potoku. Na przykład jeśli wyzwolone uruchomienie potoku zostanie anulowane, odpowiadające mu uruchomienie wyzwalacza okna wirowania zostanie oznaczone jako anulowane. Różni się to od zachowania "pożar i zapomnij" wyzwalacza harmonogramu, który jest oznaczony jako zakończony powodzeniem, o ile uruchomiono uruchomienie potoku.

Poniższa tabela zawiera porównanie wyzwalacza okna wirowania i wyzwalacza harmonogramu:

| Element | Wyzwalacz okna wirowania | Wyzwalacz harmonogramu |
| --- | --- | --- |
| **Scenariusze wypełniania** | Obsługiwane. Uruchomienia potoków można planować dla okien w przeszłości. | Nieobsługiwane. Uruchomienia potoków można wykonywać tylko w teraźniejszych i przyszłych okresach czasu. |
| **Niezawodność** | 100-procentowa niezawodność. Uruchomienia potoków można zaplanować dla wszystkich okien od określonej daty rozpoczęcia bez przerw. | Mniej niezawodne. |
| **Możliwość ponowienia próby** | Obsługiwane. Uruchomienia potoków zakończone niepowodzeniem mają domyślne zasady ponawiania próby ustawione na wartość 0 lub zasady określone przez użytkownika w definicji wyzwalacza. Automatycznie ponawia próbę, gdy uruchomienie potoku nie powiodło się z powodu ograniczeń współbieżności/serwera/ograniczania (czyli kodów stanu 400: błąd użytkownika, 429: zbyt wiele żądań i 500: wewnętrzny błąd serwera). | Nieobsługiwane. |
| **Współbieżność** | Obsługiwane. Użytkownicy mogą jawnie ustawić limity współbieżności dla wyzwalacza. Umożliwia od 1 do 50 jednocześnie wyzwolonych uruchomień potoków. | Nieobsługiwane. |
| **Zmienne systemu** | Wraz z @trigger (). scheduledTime i @trigger (). StartTime obsługuje także użycie zmiennych systemowych **WindowStart** i **WindowEnd** . Użytkownicy mogą uzyskiwać dostęp do zmiennych `triggerOutputs().windowStartTime` i `triggerOutputs().windowEndTime` jako zmiennych systemu wyzwalacza w definicji wyzwalacza. Wartości są używane odpowiednio jako czas rozpoczęcia okna i czas zakończenia okna. Na przykład w przypadku wyzwalacza okna wirowania uruchamianego co godzinę dla okna od godz. 1:00 do 2:00 definicją jest zmienna `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` i `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Obsługiwane są tylko @trigger zmienne default (). scheduledTime i @trigger (). StartTime. |
| **Relacja potoku do wyzwalacza** | Obsługuje relację „jeden do jednego”. Można wyzwolić tylko jeden potok. | Obsługuje relacje „wiele do wielu”. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków. |

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki:

- [Szybki start: tworzenie fabryki danych przy użyciu zestawu SDK platformy .NET](quickstart-create-data-factory-dot-net.md)
- [Tworzenie wyzwalacza harmonogramu](how-to-create-schedule-trigger.md)
- [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md)
