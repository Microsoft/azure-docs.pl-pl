---
title: Zestawy danych
description: Dowiedz się więcej o zestawach danych w Data Factory. Zestawy danych reprezentują dane wejściowe/wyjściowe.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 122725bff616a49d27981b88f465e04418db9526
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826116"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano zestawy danych, sposób ich definiowania w formacie JSON oraz sposób ich użycia w potokach Azure Data Factory.

Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](introduction.md) przegląd.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Teraz **zestaw** danych to nazwany widok zawierający dane, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Blob Storage, z których działanie ma odczytywać dane.

Przed utworzeniem zestawu danych należy utworzyć [**połączoną usługę**](concepts-linked-services.md) , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zastanów się to w ten sposób; DataSet reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, które zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do SQL Database, należy utworzyć dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: DataSet usługi Azure BLOB (który odwołuje się do połączonej usługi Azure Storage) i zestawu danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Azure Storage i Azure SQL Database zawierają parametry połączenia, które Data Factory używane w środowisku uruchomieniowym do nawiązywania połączenia z usługą Azure Storage i Azure SQL Database. Zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB. Zestaw danych tabeli SQL Azure określa tabelę SQL w SQL Database, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Zestaw danych JSON
Zestaw danych w Data Factory jest zdefiniowany w następującym formacie JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [reguły nazewnictwa Azure Data Factory](naming-rules.md). |  Tak |
typ | Typ zestawu danych. Określ jeden z typów obsługiwanych przez Data Factory (na przykład: AzureBlob, wartość azuresqltable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [typy zestawów danych](#dataset-type). | Tak |
— struktura | Schemat zestawu danych. Aby uzyskać szczegółowe informacje, zobacz [schemat zestawu danych](#dataset-structure-or-schema). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, Azure SQL Table). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#dataset-type). | Tak |

### <a name="data-flow-compatible-dataset"></a>Zestaw danych zgodny z przepływem danych



Zobacz [typy obsługiwanych zestawów danych](#dataset-type) , aby uzyskać listę typów zestawów danych, które są zgodne z [przepływem danych](concepts-data-flow-overview.md) . Zestawy danych, które są zgodne ze strumieniem, wymagają szczegółowych definicji zestawu danych dla transformacji. W rezultacie definicja JSON jest nieco inna. Zamiast właściwości _struktury_ zestawy danych, które są zgodne ze przepływem, mają właściwość _Schema_ .

W przepływie danych zbiory są używane w transformacje źródłowe i ujścia. Zestawy danych definiują schematy podstawowe dane. Jeśli dane nie zawierają schematu, można użyć dryfu schematu dla źródła i ujścia. Schemat w zestawie danych reprezentuje fizyczny typ danych i kształt.

Definiując schemat z zestawu danych, uzyskasz powiązane typy danych, formaty danych, lokalizację pliku i informacje o połączeniu ze skojarzonej połączonej usługi. Metadane z zestawów danych pojawiają się w transformacji źródłowej jako *projekcja*źródłowa. Projekcja w transformacji źródłowej reprezentuje dane przepływu danych ze zdefiniowanymi nazwami i typami.

Podczas importowania schematu zestawu danych przepływu danych wybierz przycisk **Importuj schemat** , a następnie wybierz pozycję Importuj ze źródła lub z pliku lokalnego. W większości przypadków schemat zostanie zaimportowany bezpośrednio ze źródła. Jeśli jednak masz już lokalny plik schematu (plik Parquet lub CSV z nagłówkami), możesz skierować Data Factory, aby oprzeć schemat na tym pliku.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [reguły nazewnictwa Azure Data Factory](naming-rules.md). |  Tak |
typ | Typ zestawu danych. Określ jeden z typów obsługiwanych przez Data Factory (na przykład: AzureBlob, wartość azuresqltable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [typy zestawów danych](#dataset-type). | Tak |
schematy | Schemat zestawu danych. Aby uzyskać szczegółowe informacje, zobacz [zestawy danych zgodne ze strumieniem](#dataset-type). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, Azure SQL Table). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#dataset-type). | Tak |


## <a name="dataset-example"></a>Przykład zestawu danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie MyTable w SQL Database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Pamiętaj o następujących kwestiach:

- Typ jest ustawiony na wartość azuresqltable.
- Właściwość TableName Type (określona dla typu wartość azuresqltable) ma ustawioną wartość MyTable.
- linkedServiceName odwołuje się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym fragmencie kodu JSON.

## <a name="dataset-type"></a>Typ zestawu danych
Istnieje wiele różnych typów zestawów danych, w zależności od używanej pamięci. Listę przechowywanych danych obsługiwanych przez Data Factory można znaleźć w artykule [Omówienie łączników](connector-overview.md) . Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączoną usługę i zestaw danych dla tego magazynu danych.

W przykładzie w poprzedniej sekcji Typ zestawu danych jest ustawiony na **wartość azuresqltable**. Podobnie w przypadku zestawu danych obiektów blob platformy Azure Typ zestawu danych jest ustawiany na **AzureBlob**, jak pokazano w poniższym kodzie JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Struktura lub schemat zestawu danych
Zestawy danych sekcji **struktury** lub **schematu** (zgodnego ze strumieniem) są opcjonalne. Definiuje schemat zestawu danych, zawierający kolekcję nazw i typów danych kolumn. Sekcja struktury służy do dostarczania informacji o typie, które są używane do konwersji typów i mapowania kolumn ze źródła do miejsca docelowego.

Każda kolumna w strukturze zawiera następujące właściwości:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa kolumny. | Tak
typ | Typ danych kolumny. Data Factory obsługuje następujące typy danych pośrednich jako dozwolone wartości: **Int16, Int32, Int64, Single, Double, decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset i TimeSpan** | Nie
kultura | . Kultura oparta na sieci, która ma być używana, gdy typem jest typ .NET: `Datetime` lub `Datetimeoffset` . Wartość domyślna to `en-us`. | Nie
format | Ciąg formatu, który ma być używany, gdy typ jest typem .NET: `Datetime` lub `Datetimeoffset` . Zapoznaj się z [niestandardowymi ciągami formatu daty i godziny](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) na potrzeby formatowania daty i godziny. | Nie

### <a name="example"></a>Przykład
W poniższym przykładzie Załóżmy, że źródło danych obiektu BLOB jest w formacie CSV i zawiera trzy kolumny: UserID, Name i LastLoginDate. Są one typu Int64, String i DateTime z niestandardowym formatem DateTime przy użyciu skróconych nazw francuskich dla dnia tygodnia.

Zdefiniuj strukturę zestawu danych obiektów BLOB w następujący sposób wraz z definicjami typów kolumn:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Wskazówki

Poniższe wskazówki ułatwiają zrozumienie, kiedy należy uwzględnić informacje o strukturze i co należy uwzględnić w sekcji **struktury** . Dowiedz się więcej na temat sposobu, w jaki Fabryka danych mapuje dane źródłowe na ujścia i kiedy należy określić informacje o strukturze z [mapowania schematu i typu](copy-activity-schema-and-type-mapping.md).

- **W przypadku silnych źródeł danych schematu**Określ sekcję struktury tylko wtedy, gdy chcesz, aby kolumny źródłowe były mapowane na kolumny ujścia, a ich nazwy nie są takie same. Ten rodzaj strukturalnego źródła danych przechowuje informacje o schemacie i typach danych wraz z samymi danymi. Przykłady strukturalnych źródeł danych obejmują SQL Server, Oracle i Azure SQL Database.<br/><br/>Ponieważ informacje o typie są już dostępne dla strukturalnych źródeł danych, nie należy uwzględniać informacji o typie, gdy zostanie uwzględniona sekcja struktury.
- W **przypadku brakujących/słabych źródeł danych schematu na przykład plik tekstowy w usłudze BLOB Storage**, Włącz strukturę, gdy zestaw danych jest danymi wejściowymi dla działania kopiowania, a typy danych źródłowego zestawu plików powinny być konwertowane na typy natywne dla ujścia. I Uwzględnij strukturę, gdy chcesz mapować kolumny źródłowe na kolumny ujścia

## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [interfejsów API platformy .NET](quickstart-create-data-factory-dot-net.md), [programu PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager szablonu i Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Bieżąca wersja a zestawy danych w wersji 1

Poniżej przedstawiono kilka różnic między zestawami danych Data Factory i Data Factory wersja 1:

- Właściwość zewnętrzna nie jest obsługiwana w bieżącej wersji. Jest on zastępowany [wyzwalaczem](concepts-pipeline-execution-triggers.md).
- Właściwości zasad i dostępności nie są obsługiwane w bieżącej wersji. Godzina rozpoczęcia potoku zależy od [wyzwalaczy](concepts-pipeline-execution-triggers.md).
- Zestawy danych w zakresie (zestawy danych zdefiniowane w potoku) nie są obsługiwane w bieżącej wersji.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawu SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu Azure Portal](quickstart-create-data-factory-portal.md)
