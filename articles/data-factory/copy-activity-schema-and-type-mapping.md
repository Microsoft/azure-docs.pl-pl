---
title: Mapowanie schematu i typu danych w działaniu kopiowania
description: Dowiedz się więcej o tym, jak działa kopiowanie w Azure Data Factory mapuje schematy i typy danych z danych źródłowych na dane ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: ce7c0cba4a231fbdb33679f8cdac7d57c79845f5
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968878"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Mapowanie schematu i typu danych w działaniu kopiowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób, w jaki działanie Copy Azure Data Factory wykonuje mapowanie schematu i mapowanie typu danych z danych źródłowych na dane ujścia.

## <a name="schema-mapping"></a>Mapowanie schematu

### <a name="default-mapping"></a>Mapowanie domyślne

Domyślnie działanie Copy mapuje dane źródłowe na ujścia **według nazw kolumn** w sposób uwzględniający wielkość liter. Jeśli ujścia nie istnieje, na przykład zapisu do plików, nazwy pól źródłowych zostaną utrwalone jako nazwy ujścia. Takie mapowanie domyślne obsługuje elastyczne schematy i dryf ze źródła, aby ujścia od wykonania do wykonania — wszystkie dane zwrócone przez magazyn danych źródłowych można skopiować do ujścia.

