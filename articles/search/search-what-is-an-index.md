---
title: Tworzenie indeksu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Wprowadza indeksowanie pojęć i narzędzi na platformie Azure Wyszukiwanie poznawcze, w tym definicje schematów i fizyczną strukturę danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: aa7c06c3bad59bad11fa288631042cca86109706
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701137"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Tworzenie podstawowego indeksu wyszukiwania na platformie Azure Wyszukiwanie poznawcze

W usłudze Azure Wyszukiwanie poznawcze *indeks wyszukiwania* przechowuje zawartość do przeszukiwania używaną dla pełnych zapytań tekstowych i filtrowanych. Indeks został zdefiniowany przez schemat i zapisany w usłudze, z importowaniem danych, jak w drugim kroku. 

Indeksy zawierają *dokumenty*. Koncepcyjnie dokument jest pojedynczą jednostką danych, które można przeszukiwać w indeksie. Sprzedawca detaliczny może mieć dokument dla każdego produktu, organizacja wiadomości może mieć dokument dla każdego artykułu i tak dalej. Mapowanie tych koncepcji do bardziej znanych odpowiedników bazy danych: *indeks wyszukiwania* jest równy *tabeli*, a *dokumenty* są mniej podobne do *wierszy* w tabeli.

Struktura fizyczna indeksu jest określana przez schemat z polami oznaczonymi jako "z możliwością wyszukiwania", co powoduje odwrócenie indeksu utworzonego dla tego pola. 

Indeks można utworzyć przy użyciu następujących narzędzi i interfejsów API:

* W Azure Portal Użyj kreatora **dodawania indeksu** lub **importowania danych**
* Korzystanie z funkcji [tworzenia indeksu (interfejs API REST)](/rest/api/searchservice/create-index)
* Korzystanie z [zestawu SDK platformy .NET](./search-get-started-dotnet.md)

Łatwiej jest poznać narzędzie portalu. Portal wymusza wymagania i reguły schematu dla określonych typów danych, na przykład niezezwalanie na funkcje wyszukiwania pełnotekstowego w polach liczbowych. Gdy masz prawidłowo używany indeks, możesz przejść do kodu, pobierając definicję JSON z usługi za pomocą polecenia [Get index (REST API)](/rest/api/searchservice/get-index) i dodając ją do rozwiązania.

## <a name="recommended-workflow"></a>Zalecany przepływ pracy

Dotarcie do ostatecznego projektu indeksu jest procesem iteracyjnym. Często zaczynasz od portalu, aby utworzyć początkowy indeks, a następnie przełącz się do kodu, aby umieścić indeks pod kontrolą źródła.

