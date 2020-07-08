---
title: Tworzenie definicji indeksu i koncepcji
titleSuffix: Azure Cognitive Search
description: Wprowadzenie do indeksowania terminów i koncepcji na platformie Azure Wyszukiwanie poznawcze, w tym części składników i struktury fizycznej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79282785"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Tworzenie podstawowego indeksu na platformie Azure Wyszukiwanie poznawcze

Na platformie Azure Wyszukiwanie poznawcze *indeks* jest trwałym magazynem *dokumentów* i innych konstrukcji używanych do przeszukiwania i wyszukiwania pełnotekstowego w usłudze Azure wyszukiwanie poznawcze. Koncepcyjnie dokument jest pojedynczą jednostką danych, które można przeszukiwać w indeksie. Na przykład sklep internetowy może mieć dokument dla każdego sprzedawanego produktu, a organizacja medialna — dla każdego artykułu itp. W przełożeniu na lepiej znane pojęcia bazodanowe: *indeks* jest podobny do *tabeli*, a *dokumenty* są w przybliżeniu równe *wierszom* w tabeli.

Gdy dodajesz lub przekażesz indeks, platforma Azure Wyszukiwanie poznawcze tworzy struktury fizyczne na podstawie udostępnianego schematu. Na przykład, jeśli pole w indeksie jest oznaczone jako kryterium wyszukiwania, dla tego pola jest tworzony odwrócony indeks. Później podczas dodawania lub przekazywania dokumentów lub przesyłania zapytań wyszukiwania do usługi Azure Wyszukiwanie poznawcze wysyłane są żądania do określonego indeksu w usłudze wyszukiwania. Ładowanie pól z wartościami dokumentu jest nazywane *indeksowaniem* lub pozyskiwaniem danych.