Jeśli źródło jest plikiem tekstowym bez wiersza nagłówka, wymagane jest [jawne mapowanie](#explicit-mapping) , ponieważ źródło nie zawiera nazw kolumn.

### <a name="explicit-mapping"></a>Jawne mapowanie

Można również określić jawne mapowanie, aby dostosować mapowanie kolumn/pól ze źródła do ujścia na podstawie Twoich potrzeb. Jawne mapowanie umożliwia skopiowanie tylko częściowych danych źródłowych do ujścia lub mapowanie danych źródłowych do ujścia z różnymi nazwami lub zmiana kształtu tabelarycznych/hierarchicznych danych. Działanie kopiowania:

1. Odczytuje dane ze źródła i określają schemat źródłowy.
2. Stosuje zdefiniowane mapowanie.
3. Zapisuje dane do ujścia.

Dowiedz się więcej:

- [Źródło tabelaryczne z obiektem sink tabelarycznych](#tabular-source-to-tabular-sink)
- [Źródło hierarchiczne do obiektu sink tabelaryczne](#hierarchical-source-to-tabular-sink)
- [Źródło tabelaryczne/hierarchiczne do hierarchicznego ujścia](#tabularhierarchical-source-to-hierarchical-sink)

Mapowanie można skonfigurować na karcie Data Factory tworzenia interfejsu użytkownika > operacji kopiowania — > mapowanie lub programowo określić mapowanie w właściwości działanie kopiowania — > `translator` . W `translator`  ->  `mappings` obiektach Array-> są obsługiwane następujące właściwości — > `source` i `sink` , które wskazują konkretną kolumnę/pole do mapowania danych.

| Właściwość | Opis                                                  | Wymagane |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nazwa źródła lub kolumny/pola ujścia. Zastosuj do tabelarycznego źródła i ujścia. | Tak      |
| liczbą  | Indeks kolumn. Zacznij od 1. <br>Zastosuj i wymagane w przypadku używania tekstu rozdzielanego bez wiersza nagłówka. | Nie       |
| path     | Wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia lub zamapowania. Zastosuj do hierarchicznego źródła i ujścia, na przykład Cosmos DB, MongoDB lub łączników REST.<br>W przypadku pól w obiekcie głównym ścieżka JSON zaczyna się `$` od elementu root; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` Właściwość ścieżka JSON zaczyna się od element array bez `$` . | Nie       |
| typ     | Data Factory pośredni typ danych kolumny źródłowej lub ujścia. Ogólnie rzecz biorąc nie trzeba określać ani zmieniać tej właściwości. Dowiedz się więcej o [mapowaniu typu danych](#data-type-mapping). | Nie       |
| kultura  | Kultura kolumny źródłowej lub ujścia. Zastosuj, gdy typ jest `Datetime` lub `Datetimeoffset` . Wartość domyślna to `en-us`.<br>Ogólnie rzecz biorąc nie trzeba określać ani zmieniać tej właściwości. Dowiedz się więcej o [mapowaniu typu danych](#data-type-mapping). | Nie       |
| format   | Ciąg formatu, który ma być używany, gdy typem jest `Datetime` lub `Datetimeoffset` . Zapoznaj się z [niestandardowymi ciągami formatu daty i godziny](/dotnet/standard/base-types/custom-date-and-time-format-strings) na potrzeby formatowania daty i godziny. Ogólnie rzecz biorąc nie trzeba określać ani zmieniać tej właściwości. Dowiedz się więcej o [mapowaniu typu danych](#data-type-mapping). | Nie       |

Poniżej znajdują się następujące właściwości obsługiwane `translator` w programie `mappings` :

| Właściwość            | Opis                                                  | Wymagane |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Zastosuj podczas kopiowania danych z hierarchicznego źródła, na przykład Cosmos DB, MongoDB lub łączników REST.<br>Jeśli chcesz wykonać iterację i wyodrębnić dane z obiektów **wewnątrz pola tablicy** o tym samym wzorcu i przekonwertować na na wiersz dla każdego obiektu, określ ścieżkę JSON tej tablicy, która ma być stosowana krzyżowo. | Nie       |

#### <a name="tabular-source-to-tabular-sink"></a>Źródło tabelaryczne z obiektem sink tabelarycznych

Na przykład, aby skopiować dane z usługi Salesforce do Azure SQL Database i jawnie zmapować trzy kolumny:

1. Na > działanie kopiowania — karta mapowanie, kliknij przycisk **Importuj schematy** , aby zaimportować schematy źródłowe i ujścia.

2. Zamapuj potrzebne pola i Wyklucz/Usuń resztę.

![Mapuj tabelarycznie do tabelaryczne](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

To samo mapowanie można skonfigurować jako następujące w ładunku działania kopiowania (zobacz `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Aby skopiować dane z rozdzielanych plików tekstowych bez wiersza nagłówka, kolumny są reprezentowane przez liczby porządkowe zamiast nazw. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Źródło hierarchiczne do obiektu sink tabelaryczne

Podczas kopiowania danych z hierarchicznego źródła do obiektu sink tabelaryczne działania kopiowania obsługują następujące możliwości:

- Wyodrębnij dane z obiektów i tablic.
- Zastosuj krzyżowo wiele obiektów z tym samym wzorcem z tablicy, w tym przypadku konwersja jednego obiektu JSON na wiele rekordów w wyniku tabelarycznym.

Aby uzyskać bardziej zaawansowaną transformację hierarchiczną do tabelaryczną, można użyć [przepływu danych](concepts-data-flow-overview.md). 

Na przykład jeśli masz źródłowy dokument MongoDB z następującą zawartością:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

I chcesz skopiować ten plik do pliku tekstowego w następującym formacie z linią nagłówka, przez spłaszczonie danych wewnątrz tablicy *(order_pd i order_price)* i sprzężenie krzyżowe ze wspólnymi informacjami głównymi *(liczba, Data i miasto)*:

| orderNumber | DataZamówienia | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Można zdefiniować takie mapowanie w interfejsie użytkownika Data Factory tworzenia:

1. Na > działanie kopiowania — karta mapowanie, kliknij przycisk **Importuj schematy** , aby zaimportować schematy źródłowe i ujścia. Jak Data Factory podczas importowania schematu przykłady najważniejszych obiektów, jeśli jakieś pola nie są wyświetlane, można je dodać do właściwej warstwy w hierarchii — na początku na istniejącej nazwie pola i wybrać opcję dodania węzła, obiektu lub tablicy.

2. Wybierz tablicę, z której chcesz wykonać iterację i wyodrębnić dane. Zostanie ono automatycznie wypełnione jako **odwołanie do kolekcji**. Uwaga dla tej operacji jest obsługiwana tylko jedna tablica.

3. Zamapuj odpowiednie pola na obiekt sink. Data Factory automatycznie określa odpowiednie ścieżki JSON dla strony hierarchicznej.

![Mapuj hierarchiczne do tabelaryczne przy użyciu interfejsu użytkownika](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Możesz również przełączyć się do **edytora zaawansowanego**, w którym to przypadku można bezpośrednio zobaczyć i edytować ścieżki JSON. Jeśli wybierzesz opcję dodania nowego mapowania w tym widoku, określ ścieżkę JSON.

![Mapowanie hierarchiczne na tabelaryczne przy użyciu edytora zaawansowanego](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

To samo mapowanie można skonfigurować jako następujące w ładunku działania kopiowania (zobacz `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Źródło tabelaryczne/hierarchiczne do hierarchicznego ujścia

Przepływ pracy użytkownika jest podobny do [hierarchicznego źródła danych ujścia tabelarycznego](#hierarchical-source-to-tabular-sink). 

Podczas kopiowania danych ze źródła tabelarycznego do obiektu sink hierarchiczne zapisywanie do tablicy w obiekcie nie jest obsługiwane.

Podczas kopiowania danych z hierarchicznego źródła do obiektu sink hierarchiczne, można dodatkowo zachować hierarchię całej warstwy, zaznaczając obiekt/tablicę i mapując do ujścia bez dotykania pól wewnętrznych.

Aby uzyskać bardziej zaawansowane przekształcenia danych, można użyć [przepływu danych](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Mapowanie Sparametryzuj

Jeśli chcesz utworzyć potok szablonowana do dynamicznego kopiowania dużej liczby obiektów, ustal, czy możesz użyć [mapowania domyślnego](#default-mapping) lub musisz zdefiniować [jawne mapowanie](#explicit-mapping) dla odpowiednich obiektów.

Jeśli jest potrzebne jawne mapowanie, można:

1. Zdefiniuj parametr z typem obiektu na poziomie potoku, na przykład `mapping` .

2. Sparametryzuj mapowanie: w ramach działania Kopiuj — > kartę mapowanie, wybierz opcję Dodaj zawartość dynamiczną i wybierz powyższy parametr. Ładunek działania będzie następujący:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Skonstruuj wartość do przekazania do parametru mapowania. Powinien to być cały obiekt `translator` definicji, zapoznaj się z sekcją przykłady w [jawnym mapowaniu](#explicit-mapping) . Na przykład w przypadku tabelarycznego źródła do tabelarycznej kopii ujścia należy wartość `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Mapowanie typu danych

Działanie kopiowania wykonuje typy źródłowe do mapowania typów ujścia przy użyciu następującego przepływu: 

1. Konwertuj z natywnych typów danych źródłowych na Azure Data Factory pośrednie typy danych.
2. Automatycznie przekonwertuj typ danych pośrednich w zależności od potrzeb, aby dopasować odpowiednie typy zbiorników, które mają zastosowanie do [mapowania domyślnego](#default-mapping) i [mapowania jawnego](#explicit-mapping).
3. Konwertuj z Azure Data Factory pośrednie typy danych na ujścia natywnych typów danych.

Działanie kopiowania obsługuje obecnie następujące pośrednie typy danych: Boolean, Byte, Array, DateTime, DatetimeOffset, decimal, Double, GUID, Int16, Int32, Int64, Ci, Single, String, TimeSpan, UInt16, UInt32 i UInt64.

Następujące konwersje typu danych są obsługiwane między typami tymczasowymi ze źródła do ujścia.

| Source\Sink | Wartość logiczna | Tablica bajtów | Liczba dziesiętna | Data/godzina <small>(1)</small> | Zmiennoprzecinkowe <small>(2)</small> | GUID | Liczba całkowita <small>(3)</small> | Ciąg | przedział_czasu |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Wartość logiczna     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Tablica bajtów  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Data/godzina   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Liczba dziesiętna     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Zmiennoprzecinkowe | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Liczba całkowita     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Ciąg      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| przedział_czasu    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) Data/godzina obejmuje wartości DateTime i DateTimeOffset.

(2) punkt zmiennoprzecinkowy zawiera jedną i podwójną.

(3) liczba całkowita obejmuje bajty, Byte, Int16, UInt16, Int32, UInt32, Int64 i UInt64.

> [!NOTE]
> - Obecnie taka konwersja typu danych jest obsługiwana w przypadku kopiowania między danymi tabelarycznymi. Hierarchiczne źródła/ujścia nie są obsługiwane, co oznacza, że nie istnieje konwersja typu danych zdefiniowanego przez system między typami tymczasowymi źródła i ujścia.
> - Ta funkcja działa z najnowszym modelem zestawu danych. Jeśli ta opcja nie jest widoczna z poziomu interfejsu użytkownika, spróbuj utworzyć nowy zestaw danych.

Następujące właściwości są obsługiwane w działaniu kopiowania dla konwersji typu danych (w `translator` sekcji dla tworzenia programistycznego):

| Właściwość                         | Opis                                                  | Wymagane |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Włącz nowe środowisko konwersji typu danych. <br>Wartość domyślna to false ze względu na zgodność z poprzednimi wersjami.<br><br>W przypadku nowych działań kopiowania utworzonych za pośrednictwem interfejsu użytkownika tworzenia Data Factory od momentu do czerwca 2020 ta konwersja typu danych jest domyślnie włączona w celu uzyskania najlepszego środowiska, a na karcie Mapowanie > działania kopiowania można zobaczyć poniższe ustawienia konwersji typów. <br>Aby programowo utworzyć potok, należy jawnie ustawić `typeConversion` Właściwość na wartość true, aby ją włączyć.<br>W przypadku istniejących działań kopiowania utworzonych przed udostępnieniem tej funkcji nie będzie można zobaczyć opcji konwersji typów w interfejsie użytkownika Data Factory tworzenia w celu zapewnienia zgodności z poprzednimi wersjami. | Nie       |
| typeConversionSettings           | Grupa ustawień konwersji typu. Zastosuj `typeConversion` , gdy jest ustawiony na `true` . W tej grupie są wszystkie następujące właściwości. | Nie       |
| *Systemie `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Zezwalaj na obcinanie danych podczas konwertowania danych źródłowych na ujścia przy użyciu innego typu podczas kopiowania, na przykład z Decimal do Integer, od DatetimeOffset do DateTime. <br>Wartość domyślna to True. | Nie       |
| treatBooleanAsNumber             | Traktuj wartości logiczne jako liczby, na przykład prawda jako 1.<br>Wartość domyślna to false. | Nie       |
| dateTimeFormat                   | Ciąg formatu podczas konwersji między datami bez przesunięcia strefy czasowej i ciągów, na przykład `yyyy-MM-dd HH:mm:ss.fff` .  Aby uzyskać szczegółowe informacje, zapoznaj się z [ciągami niestandardowego formatu daty i godziny](/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Nie       |
| dateTimeOffsetFormat             | Ciąg formatu podczas konwersji między datami z przesunięciem strefy czasowej i ciągami, na przykład `yyyy-MM-dd HH:mm:ss.fff zzz` .  Aby uzyskać szczegółowe informacje, zapoznaj się z [ciągami niestandardowego formatu daty i godziny](/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Nie       |
| timeSpanFormat                   | Ciąg formatu w czasie konwersji między okresami i ciągami, na przykład `dd\.hh\:mm` . Aby uzyskać szczegółowe informacje, zobacz [Niestandardowe ciągi formatujące TimeSpan](/dotnet/standard/base-types/custom-timespan-format-strings) . | Nie       |
| kultura                          | Informacje o kulturze, które mają być używane podczas konwersji typów, na przykład `en-us` lub `fr-fr` . | Nie       |

**Przykład:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Starsze modele

> [!NOTE]
> Następujące modele mapowania kolumn źródłowych/pól do obiektu ujścia są nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu wspomnianego w [mapowaniu schematu](#schema-mapping). Interfejs użytkownika tworzenia Data Factory został przełączony w celu wygenerowania nowego modelu.

### <a name="alternative-column-mapping-legacy-model"></a>Alternatywne Mapowanie kolumn (starszy model)

Możesz określić działanie kopiowania — > `translator`  ->  `columnMappings` , aby mapować między danymi w kształcie tabelarycznym. W takim przypadku sekcja "Structure" jest wymagana zarówno dla zestawów danych wejściowych, jak i wyjściowych. Mapowanie kolumn obsługuje **mapowanie wszystkich lub podzbioru kolumn w źródłowym zestawie danych "struktura" na wszystkie kolumny w zestawie danych ujścia "Structure**". Poniżej przedstawiono warunki błędów, które powodują wyjątek:

- Wynik zapytania źródłowego magazynu danych nie zawiera nazwy kolumny określonej w sekcji "struktura" wejściowego zestawu danych.
- Magazyn danych ujścia (jeśli ze wstępnie zdefiniowanym schematem) nie ma nazwy kolumny określonej w sekcji "struktura" wyjściowego zestawu danych.
- Mniejsza liczba kolumn lub więcej kolumn w strukturze zestawu danych ujścia, niż określono w mapowaniu.
- Zduplikowane mapowanie.

W poniższym przykładzie wejściowy zestaw danych ma strukturę i wskazuje tabelę w lokalnej bazie danych Oracle.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

W tym przykładzie wyjściowy zestaw danych ma strukturę i wskazuje tabelę w Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Poniższy kod JSON definiuje działanie kopiowania w potoku. Kolumny ze źródła zamapowane na kolumny w usłudze ujścia przy użyciu właściwości **translatora** obiektów  ->   .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Jeśli używasz składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` w celu określenia mapowania kolumn, jest ono nadal obsługiwane.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternatywne mapowanie schematu (starszy model)

Możesz określić działanie kopiowania — > `translator`  ->  `schemaMapping` , aby mapować między danymi w kształcie hierarchicznym a danymi w formie tabelarycznej, na przykład skopiuj z MongoDB/REST do pliku tekstowego i skopiuj go z programu Oracle do Azure Cosmos DB API dla MongoDB. W sekcji działania kopiowania są obsługiwane następujące właściwości `translator` :

| Właściwość            | Opis                                                  | Wymagane |
| :------------------ | :----------------------------------------------------------- | :------- |
| typ                | Właściwość Type translatora działania kopiowania musi być ustawiona na wartość: **TabularTranslator** | Tak      |
| schemaMapping       | Kolekcja par klucz-wartość, która reprezentuje relację mapowania **ze strony źródłowej do ujścia**.<br/>- **Klucz:** reprezentuje źródło. Dla **źródła tabelarycznego** Określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych. dla **źródła hierarchicznego** Określ wyrażenie ścieżki JSON dla każdego pola, które ma zostać wyodrębnione i zamapowane.<br>- **Wartość:** reprezentuje obiekt sink. W przypadku **ujścia tabelarycznego** należy określić nazwę kolumny, zgodnie z definicją w strukturze zestawu danych. dla **obiektu ujścia hierarchiczne** Określ wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia i mapowania. <br>W przypadku danych hierarchicznych dla pól w obszarze obiekt główny ścieżka JSON rozpoczyna się od elementu głównego $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` Właściwość ścieżka JSON zaczyna się od elementu Array. | Tak      |
| collectionReference | Jeśli chcesz wykonać iterację i wyodrębnić dane z obiektów **wewnątrz pola tablicy** o tym samym wzorcu i przekonwertować na na wiersz dla każdego obiektu, określ ścieżkę JSON tej tablicy, która ma być stosowana krzyżowo. Ta właściwość jest obsługiwana tylko wtedy, gdy dane hierarchiczne są źródłem. | Nie       |

**Przykład: Kopiuj z MongoDB do Oracle:**

Na przykład jeśli masz dokument MongoDB o następującej zawartości:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

i chcesz skopiować go do tabeli Azure SQL w następującym formacie, przez spłaszczenie danych wewnątrz tablicy *(order_pd i order_price)* i sprzężenie krzyżowe ze wspólnymi informacjami głównymi *(liczba, Data i miasto)*:

| orderNumber | DataZamówienia | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Skonfiguruj regułę mapowania schematu jako przykład JSON działania kopiowania:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