1. Określ, czy można użyć [**Importuj dane**](search-import-data-portal.md). Kreator wykonuje indeksowanie oparte na indeksie, jeśli dane źródłowe pochodzą z [obsługiwanego typu źródła danych na platformie Azure](search-indexer-overview.md#supported-data-sources).

1. Jeśli nie możesz użyć **importowania danych**, Rozpocznij od **dodania indeksu** , aby zdefiniować schemat.

   ![Dodaj indeks — polecenie](media/search-what-is-an-index/add-index.png "Dodaj indeks — polecenie")

1. Podaj nazwę i klucz służące do unikatowego identyfikowania każdego dokumentu wyszukiwania w indeksie. Klucz jest obowiązkowy i musi być typu EDM. String. Podczas importowania należy zaplanować mapowanie unikatowego pola w danych źródłowych do tego pola. 

   Portal udostępnia `id` pole klucza. Aby zastąpić wartość domyślną `id` , Utwórz nowe pole (na przykład nową definicję pola o nazwie `HotelId` ), a następnie wybierz je w **kluczu**.

   ![Wypełnij wymagane właściwości](media/search-what-is-an-index//field-attributes.png "Wypełnij wymagane właściwości")

1. Dodaj więcej pól. W portalu są wyświetlane [atrybuty pól](#index-attributes) , które są dostępne dla różnych typów danych. Jeśli nie jesteś nowym indeksem projektu, jest to przydatne.

   Jeśli dane przychodzące mają charakter hierarchiczny, przypisz typ danych [typu złożonego](search-howto-complex-data-types.md) , aby reprezentować zagnieżdżone struktury. Wbudowany zestaw danych przykładowych, Hotele, ilustruje złożone typy przy użyciu adresu (zawiera wiele podpól), które mają relację jeden do jednego z każdym hotelem, oraz złożoną kolekcję pokojów, w której wiele pokojów jest skojarzonych z każdym hotelem. 

1. Przypisz wszystkie [analizatory](#analyzers) do pól ciągów przed utworzeniem indeksu. Wykonaj te same czynności dla [sugestii](#suggesters) , jeśli chcesz włączyć Autouzupełnianie dla określonych pól.

1. Kliknij przycisk **Utwórz** , aby skompilować struktury fizyczne w usłudze wyszukiwania.

1. Po utworzeniu indeksu Użyj dodatkowych poleceń, aby przejrzeć definicje lub dodać więcej elementów.

   ![Dodaj stronę indeksu pokazującą atrybuty według typu danych](media/search-what-is-an-index//field-definitions.png "Dodaj stronę indeksu pokazującą atrybuty według typu danych")

1. Pobierz schemat indeksu przy użyciu polecenia [Pobierz indeks (interfejs API REST)](/rest/api/searchservice/get-index) i narzędzia do testowania sieci Web, takiego jak [Poster](search-get-started-rest.md). Teraz masz reprezentację w formacie JSON indeksu, który można dostosować do kodu.

1. [Załaduj swój indeks z danymi](search-what-is-data-import.md). Usługa Azure Wyszukiwanie poznawcze akceptuje dokumenty JSON. Aby programowo załadować dane, można użyć programu Poster z dokumentami JSON w ładunku żądania. Jeśli dane nie są łatwo wyrażone w formacie JSON, ten krok będzie najbardziej pracochłonny. 

    Gdy indeks zostanie załadowany z danymi, większość edycji istniejących pól będzie wymagała porzucenia i odbudowania indeksu.

1. Zbadaj swój indeks, sprawdź wyniki i wykonaj kolejne iteracje na schemacie indeksu do momentu rozpoczęcia wyświetlania oczekiwanych wyników. Aby zbadać indeks, można użyć [**Eksploratora wyszukiwania**](search-explorer.md) lub programu Poster.

Podczas opracowywania Planuj częste ponowne kompilacje. Ponieważ struktury fizyczne są tworzone w usłudze, [usuwanie i odtwarzanie indeksów](search-howto-reindex.md) jest niezbędne w przypadku większości modyfikacji istniejącej definicji pola. Możesz rozważyć pracę z podzbiorem danych, aby szybciej tworzyć kompilacje. 

> [!Tip]
> Kod, a nie podejście portalu, jest zalecany do pracy nad projektem indeksu i importem danych jednocześnie. Alternatywnie narzędzia takie jak [Poster i Visual Studio Code](search-get-started-rest.md) są przydatne do testowania koncepcji, gdy projekty programistyczne są nadal w fazie wczesnych faz. Możesz wprowadzić przyrostowe zmiany definicji indeksu w treści żądania, a następnie wysłać żądanie do usługi, aby ponownie utworzyć indeks przy użyciu zaktualizowanego schematu.

## <a name="index-schema"></a>Schemat indeksu

Indeks musi mieć nazwę i jedno wskazane pole klucza (w modelu EDM. String) w kolekcji Fields. [*Kolekcja Fields*](#fields-collection) jest zwykle największą częścią indeksu, gdzie każde pole ma nazwę, wpisano i jest przypisane do dozwolonych zachowań, które określają sposób ich używania. 

Inne elementy obejmują [sugestie](#suggesters), [Profile oceniania](#scoringprofiles), [analizatory](#analyzers) używane do przetwarzania ciągów do tokenów zgodnie z regułami językowymi lub innymi charakterystykami obsługiwanymi przez analizator, a także ustawienia funkcji [tworzenia skryptów zdalnych (CORS) między różnymi źródłami](#corsoptions) .

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

Pola mają nazwę, typ, który klasyfikuje przechowywane dane, oraz atrybuty, które określają sposób używania pola.

### <a name="data-types"></a>Typy danych

| Typ | Opis |
|------|-------------|
| Edm.String |Tekst, który można opcjonalnie uzyskać tokeny na potrzeby wyszukiwania pełnotekstowego (dzielenia wyrazów, szukania rdzeni itd.). |
| Collection(Edm.String) |Lista ciągów, które opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego. Nie ma teoretycznej górnej granicy liczby elementów w kolekcji, ale rozmiar ładunku w kolekcjach jest ograniczony do 16 MB. |
| Edm.Boolean |Zawiera wartości prawda/fałsz. |
| Edm.Int32 |32-bitowe wartości całkowite. |
| Edm.Int64 |64-bitowe wartości całkowite. |
| Edm.Double |Dane liczbowe o podwójnej precyzji. |
| Edm.DateTimeOffset |Wartości daty i godziny reprezentowane w formacie OData v4 (na przykład `yyyy-MM-ddTHH:mm:ss.fffZ` lub `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| Edm.GeographyPoint |Punkt przedstawiający lokalizację geograficzną na świecie. |

Aby uzyskać więcej informacji, zobacz [obsługiwane typy danych](/rest/api/searchservice/Supported-data-types).

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

## `analyzers`

Element analizatorzes ustawia nazwę analizatora języka, który ma być używany dla pola. Aby uzyskać więcej informacji na temat zakresu analizatorów dostępnych dla Ciebie, zobacz [Dodawanie analizatorów do indeksu wyszukiwanie poznawcze platformy Azure](search-analyzers.md). Analizatory mogą być używane tylko z polami z możliwością wyszukiwania. Gdy analizator zostanie przypisany do pola, nie można go zmienić, chyba że zostanie odbudowany indeks.

## `suggesters`

Sugerował to sekcja schematu, która określa, które pola w indeksie są używane do obsługi autouzupełniania lub zapytań typu "w wyszukiwaniach". Zazwyczaj częściowe ciągi wyszukiwania są wysyłane do [sugestii (interfejs API REST)](/rest/api/searchservice/suggestions) , podczas gdy użytkownik pisze zapytanie wyszukiwania, a interfejs API zwraca zestaw sugerowanych dokumentów lub fraz. 

Pola dodane do sugestii są używane do kompilowania terminów wyszukiwania z wyprzedzeniem. Wszystkie terminy wyszukiwania są tworzone podczas indeksowania i zapisywane oddzielnie. Aby uzyskać więcej informacji na temat tworzenia struktury sugerującej, zobacz [Dodawanie sugestii](index-add-suggesters.md).

## `corsOptions`

Kod JavaScript po stronie klienta nie może domyślnie wywołać żadnych interfejsów API, ponieważ przeglądarka uniemożliwi wszystkie żądania między źródłami. Aby zezwolić na zapytania między źródłami do indeksu, Włącz funkcję CORS (Udostępnianie zasobów między źródłami) przez ustawienie atrybutu **corsOptions** . Ze względów bezpieczeństwa tylko interfejsy API zapytań obsługują mechanizm CORS. 

Dla mechanizmu CORS można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): jest to lista źródeł, do których zostanie udzielony dostęp do Twojego indeksu. Oznacza to, że każdy kod JavaScript obsługiwany z tych źródeł będzie mógł wysyłać zapytania do indeksu (przy założeniu, że zawiera on prawidłowy klucz API-Key). Każdy punkt początkowy ma zwykle postać, `protocol://<fully-qualified-domain-name>:<port>` chociaż `<port>` jest często pomijany. Aby uzyskać więcej informacji, zobacz [udostępnianie zasobów między źródłami (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Jeśli chcesz zezwolić na dostęp do wszystkich źródeł, Dołącz `*` jako pojedynczy element w tablicy **allowedOrigins** . *Nie jest to zalecane w przypadku usług wyszukiwania w środowisku produkcyjnym* , ale często jest to przydatne w przypadku programowania i debugowania.

+ **atrybut maxageinseconds** (opcjonalnie): przeglądarki używają tej wartości, aby określić czas (w sekundach), w którym są buforowane odpowiedzi na inspekcje wstępne. Ta wartość musi być nieujemną liczbą całkowitą. Im większa wartość to, tym lepsza wydajność, ale im dłużej zacznie obowiązywać zmiana zasad CORS. Jeśli nie jest ustawiona, zostanie użyty domyślny czas trwania wynoszący 5 minut.

## `scoringProfiles`

[Profil oceniania](index-add-scoring-profiles.md) to sekcja schematu, która definiuje niestandardowe zachowania oceniania, które pozwalają na wpływ, które elementy są wyświetlane w wynikach wyszukiwania. Profile oceniania składają się z wag pól i funkcji. Aby ich użyć, należy określić profil według nazwy w ciągu zapytania.

Domyślny profil oceniania działa w tle, aby obliczyć wynik wyszukiwania dla każdego elementu w zestawie wyników. Możesz użyć wewnętrznego, nienazwanego profilu oceniania. Alternatywnie można ustawić **defaultScoringProfile** , aby używał niestandardowego profilu jako domyślnego, wywołanego za każdym razem, gdy w ciągu zapytania nie określono profilu niestandardowego.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atrybuty i rozmiar indeksu (implikacje magazynu)

Rozmiar indeksu zależy od rozmiaru przekazywanych dokumentów, a także konfiguracji indeksu, takich jak to, czy są uwzględniane sugestie i jak ustawić atrybuty dla poszczególnych pól. 

Poniższy zrzut ekranu ilustruje wzorce magazynu indeksów, które wynikają z różnych kombinacji atrybutów. Indeks jest oparty na **przykładowym indeksie nieruchomości**, który można łatwo utworzyć przy użyciu Kreatora importu danych. Chociaż schematy indeksów nie są wyświetlane, można wywnioskować atrybuty na podstawie nazwy indeksu. Na przykład indeks z *możliwością wyszukiwania realestate* ma wybrany atrybut "możliwy do przeszukiwania" i nic nie jest, a w przypadku indeksu, który można *pobrać* , ma wybrany atrybut "umożliwiający pobieranie" i nic innego i tak dalej.

![Rozmiar indeksu na podstawie wybranego atrybutu](./media/search-what-is-an-index/realestate-index-size.png "Rozmiar indeksu na podstawie wybranego atrybutu")

Chociaż te warianty indeksów są sztuczne, możemy odnieść się do nich w celu uzyskania szerokiego porównania wpływu atrybutów na magazyn. Czy ustawienie "pobierania" zwiększa rozmiar indeksu? Nie. Czy dodanie pól do **sugestii** zwiększa rozmiar indeksu? Tak.

Indeksy obsługujące filtrowanie i sortowanie są proporcjonalnie większe niż indeksy obsługujące tylko wyszukiwanie pełnotekstowe. Wynika to z tego, że operacje filtrowania i sortowania sprawdzają dokładne dopasowania, wymagając obecności ciągów tekstowych Verbatim. Natomiast pola z możliwością wyszukiwania obsługujące zapytania pełnotekstowe używają odwróconych indeksów, które są wypełniane za pomocą tokenów, które zużywają mniej miejsca niż całe dokumenty. 

> [!Note]
> Architektura magazynu jest uważana za szczegóły implementacji platformy Azure Wyszukiwanie poznawcze i może ulec zmianie bez powiadomienia. Nie ma żadnej gwarancji, że bieżące zachowanie będzie nadal występowało w przyszłości.

## <a name="next-steps"></a>Następne kroki

Dzięki zrozumieniu kompozycji indeksów można kontynuować w portalu, aby utworzyć swój pierwszy indeks. Zalecamy rozpoczęcie od kreatora **importu danych** , wybierając *realestate-US-Sample* lub *Hotele-przykładowe* hostowane źródła danych.

> [!div class="nextstepaction"]
> [Kreator importu danych (Portal)](search-get-started-portal.md)

W przypadku obu zestawów danych Kreator może wywnioskować schemat indeksu, zaimportować dane i wyprowadzić indeks wyszukiwania, który można wykonać za pomocą Eksploratora wyszukiwania. Znajdź te źródła danych na stronie **Połącz z danymi** w kreatorze **importu danych** .

   ![Tworzenie przykładowego indeksu](media/search-what-is-an-index//import-wizard-sample-data.png "Tworzenie przykładowego indeksu")