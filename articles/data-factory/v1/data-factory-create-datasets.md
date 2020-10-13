---
title: Tworzenie zestawów danych w Azure Data Factory
description: Dowiedz się, jak tworzyć zestawy danych w Azure Data Factory, z przykładami korzystającymi z właściwości, takich jak offset i anchorDateTime.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: ddb99fd7a7ce8265a6e9c63555cd6a226caacc4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440732"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Zestawy danych w Azure Data Factory (wersja 1)
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-create-datasets.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [zestawy danych w wersji 2](../concepts-datasets-linked-services.md).

W tym artykule opisano zestawy danych, sposób ich definiowania w formacie JSON oraz sposób ich użycia w potokach Azure Data Factory. Zawiera szczegółowe informacje o każdej sekcji (na przykład strukturę, dostępność i zasady) w definicji JSON zestawu danych. Artykuł zawiera również przykłady użycia właściwości **przesunięcia**, **anchorDateTime**i **stylu** w definicji JSON zestawu danych.

> [!NOTE]
> Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](data-factory-introduction.md) przegląd. Jeśli nie masz praktycznego doświadczenia w tworzeniu fabryk danych, możesz uzyskać lepsze zrozumienie, odczytując [Samouczek dotyczący transformacji danych](data-factory-build-your-first-pipeline.md) i [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku określają akcje do wykonania na danych. Można na przykład użyć działania kopiowania w celu skopiowania danych z bazy danych SQL Server do usługi Azure Blob Storage. Następnie możesz użyć działania programu Hive, które uruchamia skrypt programu Hive w klastrze usługi Azure HDInsight, aby przetwarzać dane z magazynu obiektów BLOB w celu utworzenia danych wyjściowych. Na koniec możesz użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse), na podstawie których są tworzone rozwiązania do raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania w Azure Data Factory](data-factory-create-pipelines.md).

Działanie może pobrać zero lub więcej wejściowych **zestawów**danych i utworzyć co najmniej jeden wyjściowy zestaw danych. Wejściowy zestaw danych reprezentuje dane wejściowe dla działania w potoku, a wyjściowy zestaw danych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder w usłudze BLOB Storage, z których potok ma odczytywać dane.

