---
title: Tworzenie podstawowego zapytania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć żądanie zapytania w Wyszukiwanie poznawcze, które narzędzia i interfejsy API do użycia na potrzeby testowania i kodu oraz jak decyzje dotyczące zapytań zaczynają się od projektu indeksu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: a02d51d66b9d2b8bf3c08d4515713ecb062e0c8e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400220"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Tworzenie zapytania w usłudze Azure Wyszukiwanie poznawcze

Jeśli tworzysz zapytanie po raz pierwszy, w tym artykule opisano potrzebne narzędzia i interfejsy API, które metody są używane do tworzenia zapytania oraz sposób, w jaki struktura indeksu i zawartość mogą mieć wpływ na wyniki zapytań. Aby zapoznać się z wprowadzeniem do działania zapytania, Zacznij od [typów zapytań i kompozycji](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Wybieranie narzędzi i interfejsów API

Do utworzenia zapytania potrzebne jest narzędzie lub interfejs API. Dowolna z poniższych sugestii jest przydatna w przypadku testowania i obciążeń produkcyjnych.

| Metodologia | Opis |
|-------------|-------------|
| Portal| [Eksplorator wyszukiwania (Portal)](search-explorer.md) jest interfejsem zapytań w Azure Portal, w którym są uruchamiane zapytania dotyczące indeksów w podstawowej usłudze wyszukiwania. Portal udostępnia interfejs API REST w tle do operacji [wyszukiwania dokumentów](/rest/api/searchservice/search-documents) , ale nie może wywoływać autouzupełniania, sugestii ani wyszukiwania dokumentów.<br/><br/> Można wybrać dowolny indeks i wersję interfejsu API REST, w tym Podgląd. Ciąg zapytania może używać prostej lub pełnej składni z obsługą wszystkich parametrów zapytania (Filter, SELECT, searchFields itd.). W portalu, gdy otworzysz indeks, możesz współpracować z Eksploratorem wyszukiwania obok definicji JSON indeksu na kartach obok siebie, aby łatwo uzyskać dostęp do atrybutów pól. Sprawdź, jakie pola są do przeszukiwania, do sortowania, do filtrowania i do czego podczas testowania zapytań. <br/>Zalecane do wczesnego badania, testowania i weryfikacji. [Dowiedz się więcej.](search-explorer.md) |
| Narzędzia do testowania sieci Web| Program [Poster lub Visual Studio Code](search-get-started-rest.md) są silnymi wyborami w celu sformułowania żądania [przeszukiwania dokumentów](/rest/api/searchservice/search-documents) i innych żądań, w spoczynku. Interfejsy API REST obsługują wszystkie możliwe operacje programistyczne w usłudze Azure Wyszukiwanie poznawcze, a w przypadku korzystania z narzędzia, takiego jak Poster lub Visual Studio Code, można interaktywnie wysłać żądania, aby zrozumieć, jak działa funkcja przed inwestowania w kod. Narzędzie testowania sieci Web jest dobrym rozwiązaniem, jeśli nie masz uprawnień współautora lub administracyjne w Azure Portal. Tak długo, jak masz adres URL wyszukiwania i klucz interfejsu API zapytania, możesz użyć narzędzi do uruchamiania zapytań względem istniejącego indeksu. |
| Zestaw Azure SDK | Gdy wszystko jest gotowe do pisania kodu, można użyć bibliotek klienckich Azure.Search.Document w zestawach SDK platformy Azure dla platformy .NET, Python, JavaScript lub Java. Każdy zestaw SDK ma swój własny harmonogram wydania, ale można tworzyć indeksy zapytań we wszystkich z nich. <br/><br/>[SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku C#.  [Dowiedz się więcej.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku Python. [Dowiedz się więcej.](search-get-started-python.md)<br/><br/>[SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) może służyć do wykonywania zapytań względem indeksu wyszukiwania w języku JavaScript. [Dowiedz się więcej.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Konfigurowanie klienta wyszukiwania

Klient wyszukiwania jest uwierzytelniany w usłudze wyszukiwania, wysyła żądania i obsługuje odpowiedzi. Niezależnie od tego, jakiego narzędzia lub interfejsu API używasz, klient wyszukiwania musi mieć następujące elementy:

| Właściwości | Opis |
|------------|-------------|
| Punkt końcowy | Usługa wyszukiwania ma adres URL w formacie: `https://[service-name].search.windows.net` . |
| Klucz dostępu interfejsu API (administrator lub zapytanie) | Uwierzytelnia żądanie w usłudze wyszukiwania. |
| Nazwa indeksu | Zapytania są zawsze kierowane do kolekcji dokumentów o pojedynczym indeksie. Nie można przyłączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako obiektów docelowych zapytań. |
| Wersja interfejsu API | Wywołania REST jawnie wymagają `api-version` żądania. Z kolei biblioteki klienckie w zestawie Azure SDK są zgodne z konkretną wersją interfejsu API REST. W przypadku zestawów SDK `api-version` jest to niejawne. |

### <a name="in-the-portal"></a>W portalu

Eksplorator wyszukiwania i inne narzędzia portalu mają wbudowane połączenie z usługą za pomocą bezpośrednich indeksów dostępu i innych obiektów ze stron portalu. Dostęp do narzędzi, kreatorów i obiektów wymaga członkostwa w roli współautor lub wyższej w usłudze. 

### <a name="using-rest"></a>Korzystanie z interfejsu REST

W przypadku wywołań REST można użyć programu [Poster lub podobnych narzędzi](search-get-started-rest.md) jako klienta, aby określić żądanie [przeszukiwania dokumentów](/rest/api/searchservice/search-documents) . Każde żądanie jest autonomiczne, więc należy podać punkt końcowy, nazwę indeksu i wersję interfejsu API dla każdego żądania. Inne właściwości, typ zawartości i klucz interfejsu API są przesyłane do nagłówka żądania. 

Możesz użyć pozycji POST lub GET, aby zbadać indeks. WPIS z parametrami określonymi w treści żądania jest łatwiejszy w obejść z. Jeśli używasz wpisu, pamiętaj o uwzględnieniu `docs/search` w adresie URL:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Korzystanie z zestawów SDK platformy Azure

Jeśli używasz zestawu Azure SDK, utworzysz klienta w kodzie. Wszystkie zestawy SDK zapewniają klientom wyszukiwania, którzy mogą utrzymywać stan utrwalania, umożliwiając ponowne użycie połączenia. Dla operacji zapytania można utworzyć wystąpienie obiektu **`SearchClient`** i podać wartości dla następujących właściwości: punkt końcowy, klucz, indeks. Następnie można wywołać metodę w **`Search method`** celu przekazania ciągu zapytania. 

| Język | Klient | Przykład |
|----------|--------|---------|
| C# i .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Wyślij pierwsze zapytanie wyszukiwania w języku JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Wybierz parser: prosty | szczegółowe

Jeśli zapytanie jest wyszukiwaniem pełnotekstowym, parser zostanie użyty do przetworzenia zawartości parametru wyszukiwania. Usługa Azure Wyszukiwanie poznawcze oferuje dwa analizatory zapytań. Prosty parser rozpoznaje [prostą składnię zapytania](query-simple-syntax.md). Ten parser został wybrany jako domyślny dla swojej szybkości i wydajności w bezpłatnych zapytaniach tekstowych. Składnia obsługuje typowe Operatory wyszukiwania (i, nie) do wyszukiwania terminów i fraz, a `*` także funkcję wyszukiwania prefiksu () (jak w "Sea *" dla Seattle i Seaside). Ogólnym zaleceniem jest najpierw wypróbowanie prostego analizatora, a następnie przejście do pełnego analizatora, jeśli wymagania aplikacji wywołują bardziej zaawansowane zapytania.

[Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po dodaniu `queryType=full` żądania, jest oparta na [analizatorze Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Pełna składnia to rozszerzenie prostej składni zawierającej więcej operatorów, dzięki czemu można skonstruować zaawansowane zapytania, takie jak Wyszukiwanie rozmyte, wyszukiwanie przy użyciu symboli wieloznacznych, wyszukiwanie w sąsiedztwie i wyrażenia regularne. Poniższe przykłady ilustrują punkt: to samo zapytanie, ale z różnymi **`queryType`** ustawieniami, które dają różne wyniki. W pierwszym prostym zapytaniu polecenie `^3` After `historic` jest traktowane jako część wyszukiwanego terminu. Górny wynik tego zapytania to "Marquis plac & Suites", który ma *Ocean* w opisie.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

To samo zapytanie używające pełnego analizatora Lucene interpretuje `^3` jako Detonator długoterminowy jako pole. Przełączenie analizatorów zmienia rangę, z wynikami zawierającymi termin *historyczny* w górnej części.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="choose-query-methods"></a>Wybierz metody zapytania

Wyszukiwanie jest zasadniczo wykonywane przez użytkownika, gdzie warunki lub frazy są zbierane z pola wyszukiwania lub z zdarzeń kliknięcia na stronie. Poniższa tabela zawiera podsumowanie mechanizmów, za pomocą których można zbierać dane wejściowe użytkownika oraz oczekiwanego środowiska wyszukiwania.

| Dane wejściowe | Środowisko użytkownika |
|-------|---------|
| [Search — Metoda](/rest/api/searchservice/search-documents) | Użytkownik wpisze terminy lub frazy w polu wyszukiwania, z operatorami lub bez, a następnie klika pozycję Wyszukaj, aby wysłać żądanie. Wyszukiwanie może być używane z filtrami w tym samym żądaniu, ale nie z autouzupełnianiem lub sugestiami. |
| [Autouzupełnianie — Metoda](/rest/api/searchservice/autocomplete) | Użytkownik wpisze kilka znaków, a zapytania są inicjowane po wpisaniu każdego nowego znaku. Odpowiedź jest zakończonym ciągiem z indeksu. Jeśli podany ciąg jest prawidłowy, użytkownik klika polecenie Wyszukaj, aby wysłać zapytanie do usługi. |
| [Sugestie — Metoda](/rest/api/searchservice/suggestions) | Tak jak w przypadku funkcji Autouzupełnianie, użytkownik wybiera kilka znaków i są generowane przyrostowe zapytania. Odpowiedź to lista rozwijana pasujących dokumentów, zazwyczaj reprezentowana przez kilka unikatowych lub opisowych pól. Jeśli którykolwiek z opcji jest prawidłowy, użytkownik klika jeden i zostanie zwrócony pasujący dokument. |
| [Nawigacja aspektowa](/rest/api/searchservice/search-documents#query-parameters) | Na stronie wyświetlane są linki nawigacji lub struktury nawigacyjne, które zawężają zakres wyszukiwania. Struktura nawigacji aspektowej składa się dynamicznie na podstawie wstępnego zapytania. Na przykład, `search=*` Aby wypełnić drzewo nawigacji aspektów składające się z każdej możliwej kategorii. Struktura nawigacji aspektów jest tworzona na podstawie odpowiedzi na zapytanie, ale jest również mechanizmem do wyrażania następnego zapytania. odwołanie do interfejsu API REST, `facets` jest udokumentowane jako parametr zapytania operacji wyszukiwania dokumentów, ale można go użyć bez `search` parametru.|
| [Filter — Metoda](/rest/api/searchservice/search-documents#query-parameters) | Filtry są używane z aspektami w celu zawężenia wyników. Można również zaimplementować filtr związany ze stroną, na przykład w celu zainicjowania strony przy użyciu pól specyficznych dla języka. W dokumentacji interfejsu API REST `$filter` jest udokumentowany jako parametr zapytania operacji wyszukiwania dokumentów, ale można go użyć bez `search` parametru.|

## <a name="know-your-field-attributes"></a>Poznaj atrybuty pola

Jeśli wcześniej sprawdzono [podstawowe informacje o żądaniu zapytania](search-query-overview.md), można pamiętać, że parametry żądania zapytania zależą od tego, jak pola są przypisane do indeksu. Na przykład, aby można było używać ich w kwerendzie, filtrze lub porządku sortowania, pole musi być możliwe do *przeszukiwania*, *filtrowania* i *sortowania*. Podobnie tylko pola oznaczone jako możliwe do *pobierania* mogą być wyświetlane w wynikach. Po rozpoczęciu określania `search` parametrów, `filter` i `orderby` w żądaniu upewnij się, że atrybuty są sprawdzane w celu uniknięcia nieoczekiwanych wyników.

Na poniższym zrzucie ekranu portalu można używać [](search-get-started-portal.md)tylko ostatnich dwóch pól "zapytaniu lastrenovationdate" i "Rating" w `"$orderby"` klauzuli Only.

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Aby uzyskać opis atrybutów pól, zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Poznaj swoje tokeny

Podczas indeksowania aparat zapytań używa analizatora do przeprowadzenia analizy tekstu dla ciągów, maksymalizując potencjał do dopasowania w czasie wykonywania zapytania. Minimalna wielkość liter w ciągach jest niższa, ale mogą one być również poddawane Lematyzacja i zatrzymywanie usuwania wyrazów. Większe ciągi lub wyrazy złożone są zwykle podzielone na spacje, łączniki lub łączniki, a także indeksowane jako oddzielne tokeny. 

W tym miejscu warto pamiętać, że zawartość Twojego indeksu zawiera, a co w rzeczywistości może być inna. Jeśli zapytania nie zwracają oczekiwanych wyników, można sprawdzić tokeny utworzone przez analizator za pomocą [analizy tekstu (interfejs API REST)](/rest/api/searchservice/test-analyzer). Aby uzyskać więcej informacji na temat tokenizacji i wpływu na zapytania, zobacz [częściowe wyszukiwanie terminów i wzorce przy użyciu znaków specjalnych](search-query-partial-matching.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz lepsze zrozumienie sposobu tworzenia żądania zapytania, spróbuj skorzystać z następujących przewodników szybki start dla wygody środowiska.

+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w usłudze REST](search-get-started-rest.md)
+ [Jak wykonywać zapytania w programie .NET](search-get-started-dotnet.md)
+ [Jak wykonywać zapytania w języku Python](search-get-started-python.md)
+ [Jak wykonywać zapytania w języku JavaScript](search-get-started-javascript.md)