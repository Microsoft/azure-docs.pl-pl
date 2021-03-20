---
title: Tworzenie/planowanie potoków, działania łańcucha w Data Factory
description: Dowiedz się, jak utworzyć potok danych w Azure Data Factory, aby przenosić i przekształcać dane. Utwórz przepływ pracy oparty na danych, aby utworzyć gotowy do korzystania z informacji.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9cc81a8e157c244828a15ac82913ce9a88c3d34f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376874"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Potoki i działania w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-create-pipelines.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [potoki w wersji 2](../concepts-pipelines-activities.md).

Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz z konstruowaniem za ich pomocą pełnych przepływów pracy dla scenariuszy przenoszenia i przetwarzania danych.

> [!NOTE]
> W tym artykule przyjęto założenie, że wprowadzono [wprowadzenie do Azure Data Factory](data-factory-introduction.md). Jeśli nie masz praktycznego doświadczenia w tworzeniu fabryk danych, przechodzenie przez [samouczek do przekształcania danych](data-factory-build-your-first-pipeline.md) i/lub [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pomoże Ci lepiej zrozumieć ten artykuł.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. Potok jest logicznym grupowaniem działań, które wspólnie wykonują zadanie. Działania w potoku określają akcje do wykonania na danych. Na przykład możesz użyć działania kopiowania, aby skopiować dane z bazy danych SQL Server do Blob Storage platformy Azure. Następnie użyj działania usługi Hive, które uruchamia skrypt Hive w klastrze usługi Apache HDInsight w celu przetworzenia/przekształcenia danych z magazynu obiektów blob, aby utworzyć dane wyjściowe. Na koniec Użyj drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure Synapse Analytics, na podstawie której są tworzone rozwiązania do raportowania analizy biznesowej.

Dane działanie może — ale nie musi — korzystać z wejściowych [zestawów danych](data-factory-create-datasets.md) i generować co najmniej jeden wyjściowy [zestaw danych](data-factory-create-datasets.md). Na poniższym diagramie przedstawiono relację między potokiem, działaniem i zestawem danych w usłudze Data Factory:

![Relacja między potokiem, działaniem i zestawem danych](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Potok pozwala zarządzać działaniami jako zestawem, a nie osobno pojedynczo. Można na przykład wdrażać, planować, wstrzymywać i wznawiać potok, zamiast korzystać z działań w potoku niezależnie.

Usługa Data Factory obsługuje dwa typy działań — w zakresie przekształcania oraz przenoszenia danych. Każde działanie może mieć zero lub więcej wejściowych [zestawów](data-factory-create-datasets.md) danych i generować co najmniej jeden wyjściowy zestaw danych.

Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Po utworzeniu zestawu danych można go użyć z działaniami w potoku. Na przykład zestaw danych może być zestawem danych wejściowych/wyjściowych działania kopiowania lub działania HDInsightHive. Aby uzyskać więcej informacji na temat zestawów danych, zobacz artykuł [Datasets in Azure Data Factory](data-factory-create-datasets.md) (Zestawy danych w usłudze Azure Data Factory).

### <a name="data-movement-activities"></a>Działania dotyczące przenoszenia danych
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

Aby uzyskać więcej informacji, zobacz artykuł [Działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Działania dotyczące przekształcania danych
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Aby uzyskać więcej informacji, zobacz artykuł [Działania dotyczące przekształcania danych](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Niestandardowe działania programu .NET
Jeśli musisz przenieść dane do/z magazynu danych, którego nie obsługuje działanie kopiowania, lub Przekształć dane przy użyciu własnej logiki, Utwórz **niestandardowe działanie platformy .NET**. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

## <a name="schedule-pipelines"></a>Planowanie potoków
Potok jest aktywny tylko między jego czasem **rozpoczęcia** i czasem **zakończenia** . Nie jest wykonywane przed czasem rozpoczęcia lub po zakończeniu. Jeśli potok jest wstrzymany, nie jest wykonywany niezależnie od jego czasu rozpoczęcia i zakończenia. Do uruchomienia potoku nie należy wstrzymywać. Zapoznaj się z tematem [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) , aby zrozumieć, jak planowanie i wykonywanie działa w Azure Data Factory.

## <a name="pipeline-json"></a>Format JSON potoku
Przyjrzyjmy się bliżej definicji potoku w formacie JSON. Ogólna struktura potoku wygląda następująco:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa potoku. Określ nazwę, która reprezentuje akcję wykonywaną przez potok. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się cyfrą lub znakiem podkreślenia ( \_ )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", " \* ", "%", "&", ":", " \\ "</li></ul> |Tak |
| description (opis) | Wprowadź tekst opisujący przeznaczenie potoku. |Tak |
| activities | W sekcji **activities** można zdefiniować jedno lub więcej działań. Zobacz następną sekcję, aby uzyskać szczegółowe informacje o elemencie JSON. | Tak |
| start | Data i godzina rozpoczęcia potoku. Musi być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: `2016-10-14T16:32:41Z`. <br/><br/>Można określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00-05:00` ", czyli 6.<br/><br/>Właściwości Start i End wspólnie określają aktywny okres dla potoku. Wycinki wyjściowe są tworzone tylko w tym aktywnym okresie. |Nie<br/><br/>W przypadku określenia wartości właściwości End należy określić wartość właściwości Start.<br/><br/>Aby można było utworzyć potok, oba czasy rozpoczęcia i zakończenia mogą być puste. Należy określić obie wartości, aby ustawić aktywny okres uruchomienia potoku. Jeśli nie określisz godzin początkowych i końcowych podczas tworzenia potoku, możesz je ustawić przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod w późniejszym czasie. |
| end | Data i godzina zakończenia potoku. Jeśli ta wartość jest określona, musi być w formacie ISO. Na przykład: `2016-10-14T17:32:41Z` <br/><br/>Można określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00-05:00` , czyli 6.<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. <br/><br/> Potok jest aktywny tylko między jego czasem rozpoczęcia i czasem zakończenia. Nie jest wykonywane przed czasem rozpoczęcia lub po zakończeniu. Jeśli potok jest wstrzymany, nie jest wykonywany niezależnie od jego czasu rozpoczęcia i zakończenia. Do uruchomienia potoku nie należy wstrzymywać. Zapoznaj się z tematem [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) , aby zrozumieć, jak planowanie i wykonywanie działa w Azure Data Factory. |Nie <br/><br/>W przypadku określenia wartości właściwości Start należy określić wartość właściwości end.<br/><br/>Zobacz uwagi dotyczące właściwości **Start** . |
| isPaused | W przypadku ustawienia wartości true potok nie zostanie uruchomiony. Jest w stanie wstrzymania. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania potoku. |Nie |
| potokmode | Metoda planowania przebiegów dla potoku. Dozwolone wartości to: zaplanowane (wartość domyślna), jednorazowej.<br/><br/>Wartość "zaplanowany" wskazuje, że potok jest uruchamiany w określonym przedziale czasu, zgodnie z jego aktywnym okresem (godzina rozpoczęcia i zakończenia). Element "jednorazowej" wskazuje, że potok jest uruchamiany tylko raz. Nie można obecnie modyfikować/aktualizować potoków jednorazowej utworzonych po utworzeniu. Zobacz [potok jednorazowej](#onetime-pipeline) , aby uzyskać szczegółowe informacje na temat ustawienia jednorazowej. |Nie |
| expirationTime | Czas trwania okresu, w którym [potok jednorazowy](#onetime-pipeline) jest prawidłowy i powinien pozostać zainicjowany. Jeśli nie ma żadnych aktywnych, zakończonych niepowodzeniem lub oczekujących uruchomień, potok jest automatycznie usuwany po osiągnięciu czasu wygaśnięcia. Wartość domyślna: `"expirationTime": "3.00:00:00"`|Nie |
| zestawy danych |Lista zestawów danych, które mają być używane przez działania zdefiniowane w potoku. Ta właściwość może służyć do definiowania zestawów danych, które są specyficzne dla tego potoku i nie są zdefiniowane w ramach fabryki Data Factory. Zestawy danych zdefiniowane w tym potoku mogą być używane tylko przez ten potok i nie mogą być udostępniane. Aby uzyskać szczegółowe informacje, zobacz [zestawy danych z zakresem](data-factory-create-datasets.md#scoped-datasets) . |Nie |

## <a name="activity-json"></a>Format JSON działania
W sekcji **activities** można zdefiniować jedno lub więcej działań. Każde działanie ma następującą strukturę najwyższego poziomu:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

Poniższa tabela zawiera opis właściwości w definicji JSON działania:

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name | Nazwa działania. Określ nazwę, która reprezentuje akcję wykonywaną przez działanie. <br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się cyfrą lub znakiem podkreślenia ( \_ )</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Tak |
| description (opis) | Tekst opisujący przeznaczenie działania |Tak |
| typ | Typ działania. Zapoznaj się z sekcjami [działania przenoszenia danych](#data-movement-activities) i [działania przekształcania danych](#data-transformation-activities) dla różnych typów działań. |Tak |
| danych wejściowych |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Tak |
| wydajności |Tabele wyjściowe używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Tak |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak dla działania usługi HDInsight i działania oceniania partii Azure Machine Learning Studio (klasycznej) <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji **typeProperties** zależą od typu działania. Aby wyświetlić właściwości typu dla działania, kliknij linki do działań w poprzedniej sekcji. | Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określona, używane są zasady domyślne. |Nie |
| scheduler | Właściwość "Scheduler" służy do definiowania żądanego planowania dla działania. Jego właściwości własne są takie same jak te we [właściwości Availability w zestawie danych](data-factory-create-datasets.md#dataset-availability). |Nie |

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

## <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**.
* Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. Definiowanie zestawów danych w formacie JSON opisano w artykule [Zestawy danych](data-factory-create-datasets.md).
* W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. W sekcji [działania dotyczące przenoszenia danych](#data-movement-activities) kliknij magazyn danych, którego chcesz użyć jako źródła lub ujścia, aby dowiedzieć się więcej na temat przenoszenia danych do/z tego magazynu danych.

Aby uzyskać kompletny przewodnik tworzenia tego potoku, zobacz [Samouczek: kopiowanie danych z BLOB Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Przykładowy potok przekształcania
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** został ustawiony na wartość **HDInsightHive**.
* Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie usługi Azure Storage (określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService**) oraz w folderze **script** w kontenerze **adfgetstarted**.
* `defines`Sekcja służy do określania ustawień środowiska uruchomieniowego, które są przesyłane do skryptu programu Hive jako wartości konfiguracyjne programu Hive (np `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` .).

Sekcja **typeProperties** jest inna dla każdego działania przekształcania. Aby uzyskać informacje na temat właściwości typu obsługiwanych dla działania przekształcania, kliknij działanie przekształcania w tabeli [działania przekształcania danych](#data-transformation-activities) .

Aby uzyskać kompletny przewodnik tworzenia tego potoku, zobacz [Samouczek: Tworzenie pierwszego potoku w celu przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Poprzednie dwa przykładowe potoki zawierają tylko po jednym działaniu. Potok może obejmować więcej niż jedno działanie.

Jeśli masz wiele działań w potoku, a dane wyjściowe działania nie są danymi wejściowymi innego działania, działania mogą działać równolegle, jeśli wycinki danych wejściowych dla działań są gotowe.

Dwa działania można połączyć przez posiadanie wyjściowego zestawu danych jednego działania jako zestawu danych wejściowych innego działania. Drugie działanie jest wykonywane tylko wtedy, gdy pierwszy z nich zostanie zakończony pomyślnie.

![Łączenie działań w tym samym potoku](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

W tym przykładzie potok ma dwie działania: zakończeniu i Activity2. Zakończeniu Pobiera pozycję DataSet1 jako dane wejściowe i generuje Dataset2 danych wyjściowych. Działanie przyjmuje Dataset2 jako dane wejściowe i tworzy Dataset3 wyjściowy. Ponieważ dane wyjściowe elementu zakończeniu (Dataset2) to dane wejściowe Activity2, Activity2 działa tylko po pomyślnym zakończeniu działania i wygeneruje wycinka Dataset2. Jeśli zakończeniu nie powiedzie się z jakiegoś powodu i nie produkuje wycinka Dataset2, działanie 2 nie zostanie uruchomione dla tego wycinka (na przykład: 9 AM do 10 AM).

Można również tworzyć łańcuch działań, które znajdują się w różnych potokach.

![Łączenie działań w dwóch potokach](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

W tym przykładzie Pipeline1 ma tylko jedno działanie, które pobiera pozycję DataSet1 jako dane wejściowe i generuje Dataset2 jako dane wyjściowe. Pipeline2 ma także tylko jedno działanie, które przyjmuje Dataset2 jako dane wejściowe i Dataset3 jako dane wyjściowe.

Aby uzyskać więcej informacji, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Tworzenie i monitorowanie potoków
Potoki można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK.

- Kreator kopiowania
- Visual Studio
- Azure PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków przy użyciu jednego z tych narzędzi lub zestawów SDK, zobacz następujące samouczki.

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku za pomocą działania przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu/wdrożeniu potoku możesz zarządzać potokami i monitorować je za pomocą Azure Portalych lub monitorować aplikacje oraz zarządzać nimi. Instrukcje krok po kroku znajdują się w następujących tematach.

- [Monitorowanie potoków i zarządzanie nimi przy użyciu Azure Portalch](data-factory-monitor-manage-pipelines.md).
- [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Potok jednorazowej
Można utworzyć i zaplanować okresowe uruchamianie potoku (na przykład: co godzinę lub codziennie) w czasie rozpoczęcia i zakończenia określonych w definicji potoku. Szczegóły można znaleźć w temacie Planowanie działań. Możesz również utworzyć potok, który jest uruchamiany tylko raz. W tym celu należy ustawić właściwość **pipelinemode** w definicji potoku na **jednorazowej** , jak pokazano w poniższym przykładzie JSON. Wartość domyślna tej właściwości jest **zaplanowana**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

. Weź pod uwagę następujące kwestie:

* Nie określono czasu **rozpoczęcia** i **zakończenia** potoku.
* Określono **dostępność** zestawów danych wejściowych i wyjściowych (**częstotliwość** i **interwał**), mimo że Data Factory nie używa wartości.
* W widoku diagramu nie są wyświetlane jednokrotne potoki. To zachowanie jest celowe.
* Potoki jednorazowe nie mogą zostać zaktualizowane. Można sklonować potok jednorazowy, zmienić jego nazwę, zaktualizować właściwości i wdrożyć go w celu utworzenia kolejnego.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) .
- Aby uzyskać więcej informacji o planowaniu i wykonywaniu potoków, zobacz [Planowanie i wykonywanie w artykule Azure Data Factory](data-factory-scheduling-and-execution.md) .
