---
title: Przeszukaj Azure Cosmos DB dane
titleSuffix: Azure Cognitive Search
description: Importuj dane z Azure Cosmos DB do indeksu z możliwością wyszukiwania w usłudze Azure Wyszukiwanie poznawcze. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: aed1aa03527481014a63c636181725b91b17a1e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003892"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Jak indeksować dane usługi Cosmos DB przy użyciu indeksatora usługi Azure Cognitive Search 

> [!IMPORTANT] 
> Interfejs API SQL jest ogólnie dostępny.
> Obsługa interfejsu API MongoDB, interfejsu API Gremlin i obsługi interfejs API Cassandra są obecnie dostępne w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Możesz zażądać dostępu do wersji zapoznawczych, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). 
> [Wersje zapoznawcze interfejsu API REST](search-api-preview.md) udostępniają te funkcje. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

> [!WARNING]
> Tylko kolekcje Cosmos DB z [zasadami indeksowania](/azure/cosmos-db/index-policy) ustawionymi na [spójne](/azure/cosmos-db/index-policy#indexing-mode) są obsługiwane przez usługę Azure wyszukiwanie poznawcze. Kolekcje indeksowania z opóźnionymi zasadami indeksowania nie są zalecane i mogą spowodować brak danych. Kolekcje z wyłączonym indeksem nie są obsługiwane.

W tym artykule opisano sposób konfigurowania [indeksatora](search-indexer-overview.md) Azure Cosmos DB w celu wyodrębnienia zawartości i przeszukiwania jej w usłudze Azure wyszukiwanie poznawcze. Ten przepływ pracy tworzy indeks Wyszukiwanie poznawcze platformy Azure i ładuje go z istniejącym tekstem wyodrębnionym z Azure Cosmos DB. 

Ze względu na to, że terminologia może być myląca, warto zauważyć, że [Azure Cosmos DB indeksowania](/azure/cosmos-db/index-overview) i [indeksowanie wyszukiwanie poznawcze platformy Azure](search-what-is-an-index.md) to różne operacje, unikatowe dla każdej usługi. Przed rozpoczęciem indeksowania usługi Azure Wyszukiwanie poznawcze baza danych Azure Cosmos DB musi już istnieć i zawierać dane.

Indeksator Cosmos DB w usłudze Azure Wyszukiwanie poznawcze umożliwia przeszukiwanie [Azure Cosmos DB elementów](../cosmos-db/databases-containers-items.md#azure-cosmos-items) dostępnych za pomocą różnych protokołów. 

+ W przypadku [interfejsu SQL API](../cosmos-db/sql-query-getting-started.md), który jest ogólnie dostępny, można użyć [portalu](#cosmos-indexer-portal), [interfejsu API REST](/rest/api/searchservice/indexer-operations)lub [zestawu .NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) do utworzenia źródła danych i indeksatora.

+ W przypadku [interfejsu API MongoDB (wersja zapoznawcza)](../cosmos-db/mongodb-introduction.md)można użyć [portalu](#cosmos-indexer-portal) lub [interfejsu API REST w wersji 2020-06-30-Preview](search-api-preview.md) , aby utworzyć źródło danych i indeksator.

+ W przypadku [interfejs API Cassandra (wersja zapoznawcza)](../cosmos-db/cassandra-introduction.md) i [interfejsu API Gremlin (](../cosmos-db/graph-introduction.md)wersja zapoznawcza) można używać tylko [interfejsu API REST w wersji 2020-06-30-Preview](search-api-preview.md) w celu utworzenia źródła danych i indeksatora.


> [!Note]
> Możesz odłożyć głos na głos użytkownika dla [interfejs API tabel](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) , jeśli chcesz go zobaczyć na platformie Azure wyszukiwanie poznawcze.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

> [!Note]
> Portal obsługuje obecnie interfejs API SQL i interfejs API MongoDB (wersja zapoznawcza).

Najprostszym sposobem indeksowania Azure Cosmos DB elementów jest użycie Kreatora w [Azure Portal](https://portal.azure.com/). Poprzez próbkowanie danych i odczytywanie metadanych w kontenerze Kreator [**importowania danych**](search-import-data-portal.md) w usłudze Azure wyszukiwanie poznawcze może utworzyć domyślny indeks, zmapować pola źródłowe na docelowe pola indeksu i załadować indeks w ramach jednej operacji. W zależności od rozmiaru i stopnia złożoności danych źródłowych można mieć indeks wyszukiwania pełnotekstowego w ciągu kilku minut.

Zalecamy używanie tego samego regionu lub lokalizacji zarówno dla Wyszukiwanie poznawcze platformy Azure, jak i Azure Cosmos DB na potrzeby małych opóźnień i aby uniknąć naliczania opłat za przepustowość.

### <a name="1---prepare-source-data"></a>1 — Przygotowywanie danych źródłowych

Należy mieć konto Cosmos DB, Azure Cosmos DB bazę danych zamapowane na interfejs API SQL, interfejs API MongoDB (wersja zapoznawcza) lub interfejs API Gremlin (wersja zapoznawcza) oraz zawartość bazy danych.

Upewnij się, że baza danych Cosmos DB zawiera dane. [Kreator importu danych](search-import-data-portal.md) odczytuje metadane i wykonuje próbkowanie danych w celu wywnioskowania schematu indeksu, ale również ładuje dane z Cosmos DB. Jeśli brakuje danych, Kreator zatrzymał się z powodu błędu "błąd podczas wykrywania schematu indeksu ze źródła danych: nie można utworzyć indeksu prototypu, ponieważ źródło danych" emptycollection "nie zwróciło żadnych danych".

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Kreatora można [uruchomić](search-import-data-portal.md) na pasku poleceń na stronie usługi Azure wyszukiwanie poznawcze lub w przypadku nawiązywania połączenia z Cosmos DB interfejsem API SQL można kliknąć pozycję **Dodaj wyszukiwanie poznawcze platformy Azure** w sekcji **Ustawienia** w lewym okienku nawigacji konta Cosmos DB.

   ![Importuj dane — polecenie w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 — Ustawianie źródła danych

Na stronie **Źródło danych** Źródło musi być **Cosmos DB** z następującymi specyfikacjami:

+ **Nazwa** to nazwa obiektu źródła danych. Po utworzeniu można wybrać go dla innych obciążeń.

+ **Konto Cosmos DB** powinno mieć jeden z następujących formatów:
    1. Podstawowe lub pomocnicze parametry połączenia z Cosmos DB w następującym formacie: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` .
        + W przypadku **kolekcji MongoDB** w wersji 3,2 i 3,6 w Azure Portal Użyj następującego formatu dla konta Cosmos DB: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + [Aby uzyskać](https://aka.ms/azure-cognitive-search/indexer-preview) dostęp do wersji zapoznawczej i zapoznać się z informacjami na temat sposobu formatowania poświadczeń, w przypadku **wykresów Gremlin i tabel Cassandra**.
    1.  Parametry połączenia tożsamości zarządzanej o następującym formacie, który nie zawiera klucza konta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` . Aby użyć tego formatu parametrów połączenia, postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia indeksatora z bazą danych Cosmos dB przy użyciu tożsamości zarządzanej](search-howto-managed-identities-cosmos-db.md).

+ **Baza danych** jest istniejącą bazą danych z konta. 

+ **Kolekcja** jest kontenerem dokumentów. Aby importowanie powiodło się, muszą istnieć dokumenty. 

+ **Zapytanie** może być puste, jeśli chcesz uzyskać wszystkie dokumenty, w przeciwnym razie możesz wprowadzić zapytanie, które wybierze podzestaw dokumentu. **Zapytanie** jest dostępne tylko dla interfejsu API SQL.

   ![Cosmos DB definicję źródła danych](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definicję źródła danych")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 — pomijanie strony "wzbogacanie zawartości" w Kreatorze

Dodawanie umiejętności poznawczych (lub wzbogacania) nie jest wymaganiem importowania. Jeśli nie ma potrzeby [dodawania wzbogacania AI](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, kliknij niebieskie przyciski u dołu strony, aby "dalej" i "Pomiń".

### <a name="5---set-index-attributes"></a>5 — Ustawianie atrybutów indeksu

Na stronie **indeks** powinna zostać wyświetlona lista pól z typem danych oraz seria pola wyboru służących do ustawiania atrybutów indeksu. Kreator może wygenerować listę pól na podstawie metadanych i przez próbkowanie danych źródłowych. 

Możesz wybrać atrybuty zbiorcze, klikając pole wyboru u góry kolumny atrybutu. Wybierz opcję **pobierania** i **wyszukiwania** dla każdego pola, które ma zostać zwrócone do aplikacji klienckiej i podlegające przetwarzaniu wyszukiwania pełnotekstowego. Zauważ, że liczby całkowite nie są pełnymi tekstami ani rozmyte wyszukiwania (liczby są oceniane Verbatim i często są przydatne w filtrach).

Przejrzyj opis [atrybutów indeksu](/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatorów języka](/rest/api/searchservice/language-support) , aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora są tworzone fizyczne struktury danych i nie będzie można edytować tych pól bez porzucania i ponownego tworzenia wszystkich obiektów.

   ![Definicja indeksu Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definicja indeksu Cosmos DB")

### <a name="6---create-indexer"></a>6 — Tworzenie indeksatora

W pełni określony Kreator tworzy trzy odrębne obiekty w usłudze wyszukiwania. Obiekt źródła danych i obiekt indeksu są zapisywane jako zasoby nazwane w usłudze Azure Wyszukiwanie poznawcze. Ostatnim krokiem jest utworzenie obiektu indeksatora. Nazwa indeksatora pozwala na jego istnienie jako zasób autonomiczny, który można zaplanować i zarządzać niezależnie od obiektu indeksu i źródła danych, utworzonego w tej samej sekwencji kreatora.

Jeśli nie znasz indeksatorów, *indeksator* jest zasobem w usłudze Azure wyszukiwanie poznawcze, który przeszukuje zewnętrzne źródło danych w celu przeszukiwania zawartości. Dane wyjściowe kreatora **importu danych** to indeksator, który przeszukuje źródło danych Cosmos DB, wyodrębnia zawartość z możliwością przeszukiwania i importuje go do indeksu na platformie Azure wyszukiwanie poznawcze.

Poniższy zrzut ekranu przedstawia domyślną konfigurację indeksatora. Jeśli chcesz uruchomić indeksator **jednorazowo** , możesz przełączyć się do niego. Kliknij przycisk **Prześlij** , aby uruchomić kreatora i utworzyć wszystkie obiekty. Indeksowanie rozpocznie się natychmiast.

   ![Definicja indeksatora Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definicja indeksatora Cosmos DB")

Import danych można monitorować na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania oraz liczbę przekazywanych dokumentów. 

Po zakończeniu indeksowania można użyć [Eksploratora wyszukiwania](search-explorer.md) do wykonywania zapytań względem indeksu.

> [!NOTE]
> Jeśli nie widzisz danych, których oczekujesz, może być konieczne ustawienie więcej atrybutów na więcej pól. Usuń indeks i indeksator, który właśnie został utworzony, a następnie ponownie wykonaj kroki kreatora, modyfikując wybrane opcje dla atrybutów indeksu w kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Za pomocą interfejsu API REST można indeksować Azure Cosmos DB dane, wykonując trzy części przepływu pracy wspólne dla wszystkich indeksatorów na platformie Azure Wyszukiwanie poznawcze: tworzenie źródła danych, tworzenie indeksu i tworzenie indeksatora. Wyodrębnianie danych z Cosmos DB występuje po przesłaniu żądania utworzenia indeksatora. Po zakończeniu tego żądania będzie miał indeks queryable. 

> [!NOTE]
> W przypadku indeksowania danych z Cosmos DB interfejsu API Gremlin lub Cosmos DB interfejs API Cassandra musisz najpierw zażądać dostępu do zapoznawczych wersji zapoznawczych, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Po przetworzeniu żądania otrzymasz instrukcje dotyczące korzystania z [interfejsu API REST w wersji 2020-06-30-Preview](search-api-preview.md) w celu utworzenia źródła danych.

Wcześniej w tym artykule opisano, że [Azure Cosmos DB indeksowania](/azure/cosmos-db/index-overview) i indeksowania indeksowania [Wyszukiwanie poznawcze na platformie Azure](search-what-is-an-index.md) są różne operacje. W przypadku Cosmos DB indeksowania domyślnie wszystkie dokumenty są automatycznie indeksowane z wyjątkiem interfejs API Cassandra. Jeśli wyłączysz automatyczne indeksowanie, dostęp do dokumentów można uzyskać tylko za pośrednictwem własnych linków lub zapytań przy użyciu identyfikatora dokumentu. Funkcja indeksowania Wyszukiwanie poznawcze platformy Azure Cosmos DB wymaga włączenia automatycznego indeksowania w kolekcji, która będzie indeksowana przez usługę Azure Wyszukiwanie poznawcze. Podczas tworzenia rejestracji w usłudze Cosmos DB interfejs API Cassandra Indexer Preview zostaną podane instrukcje dotyczące konfiguracji Cosmos DB indeksowania.

> [!WARNING]
> Azure Cosmos DB to Kolejna generacja DocumentDB. Wcześniej z interfejsem API w wersji **2017-11-11** , można użyć `documentdb` składni. Oznacza to, że można określić typ źródła danych jako `cosmosdb` lub `documentdb` . Począwszy od interfejsu API w wersji **2019-05-06** , interfejsy API i portal usługi Azure wyszukiwanie poznawcze obsługują tylko `cosmosdb` składnię zgodnie z instrukcjami w tym artykule. Oznacza to, że typ źródła danych musi `cosmosdb` nawiązać połączenie z punktem końcowym Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 — Tworzenie danych wejściowych dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora dla usługi Azure Wyszukiwanie poznawcze (w nagłówku POST) oraz nazwę i klucz konta magazynu dla magazynu obiektów BLOB. Do wysyłania żądań HTTP do usługi Azure Wyszukiwanie poznawcze można użyć programu [Poster lub Visual Studio Code](search-get-started-rest.md) .

Skopiuj następujące cztery wartości do Notatnika, aby można było je wkleić do żądania:

+ Nazwa usługi Azure Wyszukiwanie poznawcze
+ Klucz administratora usługi Azure Wyszukiwanie poznawcze
+ Cosmos DB parametry połączenia

Te wartości można znaleźć w portalu:

1. Na stronach portalu Wyszukiwanie poznawcze platformy Azure Skopiuj adres URL usługi wyszukiwania na stronie Przegląd.

2. W okienku nawigacji po lewej stronie kliknij pozycję **klucze** , a następnie skopiuj klucz podstawowy lub pomocniczy (są one równoważne).

3. Przejdź do strony portalu dla konta magazynu Cosmos. W okienku nawigacji po lewej stronie w obszarze **Ustawienia** kliknij pozycję **klucze**. Ta strona zawiera identyfikator URI, dwa zestawy parametrów połączenia i dwa zestawy kluczy. Skopiuj jeden z parametrów połączenia do Notatnika.

### <a name="2---create-a-data-source"></a>2 — Tworzenie źródła danych

**Źródło danych** określa dane do indeksowania, poświadczeń i zasad służących do identyfikowania zmian w danych (takich jak zmodyfikowane lub usunięte dokumenty wewnątrz kolekcji). Źródło danych jest zdefiniowane jako zasób niezależny, aby mógł być używany przez wiele indeksatorów.

Aby utworzyć źródło danych, należy sformułować żądanie POST:

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

Treść żądania zawiera definicję źródła danych, która powinna zawierać następujące pola:

| Pole   | Opis |
|---------|-------------|
| **Nazwij** | Wymagane. Wybierz dowolną nazwę reprezentującą obiekt źródła danych. |
|**Wprowadź**| Wymagane. Musi być `cosmosdb` . |
|**uwierzytelniające** | Wymagane. Musi być zgodna z formatem parametrów połączenia Cosmos DB lub formatem parametrów połączenia zarządzanej tożsamości.<br/><br/>W przypadku **kolekcji SQL** parametry połączenia mogą mieć jeden z następujących formatów: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Parametry połączenia tożsamości zarządzanej o następującym formacie, który nie zawiera klucza konta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` . Aby użyć tego formatu parametrów połączenia, postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia indeksatora z bazą danych Cosmos dB przy użyciu tożsamości zarządzanej](search-howto-managed-identities-cosmos-db.md).<br/><br/>W przypadku wersji 3,2 i w wersji 3,6 **kolekcje MongoDB** używają jednego z następujących formatów parametrów połączenia: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Parametry połączenia tożsamości zarządzanej o następującym formacie, który nie zawiera klucza konta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` . Aby użyć tego formatu parametrów połączenia, postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania połączenia indeksatora z bazą danych Cosmos dB przy użyciu tożsamości zarządzanej](search-howto-managed-identities-cosmos-db.md).<br/><br/>[Aby uzyskać](https://aka.ms/azure-cognitive-search/indexer-preview) dostęp do wersji zapoznawczej i zapoznać się z informacjami na temat sposobu formatowania poświadczeń, w przypadku **wykresów Gremlin i tabel Cassandra**.<br/><br/>Należy unikać numerów portów w adresie URL punktu końcowego. Jeśli dołączysz numer portu, usługa Azure Wyszukiwanie poznawcze nie będzie w stanie indeksować bazy danych Azure Cosmos DB.|
| **wbudowane** | Zawiera następujące elementy: <br/>**Nazwa**: wymagane. Określ identyfikator kolekcji baz danych do indeksowania.<br/>**zapytanie**: opcjonalne. Możesz określić zapytanie, aby spłaszczyć dowolny dokument JSON do prostego schematu, który usługa Azure Wyszukiwanie poznawcze może indeksować.<br/>W przypadku interfejsu API MongoDB, interfejsu API Gremlin i interfejs API Cassandra zapytania nie są obsługiwane. |
| **dataChangeDetectionPolicy** | Rekomendowane. Zobacz sekcję [indeksowanie zmienionych dokumentów](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Opcjonalny. Zobacz sekcję [indeksowanie usuniętych dokumentów](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Używanie zapytań w celu kształtowania indeksowanych danych
Możesz określić zapytanie SQL do spłaszczania zagnieżdżonych właściwości lub tablic, właściwości JSON projektu i przefiltrować dane, które mają być indeksowane. 

> [!WARNING]
> Zapytania niestandardowe nie są obsługiwane w przypadku **interfejsu API MongoDB**, **interfejsu api Gremlin** i **interfejs API Cassandra**: `container.query` parametr musi mieć wartość null lub być pominięty. Jeśli musisz użyć zapytania niestandardowego, poinformuj nas o [głosowaniu użytkownika](https://feedback.azure.com/forums/263029-azure-search).

Przykładowy dokument:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Zapytanie filtru:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Spłaszczanie zapytania:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Zapytanie projekcji:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Zapytanie spłaszczone tablicy:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3 — Tworzenie docelowego indeksu wyszukiwania 

[Utwórz docelowy indeks wyszukiwanie poznawcze platformy Azure](/rest/api/searchservice/create-index) , jeśli jeszcze go nie masz. Poniższy przykład tworzy indeks z polem ID i Description:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem źródłowych dokumentów JSON lub danymi wyjściowymi projekcji zapytań niestandardowych.

> [!NOTE]
> W przypadku kolekcji partycjonowanych domyślnym kluczem dokumentu jest Azure Cosmos DB `_rid` właściwości, które platforma Azure wyszukiwanie poznawcze automatycznie zmienia nazwy, `rid` ponieważ nazwy pól nie mogą zaczynać się od znaku podkreślenia. Ponadto wartości Azure Cosmos DB `_rid` zawierają znaki, które są nieprawidłowe w usłudze Azure wyszukiwanie poznawcze Keys. Z tego powodu `_rid` wartości są zakodowane w formacie base64.
> 
> W przypadku kolekcji MongoDB platforma Azure Wyszukiwanie poznawcze automatycznie zmienia nazwę `_id` właściwości na `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapowanie między typami danych JSON i typami danych Wyszukiwanie poznawcze platformy Azure
| Typ danych JSON | Zgodne typy pól indeksu docelowego |
| --- | --- |
| Wartość logiczna |EDM. Boolean, EDM. String |
| Liczby, które wyglądają jak liczby całkowite |EDM. Int32, EDM. Int64, EDM. String |
| Liczby, które wyglądają jak zmiennoprzecinkowe |EDM. Double, EDM. String |
| Ciąg |Edm.String |
| Tablice typów pierwotnych, na przykład ["a", "b", "c"] |Collection(Edm.String) |
| Ciągi, które wyglądają jak daty |EDM. DateTimeOffset, EDM. String |
| Obiekty GEOJSON, na przykład {"Type": "Point", "współrzędne": [Long, lat]} |Edm.GeographyPoint |
| Inne obiekty JSON |Nie dotyczy |

### <a name="4---configure-and-run-the-indexer"></a>4 — Konfigurowanie i uruchamianie indeksatora

Po utworzeniu indeksu i źródła danych można rozpocząć tworzenie indeksatora:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ten indeksator działa co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z tematem [Tworzenie indeksatora](/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji o definiowaniu harmonogramów indeksatora, zobacz [How to Scheduler indeksators for Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Korzystanie z platformy .NET

Ogólnie dostępny zestaw .NET SDK ma pełną zgodność z ogólnie dostępnym interfejsem API REST. Zalecamy zapoznanie się z poprzednią sekcją interfejsu API REST, aby poznać koncepcje, przepływ pracy i wymagania. Następnie można zapoznać się z następującą dokumentacją interfejsu API platformy .NET, aby zaimplementować indeksator JSON w kodzie zarządzanym.

+ [azure.search.documents. indexes. models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. indexes. models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents. indexes. models. searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents. indexes. models. searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indeksowanie zmienionych dokumentów

Celem zasad wykrywania zmian danych jest efektywne zidentyfikowanie zmienionych elementów danych. Obecnie jedyną obsługiwaną zasadą jest [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) użycie `_ts` właściwości (Sygnatura czasowa) dostarczonej przez Azure Cosmos DB, która jest określona w następujący sposób:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

Korzystanie z tych zasad jest zdecydowanie zalecane, aby zapewnić dobrą wydajność indeksatora. 

Jeśli używasz zapytania niestandardowego, upewnij się, że `_ts` Właściwość jest rzutowana przez zapytanie.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Postęp przyrostowy i zapytania niestandardowe

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonywanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może zostać pobrany w miejscu, w którym zostanie pozostawiony po następnym uruchomieniu, zamiast konieczności ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji. 

Aby włączyć przyrostowy postęp przy użyciu zapytania niestandardowego, upewnij się, że zapytanie porządkuje wyniki według `_ts` kolumny. Dzięki temu okresowe sprawdzanie wskazujące, że usługa Azure Wyszukiwanie poznawcze używa do zapewnienia przyrostowego postępu w przypadku wystąpienia awarii.   

W niektórych przypadkach, nawet jeśli zapytanie zawiera `ORDER BY [collection alias]._ts` klauzulę, usługa Azure wyszukiwanie poznawcze nie może wywnioskować, że zapytanie jest uporządkowane według `_ts` . Możesz powiedzieć, Wyszukiwanie poznawcze platformy Azure, że wyniki są uporządkowane przy użyciu `assumeOrderByHighWaterMarkColumn` właściwości konfiguracja. Aby określić tę wskazówkę, należy utworzyć lub zaktualizować indeksator w następujący sposób: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indeksowanie usuniętych dokumentów

Po usunięciu wierszy z kolekcji, zazwyczaj chcesz usunąć te wiersze z indeksu wyszukiwania. Celem zasad wykrywania usuwania danych jest efektywne identyfikowanie usuniętych elementów danych. Obecnie jedynymi obsługiwanymi zasadami są `Soft Delete` zasady (usuwanie jest oznaczone flagą niektórych rodzajów sortowania), które są określone w następujący sposób:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Jeśli używasz zapytania niestandardowego, upewnij się, że właściwość, do której się odwołuje `softDeleteColumnName` się, jest rzutowana przez zapytanie.

Poniższy przykład tworzy źródło danych z zasadami usuwania nietrwałego:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>Następne kroki

Gratulacje! Wiesz już, jak zintegrować Azure Cosmos DB z platformą Azure Wyszukiwanie poznawcze przy użyciu indeksatora.

* Aby dowiedzieć się więcej na temat Azure Cosmos DB, zobacz [stronę usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej o usłudze Azure Wyszukiwanie poznawcze, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).