---
title: Tworzenie indeksu
titleSuffix: Azure Cognitive Search
description: Wprowadza indeksowanie pojęć i narzędzi na platformie Azure Wyszukiwanie poznawcze, w tym definicje schematów i fizyczną strukturę danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d9f4ba48a7dc6cdcf6d60e4e9da5f68fcc6b1f28
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509337"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Tworzenie indeksów wyszukiwania na platformie Azure Wyszukiwanie poznawcze

Indeks wyszukiwania przechowuje zawartość do przeszukiwania używaną dla pełnych zapytań tekstowych i filtrowanych. Indeks został zdefiniowany przez schemat i zapisany w usłudze, z importowaniem danych, jak w drugim kroku. 

Indeksy zawierają *dokumenty*. Koncepcyjnie dokument jest pojedynczą jednostką danych, które można przeszukiwać w indeksie. Sprzedawca detaliczny może mieć dokument dla każdego produktu, organizacja wiadomości może mieć dokument dla każdego artykułu i tak dalej. Mapowanie tych koncepcji do bardziej znanych odpowiedników bazy danych: *indeks wyszukiwania* jest równy *tabeli*, a *dokumenty* są mniej podobne do *wierszy* w tabeli.

## <a name="whats-an-index-schema"></a>Co to jest schemat indeksu?

