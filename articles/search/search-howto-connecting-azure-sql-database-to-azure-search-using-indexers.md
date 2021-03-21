---
title: Wyszukiwanie w usłudze Azure SQL Data
titleSuffix: Azure Cognitive Search
description: Importuj dane z usługi Azure SQL Database lub wystąpienia zarządzanego SQL przy użyciu indeksatorów w celu wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze. W tym artykule opisano połączenia, konfigurację indeksatora i pozyskiwanie danych.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 04e4801c26b0ac8ef91af0b028d9dc2bb9a3cd1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358630"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Łączenie się z zawartością usługi Azure SQL i indeksowanie jej przy użyciu usługi Azure Wyszukiwanie poznawcze Indexer

Aby można było wykonywać zapytania dotyczące [indeksu wyszukiwanie poznawcze platformy Azure](search-what-is-an-index.md), musisz wypełnić je danymi. Jeśli dane są przechowywane w Azure SQL Database lub wystąpieniu zarządzanym SQL, **Usługa azure wyszukiwanie poznawcze indeksator dla Azure SQL Database** (lub **usługa Azure SQL indeksator** for Short) może zautomatyzować proces indeksowania, co oznacza, że mniej kodu do pisania i mniejszego poziomu infrastruktury należy zadbać o to.

Ten artykuł dotyczy Mechanics z użyciem [indeksatorów](search-indexer-overview.md), ale również zawiera opis funkcji dostępnych tylko w przypadku wystąpienia zarządzanego Azure SQL Database lub SQL (na przykład zintegrowane śledzenie zmian). 

