---
title: Uaktualnij wersje interfejsu API REST
titleSuffix: Azure Cognitive Search
description: Zapoznaj się z różnicami w wersji interfejsu API i Dowiedz się, które akcje są wymagane do migracji istniejącego kodu do najnowszej wersji interfejsu API REST usługi Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 0f1050bf58e0cd8d9a601d60a4c5dc22a5420483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949035"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Uaktualnianie do najnowszego interfejsu API REST na platformie Azure Wyszukiwanie poznawcze

Jeśli używasz starszej wersji [**interfejsu API REST usługi Search**](/rest/api/searchservice/), ten artykuł pomoże Ci uaktualnić aplikację do najnowszej ogólnie dostępnej wersji interfejsu api, **2020-06-30**.

Wersja 2020-06-30 zawiera ważną nową funkcję ([Magazyn wiedzy](knowledge-store-concept-intro.md)) i wprowadza kilka drobnych zmian w zachowaniu. W związku z tym ta wersja jest w większości zgodna z poprzednią wersją, więc zmiany kodu powinny być minimalne, Jeśli uaktualniasz poprzednią wersję (2019-05-06).

> [!NOTE]
> Usługa wyszukiwania obsługuje wiele wersji interfejsu API REST, w tym wcześniejszych. Możesz nadal używać tych wersji interfejsu API, ale zalecamy Migrowanie kodu do najnowszej wersji, aby uzyskać dostęp do nowych funkcji. W miarę upływu czasu najbardziej przestarzałe wersje interfejsu API REST zostaną wycofane i nie będą [już obsługiwane](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Jak uaktualnić

W przypadku uaktualniania do nowej wersji prawdopodobnie nie będzie konieczne wprowadzanie wielu zmian w kodzie, innych niż zmiana numeru wersji. Jedyne sytuacje, w których może zajść potrzeba zmiany kodu, są następujące:

* Kod kończy się niepowodzeniem, gdy nierozpoznane właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja powinna ignorować właściwości, które nie są zrozumiałe.

* Kod utrzymuje żądania interfejsu API i próbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład może się tak zdarzyć, jeśli aplikacja utrzymuje tokeny kontynuacji zwrócone przez interfejs API wyszukiwania (Aby uzyskać więcej informacji, poszukaj `@search.nextPageParameters` w [dokumentacji interfejsu API wyszukiwania](/rest/api/searchservice/Search-Documents)).

* Kod odwołuje się do wersji interfejsu API, która jest przedmiocie 2019-05-06 i podlega co najmniej jednej istotnej zmianie w tej wersji. Sekcja [uaktualnienie do 2019-05-06](#upgrade-to-2019-05-06) zawiera więcej szczegółów. 

Jeśli dowolna z tych sytuacji dotyczy użytkownika, może być konieczne odpowiednie zmiany kodu. W przeciwnym razie nie powinno być konieczne wprowadzanie zmian, chociaż warto zacząć korzystać z funkcji dodanych w nowej wersji.

## <a name="upgrade-to-2020-06-30"></a>Uaktualnij do 2020-06-30

Wersja 2020-06-30 to nowa, ogólnie dostępna wersja interfejsu API REST. Nie wprowadzono żadnych zmian, ale istnieją pewne różnice w zachowaniu. 

Funkcje są teraz ogólnie dostępne w tej wersji interfejsu API:

* [Magazyn merytoryczny](knowledge-store-concept-intro.md), trwały magazyn zawartości wzbogaconej utworzony za poorednictwem umiejętności, utworzony na potrzeby analizy i przetwarzania przez inne aplikacje. Dzięki tej możliwości potok wzbogacania AI oparty na indeksatorze może wypełnić magazyn wiedzy oprócz indeksu wyszukiwania. Jeśli używana jest wersja zapoznawcza tej funkcji, jest ona równoważna z ogólnie dostępną wersją. Jedyną wymaganą zmianą kodu jest modyfikowanie interfejsu API-Version.

Zmiany zachowania są następujące:

* [Algorytm klasyfikacji BM25](index-ranking-similarity.md) zastępuje poprzedni algorytm klasyfikacji nowszą technologią. Nowe usługi będą używać tego algorytmu automatycznie. W przypadku istniejących usług należy ustawić parametry, aby używały nowego algorytmu.

* Uporządkowane wyniki dla wartości null zostały zmienione w tej wersji, a wartości null są wyświetlane jako pierwsze, jeśli sortowanie jest `asc` i ostatnie, jeśli sortowanie jest `desc` . Jeśli Zapisano kod w celu obsługi sortowania wartości null, należy pamiętać o tej zmianie.

## <a name="upgrade-to-2019-05-06"></a>Uaktualnij do 2019-05-06

Wersja 2019-05-06 to Poprzednia ogólnie dostępna wersja interfejsu API REST. Funkcje, które stały się ogólnie dostępne w tej wersji interfejsu API, obejmują:

* [Autouzupełnianie](index-add-suggesters.md) jest funkcją typeahead, która kończy częściowo określone dane wejściowe.
* [Typy złożone](search-howto-complex-data-types.md) zapewniają natywną obsługę danych strukturalnych obiektów w indeksie wyszukiwania.
* [Tryby analizy JsonLines](search-howto-index-json-blobs.md), część indeksowania obiektów blob platformy Azure, tworzy jeden dokument wyszukiwania na jednostkę JSON rozdzieloną wierszem.
* [Wzbogacanie AI](cognitive-search-concept-intro.md) zapewnia indeksowanie, które wykorzystuje silniki wzbogacania AI Cognitive Services.

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność

Istniejący kod pisany przed wcześniejszymi wersjami interfejsu API zacznie działać w wersji API-Version = 2019-05-06 i nowszych, jeśli kod zawiera następujące funkcje:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indeksator dla Azure Cosmos DB-DataSource jest teraz "Type": "cosmosdb"

Jeśli używasz [indeksatora Cosmos DB](search-howto-index-cosmosdb.md ), musisz zmienić `"type": "documentdb"` na `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Błędy wyniku wykonywania indeksatora nie mają już stanu

Struktura błędów dla wykonywania indeksatora wcześniej zawierała `status` element. Ten element został usunięty, ponieważ nie dostarcza użytecznych informacji.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Interfejs API źródła danych indeksatora nie zwraca już parametrów połączenia

Od interfejsu API w wersji 2019-05-06 i 2019-05-06 — wersja zapoznawcza, interfejs API źródła danych nie zwraca już parametrów połączenia w odpowiedzi na jakąkolwiek operację REST. W poprzednich wersjach interfejsu API dla źródeł danych utworzonych za pomocą polecenia POST usługa Azure Wyszukiwanie poznawcze zwróciła **201** , a następnie odpowiedź OData, która zawierała ciąg połączenia w postaci zwykłego tekstu.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Umiejętność rozpoznawania nazwanych jednostek jest teraz nieobsługiwana

Jeśli wywołano w kodzie umiejętność [rozpoznawania jednostek nazw](cognitive-search-skill-named-entity-recognition.md) , wywołanie zakończy się niepowodzeniem. Funkcja wymiany jest [rozpoznawaniem jednostek](cognitive-search-skill-entity-recognition.md). Powinno być możliwe zamienienie odwołania do umiejętności bez żadnych innych zmian. Sygnatura interfejsu API jest taka sama dla obu wersji. 

### <a name="upgrading-complex-types"></a>Uaktualnianie typów złożonych

Interfejs API w wersji 2019-05-06 dodano formalne wsparcie dla typów złożonych. Jeśli kod zaimplementował poprzednie zalecenia dla równoważności typu złożonego w 2017-11-11 — wersja zapoznawcza lub 2016-09-01 — wersja zapoznawcza, istnieją pewne nowe i zmienione limity począwszy od wersji 2019-05-06, z której należy mieć świadomość:

+ Limity głębokości pól podrzędnych i liczby złożonych kolekcji na indeks zostały obniżone. Jeśli utworzono indeksy, które przekraczają te limity przy użyciu wersji zapoznawczej interfejsu API, każda próba zaktualizowania lub ponownego utworzenia ich przy użyciu interfejsu API w wersji 2019-05-06 zakończy się niepowodzeniem. Jeśli ma to zastosowanie, konieczne będzie ponowne zaprojektowanie schematu w celu dopasowania go do nowych limitów, a następnie odbudowanie indeksu.

+ Istnieje nowy limit rozpoczynający się w wersji API-Version 2019-05-06 na liczbie elementów złożonych kolekcji na dokument. W przypadku utworzenia indeksów z dokumentami, które przekraczają te limity przy użyciu wersji zapoznawczej interfejsu API, wszelkie próby ponownego indeksowania danych przy użyciu interfejsu API w wersji 2019-05-06 będą kończyć się niepowodzeniem. Jeśli ma to zastosowanie, należy zmniejszyć liczbę złożonych elementów kolekcji dla dokumentu przed ponownym indeksem danych.

Aby uzyskać więcej informacji, zobacz [limity usługi dla platformy Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Jak uaktualnić starą strukturę typu złożonego

Jeśli kod korzysta z typów złożonych z jedną ze starszych wersji interfejsu API podglądu, może być używany format definicji indeksu, który wygląda następująco:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

W interfejsie API w wersji 2017-11-11-Preview wprowadzono nowszy format przypominający drzewo służący do definiowania pól indeksów. W nowym formacie każde pole złożone zawiera kolekcję pól, w której są zdefiniowane pola podrzędne. W interfejsie API w wersji 2019-05-06 ten nowy format jest używany wyłącznie i próba utworzenia lub zaktualizowania indeksu przy użyciu starego formatu zakończy się niepowodzeniem. Jeśli masz indeksy utworzone przy użyciu starego formatu, musisz użyć interfejsu API w wersji 2017-11-11-Preview, aby zaktualizować je do nowego formatu, zanim będzie można nimi zarządzać przy użyciu interfejsu API w wersji 2019-05-06.

Za pomocą interfejsu API w wersji 2017-11-11-Preview można zaktualizować indeksy "Flat" w nowym formacie:

1. Wykonanie żądania GET w celu pobrania indeksu. Jeśli jest już w nowym formacie, wszystko jest gotowe.

2. Przetłumacz indeks z formatu "Flat" na nowy format. Musisz napisać kod, ponieważ w momencie pisania nie ma dostępnego przykładowego kodu.

3. Wykonaj żądanie PUT, aby zaktualizować indeks do nowego formatu. Pamiętaj, aby nie zmieniać żadnych szczegółów indeksu, takich jak możliwości wyszukiwania/filtrowania pól, ponieważ nie jest to dozwolone przez interfejs API indeksu aktualizacji.

> [!NOTE]
> Nie jest możliwe zarządzanie indeksami utworzonymi przy użyciu starego formatu "Flat" z Azure Portal. Uaktualnij indeksy z "płaskiej" reprezentacji do "drzewa" w najkrótszej wygodie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API REST usługi Search. Jeśli wystąpią problemy, poproś nas o pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/) lub [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Search](/rest/api/searchservice/)