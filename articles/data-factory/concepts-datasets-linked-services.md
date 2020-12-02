---
title: Zestawy danych
description: Dowiedz się więcej o zestawach danych w Data Factory. Zestawy danych reprezentują dane wejściowe/wyjściowe.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 6a9de6ff174adc5f6be9647560ad4e26367cb4ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500125"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


W tym artykule opisano zestawy danych, sposób ich definiowania w formacie JSON oraz sposób ich użycia w potokach Azure Data Factory.

Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](introduction.md) przegląd.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku określają akcje do wykonania na danych. Teraz **zestaw** danych to nazwany widok zawierający dane, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Blob Storage, z których działanie ma odczytywać dane.

Przed utworzeniem zestawu danych należy utworzyć [**połączoną usługę**](concepts-linked-services.md) , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zastanów się to w ten sposób; DataSet reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, które zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do SQL Database, należy utworzyć dwie połączone usługi: Azure Blob Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: rozdzielany tekst tekstowy (który odnosi się do połączonej usługi Blob Storage Azure, przy założeniu, że masz pliki tekstowe jako źródło) i zestaw danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Blob Storage i Azure SQL Database platformy Azure zawierają Data Factory parametry połączenia używane w środowisku uruchomieniowym do nawiązywania połączeń z usługą Azure Storage i Azure SQL Database odpowiednio. Rozdzielany tekst zestaw danych Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB wraz z ustawieniami związanymi z formatowaniem. Zestaw danych tabeli SQL Azure określa tabelę SQL w SQL Database, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Zestaw danych JSON
Zestaw danych w Data Factory jest zdefiniowany w następującym formacie JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
typ | Typ zestawu danych. Określ jeden z typów obsługiwanych przez Data Factory (na przykład: DelimitedText, wartość azuresqltable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [typy zestawów danych](#dataset-type). | Tak |
schema | Schemat zestawu danych, reprezentujący fizyczny typ danych i kształt. | Nie |
typeProperties | Właściwości typu są różne dla każdego typu. Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#dataset-type). | Tak |

Podczas importowania schematu zestawu danych wybierz przycisk **Importuj schemat** , a następnie wybierz pozycję Importuj ze źródła lub z pliku lokalnego. W większości przypadków schemat zostanie zaimportowany bezpośrednio ze źródła. Jeśli jednak masz już lokalny plik schematu (plik Parquet lub CSV z nagłówkami), możesz skierować Data Factory, aby oprzeć schemat na tym pliku.

W działaniu kopiowania zestawy danych są używane w źródle i ujścia. Schemat zdefiniowany w elemencie DataSet jest opcjonalny jako odwołanie. Jeśli chcesz zastosować Mapowanie kolumn/pól między źródłem i ujściam, zapoznaj się z [mapowaniem schematu i typu](copy-activity-schema-and-type-mapping.md).

W przepływie danych zbiory są używane w transformacje źródłowe i ujścia. Zestawy danych definiują schematy podstawowe dane. Jeśli dane nie zawierają schematu, można użyć dryfu schematu dla źródła i ujścia. Metadane z zestawów danych pojawiają się w transformacji źródłowej jako projekcja źródłowa. Projekcja w transformacji źródłowej reprezentuje dane przepływu danych ze zdefiniowanymi nazwami i typami.

## <a name="dataset-type"></a>Typ zestawu danych

Azure Data Factory obsługuje wiele różnych typów zestawów danych, w zależności od używanych magazynów. Listę magazynów danych obsługiwanych przez Data Factory można znaleźć w artykule [Omówienie łączników](connector-overview.md) . Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączoną usługę i zestaw danych dla niego.

Na przykład w przypadku zestawu danych tekstu rozdzielanego Typ zestawu danych jest ustawiany na **DelimitedText** , jak pokazano w następującym przykładzie JSON:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

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