Struktura fizyczna indeksu jest określana przez schemat. Kolekcja "Fields" jest zazwyczaj największą częścią indeksu, gdzie każde pole ma nazwę, ma przypisany [Typ danych](/rest/api/searchservice/Supported-data-types)i jest przypisywane do dozwolonych zachowań, które określają, w jaki sposób jest używany.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Inne elementy są zwijane do zwięzłości, ale następujące linki mogą zapewnić szczegóły: [sugestie](index-add-suggesters.md), [Profile oceniania](index-add-scoring-profiles.md) [, analizatory używane do](search-analyzers.md) przetwarzania ciągów do tokenów zgodnie z regułami językowymi lub innymi charakterystykami obsługiwanymi przez analizatorze oraz ustawienia funkcji [tworzenia skryptów zdalnych między źródłami danych (CORS)](#corsoptions) .

## <a name="choose-a-client"></a>Wybierz klienta

Istnieje kilka metod tworzenia indeksu wyszukiwania. Zalecamy używanie Azure Portal lub interfejsów API REST na potrzeby wczesnego tworzenia i testowania koncepcji.

Podczas opracowywania Planuj częste ponowne kompilacje. Ponieważ struktury fizyczne są tworzone w usłudze, [usuwanie i odtwarzanie indeksów](search-howto-reindex.md) jest niezbędne w przypadku większości modyfikacji istniejącej definicji pola. Możesz rozważyć pracę z podzbiorem danych, aby szybciej tworzyć kompilacje.

### <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksem wyszukiwania, w tym żądania GET, wymagają od [administratora klucza interfejsu API](search-security-api-keys.md) w żądaniu.

### <a name="limits"></a>Limity

Wszystkie [warstwy usług ograniczają](search-limits-quotas-capacity.md#index-limits) liczbę obiektów, które można utworzyć. W przypadku eksperymentowania w warstwie Bezpłatna możesz mieć tylko 3 indeksy w danym momencie.

### <a name="use-azure-portal-to-create-a-search-index"></a>Użyj Azure Portal, aby utworzyć indeks wyszukiwania

Portal udostępnia dwie opcje tworzenia indeksu wyszukiwania: [**Kreator importowania danych**](search-import-data-portal.md) i **Dodawanie indeksu** dostarczającego pola służące do określania schematu indeksu. Pakiety kreatora w dodatkowych operacjach, tworząc również indeksator, źródło danych i ładujące dane. Jeśli jest to więcej niż to, co jest potrzebne, należy tylko użyć **Dodaj indeks** lub innego podejścia.

Poniższy zrzut ekranu pokazuje, gdzie można znaleźć **Dodaj indeks** w portalu. **Importowanie danych** to prawo do następnego drzwi.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Dodaj indeks — polecenie" border="true":::

> [!Tip]
> Indeksowanie za pomocą portalu wymusza wymagania i reguły schematu dla określonych typów danych, takich jak niezezwalanie na funkcje wyszukiwania pełnotekstowego w polach liczbowych. Gdy masz prawidłowo używany indeks, możesz skopiować kod JSON z portalu i dodać go do rozwiązania.

### <a name="use-a-rest-client"></a>Korzystanie z klienta REST

Zarówno program Poster, jak i Visual Studio Code (z rozszerzeniem systemu Azure Wyszukiwanie poznawcze) mogą działać jako klient indeksu wyszukiwania. Za pomocą dowolnego z tych narzędzi można nawiązać połączenie z usługą wyszukiwania i wysyłać żądania [create index (REST)](/rest/api/searchservice/create-index) . Istnieje wiele samouczków i przykładów demonstrujących klientów REST do tworzenia obiektów. 

Aby dowiedzieć się więcej na temat każdego klienta, Zacznij od jednego z poniższych artykułów:

+ [Tworzenie indeksu wyszukiwania przy użyciu REST i programu Poster](search-get-started-rest.md)
+ [Wprowadzenie do Visual Studio Code i platformy Azure Wyszukiwanie poznawcze](search-get-started-vs-code.md)

Zapoznaj się z [operacjami indeksowania (REST)](/rest/api/searchservice/index-operations) , aby uzyskać pomoc dotyczącą formułowania żądań indeksu.

### <a name="use-an-sdk"></a>Korzystanie z zestawu SDK

W przypadku Wyszukiwanie poznawcze zestawy SDK platformy Azure implementują ogólnie dostępne funkcje. W związku z tym można użyć dowolnego z zestawów SDK do utworzenia indeksu wyszukiwania. Wszystkie z nich zapewniają **SearchIndexClient** , który ma metody do tworzenia i aktualizowania indeksów.

| Zestaw Azure SDK | Klient | Przykłady |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-dotnet-przykłady/szybki start/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indeksy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. PR](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="defining-fields"></a>Definiowanie pól

Należy wyznaczyć jedno pole typu EDM. String jako klucz dokumentu. Służy do jednoznacznej identyfikacji każdego dokumentu wyszukiwania. Aby wypełnić stronę szczegółów, możesz pobrać dokument według swojego klucza.  

Jeśli dane przychodzące mają charakter hierarchiczny, przypisz typ danych [typu złożonego](search-howto-complex-data-types.md) , aby reprezentować zagnieżdżone struktury. Wbudowany zestaw danych przykładowych, Hotele, ilustruje złożone typy przy użyciu adresu (zawiera wiele podpól), które mają relację jeden do jednego z każdym hotelem, oraz złożoną kolekcję pokojów, w której wiele pokojów jest skojarzonych z każdym hotelem. 

Przypisz wszystkie analizatory do pól ciągów przed utworzeniem indeksu. Wykonaj te same czynności dla sugestii, jeśli chcesz włączyć Autouzupełnianie dla określonych pól.

<a name="index-attributes"></a>

### <a name="attributes"></a>Atrybuty

Atrybuty pól określają, jak używane jest dane pole, np. czy jest używane w wyszukiwaniu pełnotekstowym, nawigacji aspektowej, operacjach sortowania i tak dalej. 

Pola ciągów są często oznaczane jako "z możliwością wyszukiwania" i "do pobierania". Pola używane do zawężania wyników wyszukiwania obejmują "sortowanie", "filtrowanie" i "uśmiech".

|Atrybut|Opis|  
|---------------|-----------------|  
|możliwością wyszukiwania |Wyszukiwanie pełnotekstowe, które można poddawać analizie leksykalnej, takiej jak dzielenie wyrazów podczas indeksowania. Ustawienie wartości „sunny day” dla pola z możliwością wyszukiwania spowoduje podział na dwa oddzielne tokeny „sunny” i „day”. Aby uzyskać więcej informacji, zobacz [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md).|  
|Filtrowanie |Odwołania do tego atrybutu znajdują się w zapytaniach $filter. Pola z możliwością filtrowania typu `Edm.String` lub `Collection(Edm.String)` nie są poddawane dzieleniu wyrazów, więc porównania dotyczą tylko dokładnych dopasowań. Ustawienie pola filtrowania na wartość „sunny day” spowoduje, że pole typu `$filter=f eq 'sunny'` nie wyświetli żadnych wyników, ale `$filter=f eq 'sunny day'` już tak. |  
|sortowan |Domyślnie system sortuje pozycje według wyników, ale można również ustawić sortowanie według poszczególnych pól w dokumentach. Pola typu `Collection(Edm.String)` nie mogą być "do sortowania". |  
|Tworzenie aspektów |Zwykle używany w prezentacji wyników wyszukiwania, która zawiera liczbę trafień według kategorii (na przykład hotele znajdujące się w określonym mieście). Nie można używać tej opcji z polami typu `Edm.GeographyPoint`. Pola typu `Edm.String` , które są filtrowane, "sortowanie" lub "uśmiech", mogą mieć co najwyżej 32 kilobajtów. Aby uzyskać więcej informacji, zobacz [Create Index (REST API)](/rest/api/searchservice/create-index) (Tworzenie indeksu [interfejs REST API]).|  
|głównych |Unikatowy identyfikator dokumentów w indeksie. Można wybrać tylko jedno pole klucza i musi ono być typu `Edm.String`.|  
|Pobieranie |Określa, czy pole może być zwracane w wynikach wyszukiwania. Jest przydatny, jeśli chcesz użyć pola (np. *marża zysku*) z możliwością filtrowania, sortowania lub oceniania, ale nie chcesz, aby to pole było widoczne dla użytkownika końcowego. Ten atrybut musi przyjmować wartość `true` dla pól typu `key`.|  

Chociaż możesz w dowolnym momencie dodać nowe pola, istniejące definicje pól są zablokowane przez cały czas istnienia indeksu. Z tego powodu deweloperzy zazwyczaj używają portalu do tworzenia prostych indeksów, testowania pomysłów lub używania stron portalu w celu wyszukania ustawień. Częsta iteracja po projekcie indeksu jest bardziej wydajna, jeśli stosujesz podejście oparte na kodzie, które pozwala na odbudowanie indeksu w prosty sposób.

> [!NOTE]
> Interfejsy API używane do tworzenia indeksu mają różne ustawienia domyślne. W przypadku [interfejsów API REST](/rest/api/searchservice/Create-Index)większość atrybutów jest domyślnie włączonych (na przykład "możliwe do przeszukania" i "możliwy do pobierania" są spełnione w przypadku pól ciągów) i często należy je ustawić tylko wtedy, gdy chcesz je wyłączyć. W przypadku zestawu .NET SDK przeciwieństwem jest true. W przypadku każdej właściwości, która nie została ustawiona jawnie, domyślnie należy wyłączyć odpowiednie zachowanie wyszukiwania, chyba że zostanie to włączone.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atrybuty i rozmiar indeksu (implikacje magazynu)

Rozmiar indeksu zależy od rozmiaru przekazywanych dokumentów, a także konfiguracji indeksu, takich jak to, czy są uwzględniane sugestie i jak ustawić atrybuty dla poszczególnych pól. 

Poniższy zrzut ekranu ilustruje wzorce magazynu indeksów, które wynikają z różnych kombinacji atrybutów. Indeks jest oparty na **przykładowym indeksie nieruchomości**, który można łatwo utworzyć przy użyciu Kreatora importu danych. Chociaż schematy indeksów nie są wyświetlane, można wywnioskować atrybuty na podstawie nazwy indeksu. Na przykład indeks z *możliwością wyszukiwania realestate* ma wybrany atrybut "możliwy do przeszukiwania" i nic nie jest, a w przypadku indeksu, który można *pobrać* , ma wybrany atrybut "umożliwiający pobieranie" i nic innego i tak dalej.

![Rozmiar indeksu na podstawie wybranego atrybutu](./media/search-what-is-an-index/realestate-index-size.png "Rozmiar indeksu na podstawie wybranego atrybutu")

Chociaż te warianty indeksów są sztuczne, możemy odnieść się do nich w celu uzyskania szerokiego porównania wpływu atrybutów na magazyn. Czy ustawienie "pobierania" zwiększa rozmiar indeksu? Nie. Czy dodanie pól do **sugestii** zwiększa rozmiar indeksu? Tak.

Indeksy obsługujące filtrowanie i sortowanie są proporcjonalnie większe niż indeksy obsługujące tylko wyszukiwanie pełnotekstowe. Wynika to z tego, że operacje filtrowania i sortowania sprawdzają dokładne dopasowania, wymagając obecności ciągów tekstowych Verbatim. Natomiast pola z możliwością wyszukiwania obsługujące zapytania pełnotekstowe używają odwróconych indeksów, które są wypełniane za pomocą tokenów, które zużywają mniej miejsca niż całe dokumenty. 

> [!Note]
> Architektura magazynu jest uważana za szczegóły implementacji platformy Azure Wyszukiwanie poznawcze i może ulec zmianie bez powiadomienia. Nie ma żadnej gwarancji, że bieżące zachowanie będzie nadal występowało w przyszłości.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>O `corsOptions`

Schematy indeksów zawierają sekcję dla ustawienia `corsOptions` . Kod JavaScript po stronie klienta nie może domyślnie wywołać żadnych interfejsów API, ponieważ przeglądarka uniemożliwi wszystkie żądania między źródłami. Aby zezwolić na zapytania między źródłami do indeksu, Włącz funkcję CORS (Udostępnianie zasobów między źródłami) przez ustawienie atrybutu **corsOptions** . Ze względów bezpieczeństwa tylko interfejsy API zapytań obsługują mechanizm CORS. 

Dla mechanizmu CORS można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): jest to lista źródeł, do których zostanie udzielony dostęp do Twojego indeksu. Oznacza to, że każdy kod JavaScript obsługiwany z tych źródeł będzie mógł wysyłać zapytania do indeksu (przy założeniu, że zawiera on prawidłowy klucz API-Key). Każdy punkt początkowy ma zwykle postać, `protocol://<fully-qualified-domain-name>:<port>` chociaż `<port>` jest często pomijany. Aby uzyskać więcej informacji, zobacz [udostępnianie zasobów między źródłami (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Jeśli chcesz zezwolić na dostęp do wszystkich źródeł, Dołącz `*` jako pojedynczy element w tablicy **allowedOrigins** . *Nie jest to zalecane w przypadku usług wyszukiwania w środowisku produkcyjnym* , ale często jest to przydatne w przypadku programowania i debugowania.

+ **atrybut maxageinseconds** (opcjonalnie): przeglądarki używają tej wartości, aby określić czas (w sekundach), w którym są buforowane odpowiedzi na inspekcje wstępne. Ta wartość musi być nieujemną liczbą całkowitą. Im większa wartość to, tym lepsza wydajność, ale im dłużej zacznie obowiązywać zmiana zasad CORS. Jeśli nie jest ustawiona, zostanie użyty domyślny czas trwania wynoszący 5 minut.

## <a name="next-steps"></a>Następne kroki

Możesz skorzystać z możliwości tworzenia indeksu przy użyciu niemal każdej próbki lub wskazówki dotyczącej Wyszukiwanie poznawcze. Aby rozpocząć pracę, możesz wybrać dowolny z przewodników szybki start z spisu treści.

Warto również zapoznać się z metodologiami dotyczącymi ładowania indeksu do danych. Definicja indeksu i populacja są wykonywane razem. Poniższe artykuły zawierają więcej informacji.

+ [Omówienie importowania danych](search-what-is-data-import.md)

+ [Dodawanie, aktualizowanie lub usuwanie dokumentów (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 