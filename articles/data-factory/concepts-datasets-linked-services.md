---
title: Zestawy danych
description: Dowiedz się więcej o zestawach danych w fabryce danych. Zestawy danych reprezentują dane wejściowe/wyjściowe.
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
ms.openlocfilehash: 33b2ca8db75acff1ce423aa50087961cce6092b2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418409"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


W tym artykule opisano, jakie są zestawy danych, jak są definiowane w formacie JSON i jak są używane w potokach usługi Azure Data Factory.

Jeśli jesteś nowy w fabryce danych, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md) dla przeglądu.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logiczne grupowanie **działań,** które razem wykonać zadanie. Działania w potoku definiują akcje do wykonania na danych. Teraz **zestaw danych** jest nazwany widok danych, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Blob Storage, z których działanie ma odczytywać dane.

Przed utworzeniem zestawu danych należy utworzyć [**połączony serwis,**](concepts-linked-services.md) aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Pomyśl o tym w ten sposób; zestaw danych reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład usługa połączona usługi Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów Blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta magazynu platformy Azure, który zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do bazy danych SQL, należy utworzyć dwie połączone usługi: Usługi Azure Storage i Azure SQL Database. Następnie utwórz dwa zestawy danych: zestaw danych obiektów Blob platformy Azure (który odwołuje się do usługi połączonej usługi Azure Storage) i zestaw danych tabeli SQL platformy Azure (który odwołuje się do połączonej usługi Azure SQL Database). Usługi połączone w usłudze Azure Storage i Azure SQL Database zawierają parametry połączenia używane przez usługę Data Factory w czasie wykonywania do łączenia się odpowiednio z usługą Azure Storage i azure SQL Database. Zestaw danych obiektów Blob platformy Azure określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów Blob. Zestaw danych tabeli SQL platformy Azure określa tabelę SQL w bazie danych SQL, do której mają być kopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, aktywnością, zestawem danych i usługą połączony w usłudze Data Factory:

![Relacja między rurociągiem, aktywnością, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Zestaw danych JSON
Zestaw danych w fabryce danych jest zdefiniowany w następującym formacie JSON:

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
W poniższej tabeli opisano właściwości w powyższym JSON:

Właściwość | Opis | Wymagany |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Tak |
type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Typy zestawów danych](#dataset-type). | Tak |
— struktura | Schemat zestawu danych. Aby uzyskać szczegółowe informacje, zobacz [Schemat zestawu danych](#dataset-structure-or-schema). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, tabela SQL platformy Azure). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#dataset-type). | Tak |

### <a name="data-flow-compatible-dataset"></a>Zestaw danych zgodny z przepływem danych



Zobacz [obsługiwane typy zestawów danych,](#dataset-type) aby uzyskać listę typów zestawów danych zgodnych z [przepływem danych.](concepts-data-flow-overview.md) Zestawy danych, które są zgodne z przepływem danych wymagają szczegółowe definicje zestawów danych dla przekształceń. W związku z tym definicja JSON jest nieco inna. Zamiast właściwości _structure_ zestawy danych, które są zgodne z przepływem danych mają właściwość _schematu._

W przepływie danych zestawy danych są używane w przekształceniach źródłowych i ujścia. Zestawy danych definiują schematy danych podstawowych. Jeśli dane nie ma schematu, można użyć dryftu schematu dla źródła i ujścia. Schemat w zestawie danych reprezentuje typ i kształt danych fizycznych.

Definiując schemat z zestawu danych, otrzymasz powiązane typy danych, formaty danych, lokalizację pliku i informacje o połączeniu z skojarzonej usługi połączonej. Metadane z zestawów danych są wyświetlane w transformacji źródła jako *projekcja źródłowa*. Projekcja w transformacji źródła reprezentuje dane przepływu danych o zdefiniowanych nazwach i typach.

Podczas importowania schematu zestawu danych przepływ danych wybierz przycisk **Importuj schemat** i wybierz opcję importowania ze źródła lub pliku lokalnego. W większości przypadków schemat zostanie zaimportować bezpośrednio ze źródła. Ale jeśli masz już lokalny plik schematu (plik parkietu lub CSV z nagłówkami), możesz skierować fabrykę danych, aby oprzeć schemat na tym pliku.


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

W poniższej tabeli opisano właściwości w powyższym JSON:

Właściwość | Opis | Wymagany |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Tak |
type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Typy zestawów danych](#dataset-type). | Tak |
Schematu | Schemat zestawu danych. Aby uzyskać szczegółowe informacje, zobacz [Zestawy danych zgodne z przepływem danych](#dataset-type). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, tabela SQL platformy Azure). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#dataset-type). | Tak |


## <a name="dataset-example"></a>Przykład zestawu danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie MyTable w bazie danych SQL.

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

- jest ustawiona na AzureSqlTable.
- właściwość typu tableName (specyficzne dla typu AzureSqlTable) jest ustawiona na MyTable.
- linkedServiceName odnosi się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym urywek JSON.

## <a name="dataset-type"></a>Typ zestawu danych
Istnieje wiele różnych typów zestawów danych, w zależności od używanego magazynu danych. Listę danych przechowywanych jest obsługiwanych przez program Data Factory from [Connector .](connector-overview.md) Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączony serwis i zestaw danych dla tego magazynu danych.

W przykładzie w poprzedniej sekcji typ zestawu danych jest ustawiony na **AzureSqlTable**. Podobnie dla zestawu danych obiektów blob platformy Azure typ zestawu danych jest ustawiony na **AzureBlob**, jak pokazano w następującym JSON:

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

## <a name="dataset-structure-or-schema"></a>Struktura zestawu danych lub schemat
Zestawy danych sekcji **sekcji struktury** lub **schematu** (zgodne z przepływem danych) są opcjonalne. Definiuje schemat zestawu danych, zawierając kolekcję nazw i typów danych kolumn. Sekcja struktury służy do dostarczania informacji o typie, który jest używany do konwertowania typów i mapowania kolumn ze źródła do miejsca docelowego.

Każda kolumna w strukturze zawiera następujące właściwości:

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa kolumny. | Tak
type | Typ danych kolumny. Fabryka danych obsługuje następujące tymczasowe typy danych jako dozwolone wartości: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset i Timespan** | Nie
kultura | . Kultura oparta na sieci, która ma być `Datetime` używana, gdy typem jest typ .NET: lub `Datetimeoffset`. Wartość domyślna to `en-us`. | Nie
format | Ciąg formatu, który ma być używany, `Datetime` `Datetimeoffset`gdy typem jest typ .NET: lub . Informacje na temat formatowania datetime można znaleźć w [obszarze Niestandardowe ciągi formatów daty i godziny.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) | Nie

### <a name="example"></a>Przykład
W poniższym przykładzie załóżmy, że źródłowe dane obiektu Blob są w formacie CSV i zawierają trzy kolumny: userid, name i lastlogindate. Są one typu Int64, String i Datetime z niestandardowym formatem datetime przy użyciu skróconych nazw francuskich dla dnia tygodnia.

Zdefiniuj strukturę zestawu danych obiektów blob w następujący sposób wraz z definicjami typów dla kolumn:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Wskazówki

Poniższe wskazówki pomagają zrozumieć, kiedy należy uwzględnić informacje o strukturze i co należy uwzględnić w sekcji **struktury.** Dowiedz się więcej o tym, jak dane źródłowe z fabryki danych mają być zatapianie i kiedy określić informacje o strukturze ze [schematu i mapowanie typów](copy-activity-schema-and-type-mapping.md).

- **W przypadku silnych źródeł danych schematu**należy określić sekcję struktury tylko wtedy, gdy kolumny źródłowe mapy mają przejaswojać kolumny, a ich nazwy nie są takie same. Ten rodzaj ustrukturyzowego źródła danych przechowuje schemat danych i wpisywać informacje wraz z samymi danymi. Przykłady ustrukturyzowanych źródeł danych obejmują SQL Server, Oracle i Azure SQL Database.<br/><br/>Ponieważ informacje o typie są już dostępne dla ustrukturyzowanych źródeł danych, nie należy dołączać informacji o typie podczas dołączania sekcji struktury.
- **Dla źródeł danych nie/słaby schemat np plik tekstowy w magazynie obiektów blob**, należy uwzględnić strukturę, gdy zestaw danych jest dane wejściowe dla działania kopiowania i typy danych źródłowego zestawu danych powinny być konwertowane na typy macierzyste dla ujścia. I dołącz strukturę, gdy chcesz mapować kolumny źródłowe do kolumn ujścia..

## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API,](quickstart-create-data-factory-rest-api.md)Szablon usługi Azure Resource Manager i witryny Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Bieżąca wersja a zestawy danych w wersji 1

Oto kilka różnic między zbiorami danych Data Factory i Data Factory w wersji 1:

- Właściwość zewnętrzna nie jest obsługiwana w bieżącej wersji. Zastępuje go [wyzwalacz.](concepts-pipeline-execution-triggers.md)
- Właściwości zasad i dostępności nie są obsługiwane w bieżącej wersji. Czas rozpoczęcia potoku zależy od [wyzwalaczy](concepts-pipeline-execution-triggers.md).
- Zestawy danych o określonym zakresie (zestawy danych zdefiniowane w potoku) nie są obsługiwane w bieżącej wersji.

## <a name="next-steps"></a>Następne kroki
Zobacz poniższy samouczek, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawów SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki start: tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki start: tworzenie fabryki danych przy użyciu interfejsu REST API](quickstart-create-data-factory-rest-api.md)
- [Szybki start: tworzenie fabryki danych przy użyciu witryny Azure portal](quickstart-create-data-factory-portal.md)