Przed utworzeniem zestawu danych Utwórz **połączoną usługę** , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zestawy danych identyfikują dane w połączonych magazynach danych, takich jak tabele SQL, pliki, foldery i dokumenty. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder zawierający wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do SQL Database, należy utworzyć dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: DataSet usługi Azure BLOB (który odwołuje się do połączonej usługi Azure Storage) i zestawu danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Azure Storage i Azure SQL Database zawierają parametry połączenia, które Data Factory używane w środowisku uruchomieniowym do nawiązywania połączenia z usługą Azure Storage i Azure SQL Database. Zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB. Zestaw danych tabeli SQL Azure określa tabelę SQL w bazie danych SQL, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Zestaw danych JSON
Zestaw danych w Data Factory jest zdefiniowany w formacie JSON w następujący sposób:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| name |Nazwa zestawu danych. Zobacz [reguły](data-factory-naming-rules.md) nazewnictwa Azure Data Factory zasad nazewnictwa. |Tak |Nie dotyczy |
| typ |Typ zestawu danych. Określ jeden z typów obsługiwanych przez Data Factory (na przykład: AzureBlob, wartość azuresqltable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Typ zestawu danych](#Type). |Tak |Nie dotyczy |
| — struktura |Schemat zestawu danych.<br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Struktura zestawu danych](#Structure). |Nie |Nie dotyczy |
| typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, Azure SQL Table). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#Type). |Tak |Nie dotyczy |
| external | Flaga logiczna określająca, czy zestaw danych jest jawnie tworzony przez potok fabryki danych, czy nie. Jeśli wejściowy zestaw danych dla działania nie jest tworzony przez bieżący potok, należy ustawić tę flagę na wartość true. Ustaw tę flagę na wartość true dla wejściowego zestawu danych pierwszego działania w potoku.  |Nie |fałsz |
| availability | Definiuje przedział czasu przetwarzania (na przykład co godzinę lub codziennie) lub model odcięć dla środowiska produkcyjnego zestawu danych. Każda jednostka danych zużywana i generowana przez uruchomienie działania jest nazywana wycinkem danych. Jeśli dostępność wyjściowego zestawu danych jest ustawiona na codziennie (częstotliwość-dzień, interwał-1), wycinek jest tworzony codziennie. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz Dostępność zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje na temat modelu odcinania zestawu danych, zobacz artykuł dotyczący [planowania i wykonywania](data-factory-scheduling-and-execution.md) . |Tak |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który musi spełniać wycinki zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz sekcję [zasady zestawu danych](#Policy) . |Nie |Nie dotyczy |

## <a name="dataset-example"></a>Przykład zestawu danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie **MyTable** w bazie danych SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Pamiętaj o następujących kwestiach:

* **Typ** jest ustawiony na wartość azuresqltable.
* Właściwość **TableName** Type (określona dla typu wartość azuresqltable) ma ustawioną wartość MyTable.
* **linkedServiceName** odwołuje się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym FRAGMENCIE kodu JSON.
* **częstotliwość dostępności** jest ustawiona na dzień, a **Interwał** jest ustawiony na 1. Oznacza to, że wycinek zestawu danych jest tworzony codziennie.

**AzureSqlLinkedService** jest zdefiniowany w następujący sposób:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

We wcześniejszym fragmencie kodu JSON:

* **Typ** jest ustawiony na AzureSqlDatabase.
* Właściwość typu **ConnectionString** określa informacje umożliwiające nawiązanie połączenia z bazą danych SQL.

Jak widać, połączona usługa definiuje sposób nawiązywania połączenia z bazą danych SQL. Zestaw danych określa, która tabela jest używana jako dane wejściowe i wyjściowe dla działania w potoku.

> [!IMPORTANT]
> Chyba że zestaw danych jest generowany przez potok, powinien być oznaczony jako **zewnętrzny**. To ustawienie ma zazwyczaj zastosowanie do danych wejściowych pierwszego działania w potoku.

## <a name="dataset-type"></a><a name="Type"></a> Typ zestawu danych
Typ zestawu danych zależy od używanego magazynu danych. Listę magazynów danych obsługiwanych przez Data Factory można znaleźć w poniższej tabeli. Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączoną usługę i zestaw danych dla tego magazynu danych.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych z programem * mogą być lokalne lub oparte na infrastrukturze platformy Azure jako usługa (IaaS). Te magazyny danych wymagają zainstalowania [bramy zarządzanie danymi](data-factory-data-management-gateway.md).

W przykładzie w poprzedniej sekcji Typ zestawu danych jest ustawiony na **wartość azuresqltable**. Podobnie w przypadku zestawu danych obiektów blob platformy Azure Typ zestawu danych jest ustawiany na **AzureBlob**, jak pokazano w poniższym kodzie JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Struktura zestawu danych
Sekcja **struktury** jest opcjonalna. Definiuje schemat zestawu danych, zawierający kolekcję nazw i typów danych kolumn. Sekcja struktury służy do dostarczania informacji o typie, które są używane do konwersji typów i mapowania kolumn ze źródła do miejsca docelowego. W poniższym przykładzie zestaw danych ma trzy kolumny: `slicetimestamp` , `projectname` , i `pageviews` . Są one odpowiednio typu String, String i Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Każda kolumna w strukturze zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| typ |Typ danych kolumny.  |Nie |
| kultura |. Kultura oparta na sieci, która ma być używana, gdy typem jest typ .NET: `Datetime` lub `Datetimeoffset` . Wartość domyślna to `en-us`. |Nie |
| format |Ciąg formatu, który ma być używany, gdy typ jest typem .NET: `Datetime` lub `Datetimeoffset` . |Nie |

Poniższe wskazówki ułatwiają określenie, kiedy należy uwzględnić informacje o strukturze, i co należy uwzględnić w sekcji **struktury** .

* **W przypadku strukturalnych źródeł danych**Określ sekcję struktury tylko wtedy, gdy chcesz, aby kolumny źródłowe były mapowane na kolumny, a ich nazwy nie są takie same. Ten rodzaj strukturalnego źródła danych przechowuje informacje o schemacie i typach danych wraz z samymi danymi. Przykłady strukturalnych źródeł danych obejmują SQL Server, Oracle i Azure Table.
  
    Ponieważ informacje o typie są już dostępne dla strukturalnych źródeł danych, nie należy uwzględniać informacji o typie, gdy zostanie uwzględniona sekcja struktury.
* W **przypadku schematu dotyczącego odczytywania źródeł danych (w przypadku usługi BLOB Storage)** można wybrać przechowywanie danych bez zapisywania jakichkolwiek schematów lub informacji o typie danych. W przypadku tych typów źródeł danych należy uwzględnić strukturę, gdy chcesz mapować kolumny źródłowe na kolumny ujścia. Uwzględnij także strukturę, gdy zestaw danych jest danymi wejściowymi dla działania kopiowania, a typy danych źródłowego elementu DataSet powinny być konwertowane na typy natywne dla ujścia.
    
    Data Factory obsługuje następujące wartości dla udostępniania informacji o typie w strukturze: **Int16, Int32, Int64, Single, Double, decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset i TimeSpan**. Te wartości są zgodne z Common Language Specification (CLS),. Wartości typu w sieci.

Data Factory automatycznie wykonuje konwersje typów podczas przesuwania danych ze źródłowego magazynu danych do magazynu danych ujścia.

## <a name="dataset-availability"></a>Dostępność zestawu danych
Sekcja **dostępność** w zestawie danych definiuje przedział czasu przetwarzania (na przykład co godzinę, codziennie lub co tydzień) dla zestawu danych. Aby uzyskać więcej informacji o oknach działań, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md).