Oprócz Azure SQL Database i wystąpienia zarządzanego SQL usługa Azure Wyszukiwanie poznawcze udostępnia Indeksatory [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)i [Azure Table Storage](search-howto-indexing-azure-tables.md). Aby poprosić o pomoc techniczną dla innych źródeł danych, Prześlij swoją opinię na [forum opinii na temat usługi Azure wyszukiwanie poznawcze](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indeksatory i źródła danych

**Źródło danych** określa, które dane mają być indeksowane, poświadczenia dotyczące dostępu do danych i zasady, które skutecznie identyfikują zmiany w danych (nowe, zmodyfikowane lub usunięte wiersze). Jest on definiowany jako zasób niezależny, dzięki czemu może być używany przez wiele indeksatorów.

**Indeksator** jest zasobem, który łączy pojedyncze źródło danych z dokierowanym indeksem wyszukiwania. Indeksator jest używany w następujący sposób:

* Wykonaj jednorazową kopię danych w celu wypełnienia indeksu.
* Zaktualizuj indeks ze zmianami w źródle danych zgodnie z harmonogramem.
* Uruchom na żądanie, aby zaktualizować indeks stosownie do potrzeb.

Pojedynczy indeksator może korzystać tylko z jednej tabeli lub widoku, ale można utworzyć wiele indeksatorów, jeśli chcesz wypełnić wiele indeksów wyszukiwania. Aby uzyskać więcej informacji na temat pojęć, zobacz [indeksator operacje: Typowy przepływ pracy](/rest/api/searchservice/Indexer-operations#typical-workflow).

Można skonfigurować i skonfigurować indeksator usługi Azure SQL przy użyciu:

* Kreator importu danych w [Azure Portal](https://portal.azure.com)
* Azure Wyszukiwanie poznawcze [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)
* [Interfejs API REST](/rest/api/searchservice/indexer-operations) usługi Azure wyszukiwanie poznawcze

W tym artykule będziemy używać interfejsu API REST do tworzenia **indeksatorów** i **źródeł danych**.

## <a name="when-to-use-azure-sql-indexer"></a>Kiedy używać usługi Azure SQL Indexer
W zależności od kilku czynników związanych z danymi korzystanie z usługi Azure SQL Indexer może być nieodpowiednie. Jeśli dane spełniają następujące wymagania, możesz użyć usługi Azure SQL indeksator.

| Kryteria | Szczegóły |
|----------|---------|
| Dane pochodzą z pojedynczej tabeli lub widoku | Jeśli dane są rozproszone w wielu tabelach, można utworzyć pojedynczy widok danych. Jednak w przypadku korzystania z widoku nie będzie można używać zintegrowanego wykrywania zmian SQL Server, aby odświeżyć indeks ze zmianami przyrostowymi. Aby uzyskać więcej informacji, zobacz [przechwytywanie zmienionych i usuniętych wierszy](#CaptureChangedRows) poniżej. |
| Typy danych są zgodne | Nie wszystkie typy SQL są obsługiwane w indeksie Wyszukiwanie poznawcze platformy Azure. Aby uzyskać listę, zobacz [Mapowanie typów danych](#TypeMapping). |
| Synchronizacja danych w czasie rzeczywistym nie jest wymagana | Indeksator może ponownie indeksować tabelę co najwyżej pięć minut. Jeśli dane ulegają zmianie często, a zmiany muszą być odzwierciedlone w indeksie w ciągu kilku sekund lub minut, zalecamy użycie [interfejsu API REST](/rest/api/searchservice/AddUpdate-or-Delete-Documents) lub [zestawu .NET SDK](./search-get-started-dotnet.md) do bezpośredniego wypychania zaktualizowanych wierszy. |
| Indeksowanie przyrostowe jest możliwe | Jeśli masz duży zestaw danych i planujesz uruchamianie indeksatora zgodnie z harmonogramem, platforma Azure Wyszukiwanie poznawcze musi być w stanie skutecznie identyfikować nowe, zmienione lub usunięte wiersze. Indeksowanie nieprzyrostowe jest dozwolone tylko w przypadku indeksowania na żądanie (bez harmonogramu) lub indeksowania mniej niż 100 000 wierszy. Aby uzyskać więcej informacji, zobacz [przechwytywanie zmienionych i usuniętych wierszy](#CaptureChangedRows) poniżej. |

> [!NOTE] 
> Usługa Azure Wyszukiwanie poznawcze obsługuje tylko uwierzytelnianie SQL Server. Jeśli wymagana jest obsługa uwierzytelniania przy Azure Active Directory hasła, zapoznaj się z [propozycją usługi UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Tworzenie indeksatora usługi Azure SQL

1. Utwórz źródło danych:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Parametry połączenia mogą mieć jeden z następujących formatów:
    1. Parametry połączenia można uzyskać z [Azure Portal](https://portal.azure.com); Użyj `ADO.NET connection string` opcji.
    1. Parametry połączenia tożsamości zarządzanej, które nie zawierają klucza konta o następującym formacie: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Aby użyć tych parametrów połączenia, postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia indeksatora do Azure SQL Database przy użyciu tożsamości zarządzanej](search-howto-managed-identities-sql.md).

2. Utwórz docelowy indeks Wyszukiwanie poznawcze platformy Azure, jeśli jeszcze go nie masz. Indeks można utworzyć przy użyciu [portalu](https://portal.azure.com) lub [interfejsu API tworzenia indeksu](/rest/api/searchservice/Create-Index). Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem tabeli źródłowej — zobacz [Mapowanie między typami danych wyszukiwania SQL i usług Azure poznawczej](#TypeMapping).

3. Utwórz indeksator, nadając mu nazwę i odwołujący się do źródła danych i indeksu docelowego:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Indeksator utworzony w ten sposób nie ma harmonogramu. Jest on automatycznie uruchamiany po utworzeniu. Można uruchomić je ponownie w dowolnym momencie przy użyciu żądania **uruchomienia indeksatora** :

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Można dostosować kilka aspektów zachowania indeksatora, takich jak rozmiar wsadu i liczbę dokumentów, które można pominąć przed zakończeniem wykonywania indeksatora. Aby uzyskać więcej informacji, zobacz [Create INDEKSATOR API](/rest/api/searchservice/Create-Indexer).

Może być konieczne zezwolenie usługom platformy Azure na łączenie się z bazą danych. Aby uzyskać instrukcje dotyczące sposobu, zobacz [łączenie z platformy Azure](../azure-sql/database/firewall-configure.md) .

Aby monitorować stan indeksatora i historię wykonywania (liczba elementów indeksowanych, niepowodzeń itp.), użyj żądania **stanu indeksatora** :

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

Odpowiedź powinna wyglądać podobnie do poniższego:

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

Historia wykonywania zawiera maksymalnie 50 ostatnio zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (w związku z czym najnowsze wykonanie jest najpierw w odpowiedzi).
Dodatkowe informacje na temat odpowiedzi można znaleźć w temacie [pobieranie stanu indeksatora](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Uruchamianie indeksatorów według harmonogramu
Można również rozmieocić indeksator tak, aby był uruchamiany okresowo zgodnie z harmonogramem. W tym celu należy dodać właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora. Poniższy przykład przedstawia żądanie PUT, aby zaktualizować indeksator:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametr **interwału** jest wymagany. Interwał odnosi się do czasu między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut; Najdłuższa wartość to jeden dzień. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Wzorzec dla tego elementu to: `P(nD)(T(nH)(nM))` . Przykłady: `PT15M` co 15 minut, `PT2H` przez co 2 godziny.

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Przechwytuj nowe, zmienione i usunięte wiersze

Usługa Azure Wyszukiwanie poznawcze używa **indeksowania przyrostowego** , aby uniknąć konieczności ponownego indeksowania całej tabeli lub widoku przy każdym uruchomieniu indeksatora. Usługa Azure Wyszukiwanie poznawcze udostępnia dwie zasady wykrywania zmian w celu obsługi indeksowania przyrostowego. 

### <a name="sql-integrated-change-tracking-policy"></a>Zasady Change Tracking zintegrowanego SQL
Jeśli Twoja baza danych SQL obsługuje [śledzenie zmian](/sql/relational-databases/track-changes/about-change-tracking-sql-server), zalecamy użycie **zasad Change Tracking zintegrowanych z programem SQL**. Jest to najbardziej wydajne zasady. Ponadto umożliwia ona platformie Azure Wyszukiwanie poznawcze identyfikowanie usuniętych wierszy bez konieczności dodawania jawnej kolumny "miękkie usuwanie" do tabeli.

#### <a name="requirements"></a>Wymagania 

+ Wymagania dotyczące wersji bazy danych:
  * SQL Server 2012 z dodatkiem SP3 lub nowszym, jeśli używasz SQL Server na maszynach wirtualnych platformy Azure.
  * Azure SQL Database lub wystąpienie zarządzane SQL.
+ Tylko tabele (bez widoków). 
+ W bazie danych [Włącz śledzenie zmian](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) dla tabeli. 
+ Brak złożonego klucza podstawowego (klucz podstawowy zawierający więcej niż jedną kolumnę) w tabeli.  

#### <a name="usage"></a>Użycie

Aby użyć tych zasad, Utwórz lub zaktualizuj źródło danych podobne do tego:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

W przypadku korzystania z zasad zintegrowanego śledzenia zmian w programie SQL Server nie określaj zasad wykrywania oddzielnego usuwania danych — ta zasada ma wbudowaną obsługę identyfikowania usuniętych wierszy. Jednak w przypadku usunięć do wykrycia "AUTOMAGIC" klucz dokumentu w indeksie wyszukiwania musi być taki sam jak klucz podstawowy w tabeli SQL. 

> [!NOTE]  
> Aby usunąć dużą liczbę wierszy z tabeli SQL przy użyciu [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql) , indeksator musi zostać [zresetowany](/rest/api/searchservice/reset-indexer) w celu zresetowania stanu śledzenia zmian w celu pobrania usunięć wierszy.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zasady wykrywania zmian wysokiego znaku wodnego

Ta zasada wykrywania zmian korzysta z kolumny "High-Mark" przechwytującej wersję lub godzinę ostatniej aktualizacji wiersza. Jeśli używasz widoku, musisz użyć wysokich zasad oznaczania. Kolumna znacznika limitu górnego musi spełniać poniższe wymagania.

#### <a name="requirements"></a>Wymagania 

* Wszystkie wstawienia określają wartość dla kolumny.
* Wszystkie aktualizacje elementu zmieniają również wartość kolumny.
* Wartość tej kolumny rośnie wraz z każdym wstawieniem lub aktualizacją.
* Zapytania z następującymi klauzulami WHERE i ORDER BY mogą być wykonywane efektywnie: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Zdecydowanie zalecamy użycie typu danych [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) dla kolumny znacznika limitu górnego. W przypadku użycia dowolnego innego typu danych śledzenie zmian nie gwarantuje przechwycenia wszystkich zmian w obecności transakcji wykonywanych współbieżnie przy użyciu zapytania indeksatora. W przypadku korzystania z **rowversion** w konfiguracji z replikami tylko do odczytu należy wskazać indeksator w replice podstawowej. Tylko replika podstawowa może być używana do scenariuszy synchronizacji danych.

#### <a name="usage"></a>Użycie

Aby użyć zasad oznakowania górnego, Utwórz lub zaktualizuj źródło danych w następujący sposób:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Jeśli tabela źródłowa nie ma indeksu w kolumnie znacznik górny, zapytania używane przez indeksator programu SQL mogą przekroczyć limit czasu. W szczególności `ORDER BY [High Water Mark Column]` klauzula wymaga, aby indeks działał efektywnie, gdy tabela zawiera wiele wierszy.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Jeśli używasz typu danych [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) dla kolumny ze znakiem wysokiej wody, rozważ użycie `convertHighWaterMarkToRowVersion` Ustawienia konfiguracji indeksatora. `convertHighWaterMarkToRowVersion` wykonuje dwie czynności:

* Użyj typu danych rowversion dla kolumny znacznika wysokiej rozdzielczości w zapytaniu SQL indeksatora. Użycie poprawnego typu danych zwiększa wydajność zapytań indeksatora.
* Odejmij 1 od wartości rowversion przed uruchomieniem zapytania indeksatora. Widoki z 1 do wielu sprzężeń mogą mieć wiersze ze zduplikowanymi wartościami rowversion. Odjęcie 1 gwarantuje, że zapytanie indeksatora nie trafi tych wierszy.

Aby włączyć tę funkcję, Utwórz lub zaktualizuj indeksator przy użyciu następującej konfiguracji:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Jeśli wystąpią błędy limitu czasu, można użyć `queryTimeout` Ustawienia konfiguracji indeksatora, aby ustawić limit czasu zapytania na wartość wyższą niż domyślny limit 5 minut. Na przykład aby ustawić limit czasu na 10 minut, należy utworzyć lub zaktualizować indeksator przy użyciu następującej konfiguracji:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

Można również wyłączyć `ORDER BY [High Water Mark Column]` klauzulę. Nie jest to jednak zalecane, ponieważ w przypadku przerwania wykonywania indeksatora przez błąd indeksator musi ponownie przetworzyć wszystkie wiersze, jeśli program indeksator przetworzył już prawie wszystkie wiersze o czas, który został przerwany. Aby wyłączyć `ORDER BY` klauzulę, użyj `disableOrderByHighWaterMarkColumn` Ustawienia w definicji indeksatora:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Zasady wykrywania usuwania nietrwałej kolumny usuwania
Po usunięciu wierszy z tabeli źródłowej prawdopodobnie chcesz również usunąć te wiersze z indeksu wyszukiwania. W przypadku korzystania ze zintegrowanych zasad śledzenia zmian SQL jest to konieczne. Jednak zasady śledzenia zmian o wysokiej rozdzielczości nie ułatwiają usuwania wierszy. Postępowanie

Jeśli wiersze są fizycznie usuwane z tabeli, usługa Azure Wyszukiwanie poznawcze nie ma możliwości wywnioskowania obecności rekordów, które już nie istnieją.  Można jednak użyć techniki "Soft-Delete", aby logicznie usunąć wiersze bez usuwania ich z tabeli. Dodaj kolumnę do tabeli lub widoku i Oznacz wiersze jako usunięte przy użyciu tej kolumny.

Korzystając z techniki usuwania nietrwałego, można określić zasady usuwania nietrwałego w następujący sposób podczas tworzenia lub aktualizowania źródła danych:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**SoftDeleteMarkerValue** musi być ciągiem — Użyj ciągu reprezentującego wartość rzeczywistą. Na przykład jeśli masz kolumnę liczb całkowitych, w której usunięte wiersze są oznaczone wartością 1, użyj `"1"` . Jeśli masz kolumnę BITOWą, w której usunięte wiersze są oznaczone wartością logiczną true, użyj literału ciągu `True` lub `true` , jeśli wielkość liter nie ma znaczenia.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapowanie między typami danych SQL i Azure Wyszukiwanie poznawcze
| Typ danych SQL | Dozwolone typy pól indeksu docelowego | Uwagi |
| --- | --- | --- |
| bit |EDM. Boolean, EDM. String | |
| int, smallint, tinyint |EDM. Int32, EDM. Int64, EDM. String | |
| bigint |EDM. Int64, EDM. String | |
| rzeczywiste, zmiennoprzecinkowe |EDM. Double, EDM. String | |
| smallmoney, cyfra dziesiętna pieniędzy |Edm.String |Usługa Azure Wyszukiwanie poznawcze nie obsługuje konwertowania typów dziesiętnych na EDM. Double, ponieważ spowodowałoby to utratę precyzji |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Ciąg SQL może służyć do wypełniania pola kolekcji (EDM. String), jeśli ciąg reprezentuje tablicę JSON ciągów: `["red", "white", "blue"]` |
| smalldatetime, DateTime, datetime2, Date, DateTimeOffset |EDM. DateTimeOffset, EDM. String | |
| uniqueidentifer |Edm.String | |
| geograficzne |Edm.GeographyPoint |Obsługiwane są tylko wystąpienia typu Geografia z SRID 4326 (co jest ustawieniem domyślnym) |
| rowversion |Nie dotyczy |Kolumny wiersza — wersja nie mogą być przechowywane w indeksie wyszukiwania, ale mogą być używane do śledzenia zmian |
| Time, TimeSpan, Binary, varbinary, Image, XML, geometria, typy CLR |Nie dotyczy |Nieobsługiwane |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Program SQL indeksator uwidacznia kilka ustawień konfiguracji:

| Ustawienie | Typ danych | Przeznaczenie | Wartość domyślna |
| --- | --- | --- | --- |
| queryTimeout |ciąg |Ustawia limit czasu wykonywania zapytania SQL |5 minut ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Powoduje, że zapytanie SQL używane przez zasady wysokiej rozdzielczości do pomijania klauzuli ORDER BY. Zobacz [zasady oznaczania górną wodą](#HighWaterMarkPolicy) |fałsz |

Te ustawienia są używane w `parameters.configuration` obiekcie w definicji indeksatora. Na przykład aby ustawić limit czasu zapytania na 10 minut, Utwórz lub zaktualizuj indeksator przy użyciu następującej konfiguracji:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Często zadawane pytania

**P: Czy można używać usługi Azure SQL Indexer z bazami danych SQL działającymi na maszynach wirtualnych IaaS na platformie Azure?**

Tak. Należy jednak zezwolić usłudze wyszukiwania na łączenie się z bazą danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia z indeksatora wyszukiwanie poznawcze platformy Azure do SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Czy można używać usługi Azure SQL Indexer z bazami danych SQL działającymi lokalnie?**

Nie bezpośrednio. Nie zalecamy ani nie obsługuje bezpośredniego połączenia, ponieważ wymaga to otworzenia baz danych do ruchu internetowego. Klienci pomyślnie korzystali z tego scenariusza przy użyciu technologii mostka, takich jak Azure Data Factory. Aby uzyskać więcej informacji, zobacz temat [wypychanie danych do indeksu wyszukiwanie poznawcze platformy Azure przy użyciu Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**P: Czy można używać usługi Azure SQL Indexer z bazami danych innymi niż SQL Server na platformie Azure?**

Nie. Ten scenariusz nie jest obsługiwany, ponieważ nie przetestowano indeksatora z innymi bazami danych, które nie są SQL Server.  

**P: Czy można utworzyć wiele indeksatorów uruchomionych zgodnie z harmonogramem?**

Tak. Jednak w jednym węźle może być uruchomiony tylko jeden indeksator. Jeśli potrzebujesz wielu indeksatorów uruchomionych współbieżnie, rozważ skalowanie usługi wyszukiwania do więcej niż jednej jednostki wyszukiwania.

**P: czy uruchomiony indeksator ma wpływ na obciążenie zapytania?**

Tak. Indeksator działa na jednym z węzłów usługi wyszukiwania, a zasoby tego węzła są współużytkowane przez indeksowanie i obsługę ruchu zapytań i innych żądań interfejsu API. W przypadku uruchamiania intensywnego indeksowania i obciążeń zapytań oraz wystąpienia dużej liczby błędów 503 lub wydłużenia czasu odpowiedzi warto rozważyć [skalowanie w górę usługi wyszukiwania](search-capacity-planning.md).

**P: Czy można użyć repliki pomocniczej w [klastrze trybu failover](../azure-sql/database/auto-failover-group-overview.md) jako źródła danych?**

To zależy. Do pełnego indeksowania tabeli lub widoku można użyć repliki pomocniczej. 

W przypadku indeksowania przyrostowego usługa Azure Wyszukiwanie poznawcze obsługuje dwie zasady wykrywania zmian: zintegrowane śledzenie zmian w programie SQL i wysoki znacznik wodny.

W przypadku replik tylko do odczytu SQL Database nie obsługuje zintegrowanego śledzenia zmian. W związku z tym należy używać zasad oznaczania wysokiej wody. 

Naszym standardowym zaleceniem jest użycie typu danych rowversion dla kolumny znacznika wysokiej wody. Jednak użycie rowversion zależy od `MIN_ACTIVE_ROWVERSION` funkcji, która nie jest obsługiwana w przypadku replik tylko do odczytu. W związku z tym należy wskazać indeksator do repliki podstawowej, jeśli używasz rowversion.

Jeśli spróbujesz użyć rowversion w replice tylko do odczytu, zostanie wyświetlony następujący błąd: 

"Używanie kolumny rowversion na potrzeby śledzenia zmian nie jest obsługiwane w przypadku replik dostępności pomocniczych (tylko do odczytu). Zaktualizuj źródło danych i określ połączenie z podstawową repliką dostępności. Właściwość "PreUpdate" bieżącej bazy danych ma wartość "READ_ONLY".

**P: Czy można użyć alternatywnej kolumny nierowversionej do śledzenia zmian znaku wodnego?**

Nie jest to zalecane. Tylko **rowversion** umożliwia niezawodne synchronizowanie danych. Jednak w zależności od logiki aplikacji może być bezpieczna, jeśli:

+ Można upewnić się, że podczas uruchamiania indeksatora nie ma zaległych transakcji w tabeli, która jest indeksowana (na przykład wszystkie aktualizacje tabeli są wykonywane jako partia według harmonogramu, a harmonogram usługi Azure Wyszukiwanie poznawcze indeksator jest ustawiony tak, aby uniknąć nakładania się na harmonogram aktualizacji tabeli).  

+ Okresowo należy wykonać pełne ponowne indeksowanie w celu pobrania wszystkich pominiętych wierszy.