Indeks można utworzyć w portalu, [interfejsie API REST](search-create-index-rest-api.md)lub w [zestawie SDK platformy .NET](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Zalecany przepływ pracy

Docieranie do właściwego projektu indeksu jest zazwyczaj realizowane przez wiele iteracji. Korzystanie z kombinacji narzędzi i interfejsów API może pomóc w szybkim sfinalizowaniu projektu.

1. Określ, czy można użyć [indeksatora](search-indexer-overview.md#supported-data-sources). Jeśli dane zewnętrzne są jednym z obsługiwanych źródeł danych, można prototypować i ładować indeks za pomocą kreatora [**importu danych**](search-import-data-portal.md) .

2. Jeśli nie można użyć opcji **Importuj dane**, nadal można [utworzyć początkowy indeks w portalu](search-create-index-portal.md), dodać pola, typy danych i przypisać atrybuty przy użyciu kontrolek na stronie **Dodawanie indeksu** . W portalu pokazano, które atrybuty są dostępne dla różnych typów danych. Jeśli nie jesteś nowym indeksem projektu, jest to przydatne.

   ![Dodaj stronę indeksu pokazującą atrybuty według typu danych](media/search-create-index-portal/field-attributes.png "Dodaj stronę indeksu pokazującą atrybuty według typu danych")
  
   Po kliknięciu pozycji **Utwórz**wszystkie struktury fizyczne obsługujące indeks są tworzone w usłudze wyszukiwania.

3. Pobierz schemat indeksu przy użyciu [interfejsu API REST Get index](https://docs.microsoft.com/rest/api/searchservice/get-index) i narzędzia do testowania sieci Web, takiego jak program [Poster](search-get-started-postman.md). Masz teraz reprezentację w formacie JSON indeksu utworzonego w portalu. 

   W tym momencie przełączasz się na podejście oparte na kodzie. Portal nie jest dobrze dostosowany do iteracji, ponieważ nie można edytować indeksu, który został już utworzony. Można jednak użyć elementu Poster i REST dla pozostałych zadań.

4. [Załaduj swój indeks z danymi](search-what-is-data-import.md). Usługa Azure Wyszukiwanie poznawcze akceptuje dokumenty JSON. Aby programowo załadować dane, można użyć programu Poster z dokumentami JSON w ładunku żądania. Jeśli dane nie są łatwo wyrażone w formacie JSON, ten krok będzie najbardziej pracochłonny.

5. Zbadaj swój indeks, sprawdź wyniki i wykonaj kolejne iteracje na schemacie indeksu do momentu rozpoczęcia wyświetlania oczekiwanych wyników. Aby zbadać indeks, można użyć [**Eksploratora wyszukiwania**](search-explorer.md) lub programu Poster.

6. Kontynuuj korzystanie z kodu, aby wykonać iterację Twojego projektu.  

Ponieważ struktury fizyczne są tworzone w usłudze, [usuwanie i ponowne tworzenie indeksów](search-howto-reindex.md) jest konieczne po każdym wprowadzeniu istotnych zmian w istniejącej definicji pola. Oznacza to, że podczas opracowywania należy zaplanować częste ponowne kompilacje. Możesz rozważyć pracę z podzbiorem danych, aby szybciej tworzyć kompilacje. 

Nie zaleca się tworzenia kodu, a nie podejścia do portalu. Jeśli korzystasz z portalu dla definicji indeksu, musisz podać definicję indeksu dla każdej kompilacji. Alternatywnie narzędzia takie jak [Poster i interfejs API REST](search-get-started-postman.md) są przydatne do testowania koncepcji, gdy projekty programistyczne są nadal w fazie wczesnych faz. Możesz wprowadzić przyrostowe zmiany definicji indeksu w treści żądania, a następnie wysłać żądanie do usługi, aby ponownie utworzyć indeks przy użyciu zaktualizowanego schematu.

## <a name="components-of-an-index"></a>Składniki indeksu

W sposób schematyczny indeks Wyszukiwanie poznawcze platformy Azure składa się z następujących elementów. 

[*Kolekcja Fields*](#fields-collection) jest zwykle największą częścią indeksu, gdzie każde pole ma nazwę, wpisano i jest przypisane do dozwolonych zachowań, które określają sposób ich używania. Inne elementy obejmują [sugestie](#suggesters), [Profile oceniania](#scoring-profiles), [analizatory](#analyzers) ze składnikami składników do obsługi opcji dostosowywania, [CORS](#cors) i [klucza szyfrowania](#encryption-key) .

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
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
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Kolekcja pól i atrybuty pól

W trakcie definiowania schematu musisz określić nazwę, typ i atrybuty każdego pola w indeksie. Typ pola klasyfikuje dane, które są w nim przechowywane. Atrybuty są ustawiane dla poszczególnych pól, aby określić sposób użycia pola. W poniższych tabelach zostały wyszczególnione typy i atrybuty, które możesz określić.

### <a name="data-types"></a>Typy danych
| Typ | Opis |
| --- | --- |
| *Edm.String* |Tekst, który można opcjonalnie uzyskać tokeny na potrzeby wyszukiwania pełnotekstowego (dzielenia wyrazów, szukania rdzeni itd.). |
| *Collection(Edm.String)* |Lista ciągów, które opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego. Nie ma teoretycznej górnej granicy liczby elementów w kolekcji, ale rozmiar ładunku w kolekcjach jest ograniczony do 16 MB. |
| *Edm.Boolean* |Zawiera wartości prawda/fałsz. |
| *Edm.Int32* |32-bitowe wartości całkowite. |
| *Edm.Int64* |64-bitowe wartości całkowite. |
| *Edm.Double* |Dane liczbowe o podwójnej precyzji. |
| *Edm.DateTimeOffset* |Wartości daty i godziny reprezentowane w formacie OData v4 (na przykład `yyyy-MM-ddTHH:mm:ss.fffZ` lub `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| *Edm.GeographyPoint* |Punkt przedstawiający lokalizację geograficzną na świecie. |

Więcej szczegółowych informacji na temat [obsługiwanych typów danych](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)platformy Azure wyszukiwanie poznawcze można znaleźć tutaj.

### <a name="index-attributes"></a>Atrybuty indeksu

Dokładnie jedno pole w indeksie musi być wyznaczono jako pole **klucza** , które jednoznacznie identyfikuje każdy dokument.

Inne atrybuty określają, w jaki sposób pole jest używane w aplikacji. Na przykład atrybut **możliwy do przeszukiwania** jest przypisany do każdego pola, które powinno zostać uwzględnione w wyszukiwaniu pełnotekstowym. 

Interfejsy API używane do tworzenia indeksu mają różne ustawienia domyślne. W przypadku [interfejsów API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index)większość atrybutów jest domyślnie włączona (na przykład **Wyszukiwanie** i **pobieranie jest prawdziwe** dla pól ciągów) i często należy je ustawić tylko wtedy, gdy chcesz je wyłączyć. W przypadku zestawu .NET SDK przeciwieństwem jest true. W przypadku każdej właściwości, która nie została ustawiona jawnie, domyślnie należy wyłączyć odpowiednie zachowanie wyszukiwania, chyba że zostanie to włączone.

| Atrybut | Opis |
| --- | --- |
| `key` |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, który jest używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| `retrievable` |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| `filterable` |Umożliwia używanie pola w zapytaniach filtrów. |
| `Sortable` |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| `facetable` |Umożliwia używanie pola w strukturze [nawigacji aspektowej](search-faceted-navigation.md) podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| `searchable` |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |

## <a name="index-size"></a>Rozmiar indeksu

Rozmiar indeksu zależy od rozmiaru przekazywanych dokumentów, a także konfiguracji indeksu, takich jak to, czy są uwzględniane sugestie i jak ustawić atrybuty dla poszczególnych pól. Poniższy zrzut ekranu ilustruje wzorce magazynu indeksów, które wynikają z różnych kombinacji atrybutów.

Indeks jest oparty na [wbudowanym przykładowym](search-get-started-portal.md) źródle danych Nieruchomości, który można indeksować i wysyłać zapytania w portalu. Chociaż schematy indeksów nie są wyświetlane, można wywnioskować atrybuty na podstawie nazwy indeksu. Na przykład indeks z *możliwością wyszukiwania realestate* ma wybrany atrybut **możliwy do przeszukiwania** i nic nie jest, a *realestate —* indeks, który można pobrać, ma wybrany atrybut do **pobierania** i nic innego i tak dalej.

![Rozmiar indeksu na podstawie wybranego atrybutu](./media/search-what-is-an-index/realestate-index-size.png "Rozmiar indeksu na podstawie wybranego atrybutu")

Chociaż te warianty indeksów są sztuczne, możemy odnieść się do nich w celu uzyskania szerokiego porównania wpływu atrybutów na magazyn. Czy ustawienie **umożliwia** pobieranie rozmiaru indeksu wzrostu? Nie. Czy dodanie pól do **sugestii** zwiększa rozmiar indeksu? Tak.

Indeksy obsługujące filtrowanie i sortowanie są proporcjonalnie większe niż te obsługujące tylko wyszukiwanie pełnotekstowe. Filtrowanie i sortowanie operacji skanowania pod kątem dokładnych dopasowań, które wymagają obecności nienaruszonych dokumentów. W przeciwieństwie do pól z możliwością wyszukiwania tekst obsługujący pełnotekstowe i rozmyte wyszukiwanie używaj odwróconych indeksów, które są wypełniane za pomocą tokenów, które zużywają mniej miejsca niż całe dokumenty. 

> [!Note]
> Architektura magazynu jest uważana za szczegóły implementacji platformy Azure Wyszukiwanie poznawcze i może ulec zmianie bez powiadomienia. Nie ma żadnej gwarancji, że bieżące zachowanie będzie nadal występowało w przyszłości.

## <a name="suggesters"></a>Funkcje sugestii
Sugerował to sekcja schematu, która określa, które pola w indeksie są używane do obsługi autouzupełniania lub zapytań typu "w wyszukiwaniach". Zazwyczaj częściowe ciągi wyszukiwania są wysyłane do [sugestii (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions) , podczas gdy użytkownik pisze zapytanie wyszukiwania, a interfejs API zwraca zestaw sugerowanych dokumentów lub fraz. 

Pola dodane do sugestii są używane do kompilowania terminów wyszukiwania z wyprzedzeniem. Wszystkie terminy wyszukiwania są tworzone podczas indeksowania i zapisywane oddzielnie. Aby uzyskać więcej informacji na temat tworzenia struktury sugerującej, zobacz [Dodawanie sugestii](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profile oceniania

[Profil oceniania](index-add-scoring-profiles.md) to sekcja schematu, która definiuje niestandardowe zachowania oceniania, które pozwalają na wpływ, które elementy są wyświetlane w wynikach wyszukiwania. Profile oceniania składają się z wag pól i funkcji. Aby ich użyć, należy określić profil według nazwy w ciągu zapytania.

Domyślny profil oceniania działa w tle, aby obliczyć wynik wyszukiwania dla każdego elementu w zestawie wyników. Możesz użyć wewnętrznego, nienazwanego profilu oceniania. Alternatywnie można ustawić **defaultScoringProfile** , aby używał niestandardowego profilu jako domyślnego, wywołanego za każdym razem, gdy w ciągu zapytania nie określono profilu niestandardowego.

## <a name="analyzers"></a>Analizatory

Element analizatorzes ustawia nazwę analizatora języka, który ma być używany dla pola. Aby uzyskać więcej informacji na temat zakresu analizatorów dostępnych dla Ciebie, zobacz [Dodawanie analizatorów do indeksu wyszukiwanie poznawcze platformy Azure](search-analyzers.md). Analizatory mogą być używane tylko z polami z możliwością wyszukiwania. Gdy analizator zostanie przypisany do pola, nie można go zmienić, chyba że zostanie odbudowany indeks.

## <a name="cors"></a>CORS

Kod JavaScript po stronie klienta nie może domyślnie wywołać żadnych interfejsów API, ponieważ przeglądarka uniemożliwi wszystkie żądania między źródłami. Aby zezwolić na zapytania między źródłami do indeksu, Włącz funkcję CORS (Udostępnianie zasobów między źródłami) przez ustawienie atrybutu **corsOptions** . Ze względów bezpieczeństwa tylko interfejsy API zapytań obsługują mechanizm CORS. 

Dla mechanizmu CORS można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): jest to lista źródeł, do których zostanie udzielony dostęp do Twojego indeksu. Oznacza to, że każdy kod JavaScript obsługiwany z tych źródeł będzie mógł wysyłać zapytania do indeksu (przy założeniu, że zawiera on prawidłowy klucz API-Key). Każdy punkt początkowy ma zwykle postać, `protocol://<fully-qualified-domain-name>:<port>` chociaż `<port>` jest często pomijany. Aby uzyskać więcej informacji, zobacz [udostępnianie zasobów między źródłami (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Jeśli chcesz zezwolić na dostęp do wszystkich źródeł, Dołącz `*` jako pojedynczy element w tablicy **allowedOrigins** . *Nie jest to zalecane w przypadku usług wyszukiwania w środowisku produkcyjnym* , ale często jest to przydatne w przypadku programowania i debugowania.

+ **atrybut maxageinseconds** (opcjonalnie): przeglądarki używają tej wartości, aby określić czas (w sekundach), w którym są buforowane odpowiedzi na inspekcje wstępne. Ta wartość musi być nieujemną liczbą całkowitą. Im większa wartość to, tym lepsza wydajność, ale im dłużej zacznie obowiązywać zmiana zasad CORS. Jeśli nie jest ustawiona, zostanie użyty domyślny czas trwania wynoszący 5 minut.

## <a name="encryption-key"></a>Klucz szyfrowania

Wszystkie indeksy Wyszukiwanie poznawcze platformy Azure domyślnie są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft, indeksy można skonfigurować tak, aby były szyfrowane za pomocą **kluczy zarządzanych przez klienta** w Key Vault. Aby dowiedzieć się więcej, zobacz [Zarządzanie kluczami szyfrowania w usłudze Azure wyszukiwanie poznawcze](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Następne kroki

Dzięki zrozumieniu kompozycji indeksów można kontynuować w portalu, aby utworzyć swój pierwszy indeks.

> [!div class="nextstepaction"]
> [Dodawanie indeksu (Portal)](search-create-index-portal.md)