W poniższej sekcji dostępności określono, że wyjściowy zestaw danych jest generowany co godzinę, lub wejściowy zestaw danych jest dostępny co godzinę:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Jeśli potok ma następujące czasy rozpoczęcia i zakończenia:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Wyjściowy zestaw danych jest generowany co godzinę w czasie rozpoczęcia i zakończenia potoku. W związku z tym, istnieje pięć wycinków zestawu danych produkowanych przez ten potok, po jednym dla każdego okna działania (12 AM-1 AM, 1 AM-2 am, 2 am-3 am, 3 am-4 am).

W poniższej tabeli opisano właściwości, których można użyć w sekcji dostępność:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla produkcji wycinków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuta, godzina, dzień, tydzień, miesiąc |Tak |Nie dotyczy |
| interval |Określa mnożnik dla częstotliwości.<br/><br/>"Interwał x częstotliwości" określa, jak często wycinek jest generowany. Na przykład jeśli potrzebujesz zestawu danych, który ma być pofragmentowany co godzinę, ustawiasz <b>częstotliwość</b> na <b>godzinę</b>, a <b>Interwał</b> na <b>1</b>.<br/><br/>Należy pamiętać, że jeśli określisz **częstotliwość** jako **minutę**, należy ustawić interwał na nie mniej niż 15. |Tak |Nie dotyczy |
| styl |Określa, czy wycinek ma być tworzony na początku, czy na końcu interwału.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Jeśli **częstotliwość** jest ustawiona na **miesiąc**, a dla opcji **styl** ustawiono wartość **EndOfInterval**, wycinek zostanie utworzony w ostatnim dniu miesiąca. Jeśli **styl** jest ustawiony na **StartOfInterval**, wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli **częstotliwość** jest ustawiona na **dzień**, a **styl** jest ustawiony na **EndOfInterval**, wycinek zostanie utworzony w ciągu ostatniej godziny dnia.<br/><br/>Jeśli **częstotliwość** jest ustawiona na **godzinę**, a **styl** jest ustawiony na **EndOfInterval**, wycinek jest generowany na końcu godziny. Na przykład dla wycinka dla okresu 1 PM-2 PM wycinek jest generowany na 2 PM. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanym przez harmonogram do obliczania granic wycinków zestawu danych. <br/><br/>Należy pamiętać, że jeśli ta właściwość ma części daty, które są bardziej szczegółowe niż określona częstotliwość, bardziej szczegółowe części są ignorowane. Jeśli na przykład **Interwał** ma wartość **co godzinę** (częstotliwość: godzina i interwał: 1), a **anchorDateTime** zawiera **minuty i sekundy**, wówczas części minut i sekund wartości **anchorDateTime** są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Przedział czasu, przez który początek i koniec wszystkich wycinków zestawu danych są przesunięte. <br/><br/>Należy pamiętać, że jeśli określono zarówno **anchorDateTime** , jak i **przesunięcie** , wynik jest połączonym przesunięciem. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie dzienne ( `"frequency": "Day", "interval": 1` ) wycinki zaczynają się od 12 am (północ) uniwersalny czas koordynowany (UTC). Jeśli chcesz, aby godzina rozpoczęcia była 6 czasem UTC, Ustaw przesunięcie, tak jak pokazano w poniższym fragmencie kodu:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest generowany co 23 godziny. Pierwszy wycink rozpocznie się o czas określony przez **anchorDateTime**, który jest ustawiony na `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>przykład przesunięcia/stylu
Następujący zestaw danych jest co miesiąc i jest tworzony w trzeciej części każdego miesiąca o 8:00 AM ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Zasady zestawu danych
Sekcja **zasady** w definicji zestawu danych definiuje kryteria lub warunek, który musi spełniać wycinki zestawu danych.

### <a name="validation-policies"></a>Zasady walidacji
| Nazwa zasady | Opis | Zastosowane do | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **usłudze Azure Blob Storage** spełniają minimalne wymagania dotyczące rozmiaru (w megabajtach). |Azure Blob Storage |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazie danych SQL Azure** lub w **tabeli platformy Azure** zawierają minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Zewnętrzne zestawy danych
Zewnętrzne zestawy danych to te, które nie są tworzone przez uruchomiony potok w fabryce Data Factory. Jeśli zestaw danych jest oznaczony jako **zewnętrzny**, zasady **ExternalDataymi** mogą być zdefiniowane w celu wpływania na zachowanie dostępności wycinka zestawu danych.

Jeśli zestaw danych nie jest tworzony przez Data Factory, powinien być oznaczony jako **zewnętrzny**. To ustawienie ma zazwyczaj zastosowanie do danych wejściowych pierwszego działania w potoku, chyba że jest używany łańcuch aktywności lub potoku.

| Nazwa | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| datadelay |Czas oczekiwania na sprawdzenie dostępności danych zewnętrznych dla danego wycinka. Na przykład można opóźnić sprawdzanie godzinowe za pomocą tego ustawienia.<br/><br/>To ustawienie dotyczy tylko obecnego czasu. Na przykład jeśli jest to 1:00 PM teraz, a ta wartość wynosi 10 minut, sprawdzanie poprawności rozpocznie się o 1:10 PM.<br/><br/>Należy zauważyć, że to ustawienie nie ma wpływu na wycinki w przeszłości. Wycinki z dataopóźnieniem **czasu zakończenia wycinka**  +  **dataDelay**  <  są**teraz** przetwarzane bez opóźnień.<br/><br/>Czasy większe niż 23:59 godzin należy określić przy użyciu `day.hours:minutes:seconds` formatu. Na przykład, aby określić 24 godziny, nie należy używać 24:00:00. Zamiast tego należy użyć 1,00:00:00. Jeśli używasz 24:00:00, jest on traktowany jako 24 dni (24.00:00:00). Przez 1 dzień i 4 godziny należy określić 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarią a kolejną próbą. To ustawienie dotyczy obecnego czasu. Jeśli poprzednia próba zakończyła się niepowodzeniem, następna próba będzie późniejsza po okresie **retryInterval** . <br/><br/>Jeśli teraz jest 1:00 PM, rozpoczynamy pierwszą próbę. Jeśli czas trwania pierwszego sprawdzania poprawności wynosi 1 minuta, a operacja nie powiodła się, kolejna ponowna próba jest równa 1:00 + 1 min (czas trwania) + 1 min (interwał ponawiania prób) = 1:02 PM. <br/><br/>W przypadku wycinków w przeszłości nie ma opóźnień. Ponowna próba nastąpi natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdej próbnej próby.<br/><br/>Jeśli ta właściwość ma wartość 10 minut, walidacja powinna zostać zakończona w ciągu 10 minut. Jeśli sprawdzanie poprawności będzie możliwe dopiero po upływie 10 minut, ponów próbę.<br/><br/>Jeśli wszystkie próby sprawdzania poprawności przekroczą limit czasu, wycinek zostanie oznaczony jako **TimedOut**. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba przypadków sprawdzania dostępności danych zewnętrznych. Maksymalna dozwolona wartość to 10. |Nie |3 |


## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK:

- Kreator kopiowania
- Visual Studio
- PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz następujące samouczki, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawu SDK:

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku za pomocą działania przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu i wdrożeniu potoku możesz zarządzać potokami i monitorować je za pomocą Azure Portalych lub aplikacji do monitorowania i zarządzania. Instrukcje krok po kroku znajdują się w następujących tematach:

- [Monitorowanie potoków i zarządzanie nimi przy użyciu Azure Portal](data-factory-monitor-manage-pipelines.md)
- [Monitorowanie potoków i zarządzanie nimi przy użyciu aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Zestawy danych z zakresem
Zestawy danych, które są objęte zakresem do potoku, można tworzyć przy użyciu właściwości **DataSets** . Te zestawy danych mogą być używane tylko przez działania w ramach tego potoku, a nie przez działania w innych potokach. W poniższym przykładzie zdefiniowano potok z dwoma zestawami danych (InputDataset-RDC i OutputDataset-RDC), które mają być używane w potoku.

> [!IMPORTANT]
> Zestawy danych należące do zakresu są obsługiwane tylko w przypadku potoków jednorazowych (gdzie **potokmode** ma wartość **jednorazowej**). Aby uzyskać szczegółowe informacje, zobacz [potok jednorazowej](data-factory-create-pipelines.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat potoków, zobacz [Tworzenie potoków](data-factory-create-pipelines.md).
- Aby uzyskać więcej informacji o planowaniu i wykonywaniu potoków, zobacz [Planowanie i wykonywanie w Azure Data Factory](data-factory-scheduling-and-execution.md).
