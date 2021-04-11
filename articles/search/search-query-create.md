---
title: Tworzenie zapytania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć żądanie zapytania w Wyszukiwanie poznawcze, które narzędzia i interfejsy API do użycia na potrzeby testowania i kodu oraz jak decyzje dotyczące zapytań zaczynają się od projektu indeksu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951508"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Tworzenie zapytań w usłudze Azure Wyszukiwanie poznawcze

W przypadku tworzenia zapytania po raz pierwszy w tym artykule opisano podejścia i metody konfigurowania zapytań. Wprowadza również żądanie zapytania i wyjaśnia, jak atrybuty pola i analizatory lingwistyczne mogą wpływać na wyniki zapytania.

## <a name="whats-a-query-request"></a>Co to jest żądanie zapytania?

Zapytanie jest żądaniem tylko do odczytu w kolekcji docs jednego indeksu wyszukiwania. Określa parametr "Search" zawiera wyrażenie zapytania, składające się z wyrażeń i operatorów.

Dodatkowe parametry zapewniają większą definicję zapytania i odpowiedzi. Na przykład zakresy "searchFields" wykonywania zapytania do określonych pól, "Select" określa, które pola są zwracane w wynikach, a element "Count" zwraca liczbę dopasowań znalezionych w indeksie.

W poniższym przykładzie przedstawiono ogólny pomysł żądania zapytania, wyświetlając podzestaw dostępnych parametrów. Aby uzyskać więcej informacji na temat kompozycji zapytania, zobacz [typy zapytań i kompozycje](search-query-overview.md) oraz [Wyszukiwanie dokumentów (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Wybierz klienta

Potrzebujesz narzędzia, takiego jak Azure Portal lub Poster, lub kodu, który tworzy wystąpienie programu Query Client przy użyciu interfejsów API. Zalecamy używanie Azure Portal lub interfejsów API REST na potrzeby wczesnego tworzenia i testowania koncepcji.

### <a name="permissions"></a>Uprawnienia

Każda operacja, w tym żądania zapytań, będzie działała w [kluczu interfejsu API administratora](search-security-api-keys.md), ale żądania zapytań mogą opcjonalnie używać [klucza interfejsu API zapytań](search-security-api-keys.md#create-query-keys). Klucze interfejsu API zapytań są zdecydowanie zalecane. Można utworzyć do 50 na usługę i przypisać różne klucze do różnych aplikacji.

W Azure Portal dostęp do narzędzi, kreatorów i obiektów wymaga członkostwa w roli współautor lub powyżej w usłudze. 

### <a name="use-azure-portal-to-query-an-index"></a>Używanie Azure Portal do wykonywania zapytań względem indeksu

[Eksplorator wyszukiwania (Portal)](search-explorer.md) jest interfejsem zapytań w Azure Portal, w którym są uruchamiane zapytania dotyczące indeksów w podstawowej usłudze wyszukiwania. Wewnętrznie Portal wykonuje żądania [przeszukiwania dokumentów](/rest/api/searchservice/search-documents) , ale nie może wywoływać autouzupełniania, sugestii ani wyszukiwania dokumentów. 

Można wybrać dowolny indeks i wersję interfejsu API REST, w tym Podgląd. Ciąg zapytania może używać prostej lub pełnej składni z obsługą wszystkich parametrów zapytania (Filter, SELECT, searchFields itd.). W portalu, gdy otworzysz indeks, możesz współpracować z Eksploratorem wyszukiwania obok definicji JSON indeksu na kartach obok siebie, aby łatwo uzyskać dostęp do atrybutów pól. Sprawdź, jakie pola są do przeszukiwania, do sortowania, do filtrowania i do czego podczas testowania zapytań.

### <a name="use-a-rest-client"></a>Korzystanie z klienta REST

Zarówno program Poster, jak i Visual Studio Code (z rozszerzeniem systemu Azure Wyszukiwanie poznawcze) mogą działać jako klient zapytań. Za pomocą dowolnego z tych narzędzi można nawiązać połączenie z usługą wyszukiwania i wysyłać żądania [przeszukiwania dokumentów (REST)](/rest/api/searchservice/search-documents) . Wiele samouczków i przykładów przedstawia klientom REST wykonywanie zapytań dotyczących indeksowania. 

Zacznij od jednego z tych artykułów, aby dowiedzieć się więcej na temat każdego klienta (oba zawierają instrukcje dotyczące zapytań):

+ [Tworzenie indeksu wyszukiwania przy użyciu REST i programu Poster](search-get-started-rest.md)
+ [Wprowadzenie do Visual Studio Code i platformy Azure Wyszukiwanie poznawcze](search-get-started-vs-code.md)

Każde żądanie jest autonomiczne, więc należy podać punkt końcowy, nazwę indeksu i wersję interfejsu API dla każdego żądania. Inne właściwości, typ zawartości i klucz interfejsu API są przesyłane do nagłówka żądania. Aby uzyskać więcej informacji, zobacz [Przeszukaj dokumenty (REST)](/rest/api/searchservice/search-documents) , aby uzyskać pomoc dotyczącą tworzenia żądań zapytań.

### <a name="use-an-sdk"></a>Korzystanie z zestawu SDK

W przypadku Wyszukiwanie poznawcze zestawy SDK platformy Azure implementują ogólnie dostępne funkcje. W związku z tym można użyć dowolnego z zestawów SDK do wykonywania zapytań względem indeksu. Wszystkie z nich zapewniają **SearchClient** , który ma metody do współpracy z indeksem, od ładowania indeksu z dokumentami wyszukiwania do tworzenia żądań zapytań.

| Zestaw Azure SDK | Klient | Przykłady |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | Podjęcia. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. PR ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Wybierz typ zapytania: prosty | szczegółowe

Jeśli zapytanie jest wyszukiwaniem pełnotekstowym, Analizator zapytań zostanie użyty do przetworzenia dowolnego tekstu, który jest przesyłany jako wyszukiwane terminy i frazy. Usługa Azure Wyszukiwanie poznawcze oferuje dwa analizatory zapytań. 

+ Prosty parser rozpoznaje [prostą składnię zapytania](query-simple-syntax.md). Ten parser został wybrany jako domyślny dla swojej szybkości i wydajności w bezpłatnych zapytaniach tekstowych. Składnia obsługuje typowe Operatory wyszukiwania (i, nie) do wyszukiwania terminów i fraz, a `*` także funkcję wyszukiwania prefiksu () (jak w "Sea *" dla Seattle i Seaside). Ogólnym zaleceniem jest najpierw wypróbowanie prostego analizatora, a następnie przejście do pełnego analizatora, jeśli wymagania aplikacji wywołują bardziej zaawansowane zapytania.

+ [Pełna składnia zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), którą można włączyć po dodaniu `queryType=full` żądania, jest oparta na [analizatorze Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Pełna składnia i prosta składnia nakładają się na zakres, który obsługuje te same prefiksy i operacje logiczne, ale Pełna składnia zawiera więcej operatorów. W pełni istnieje więcej operatorów dla wyrażeń logicznych i więcej operatorów dla zaawansowanych zapytań, takich jak Wyszukiwanie rozmyte, wyszukiwanie przy użyciu symboli wieloznacznych, wyszukiwanie w sąsiedztwie i wyrażenia regularne.

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

Jeśli wcześniej sprawdzono [typy zapytań i ich kompozycje](search-query-overview.md), można pamiętać, że parametry żądania zapytania zależą od tego, jak pola są przypisane do indeksu. Na przykład, aby można było używać ich w kwerendzie, filtrze lub porządku sortowania, pole musi być możliwe do *przeszukiwania*, *filtrowania* i *sortowania*. Podobnie tylko pola oznaczone jako możliwe do *pobierania* mogą być wyświetlane w wynikach. Po rozpoczęciu określania `search` parametrów, `filter` i `orderby` w żądaniu upewnij się, że atrybuty są sprawdzane w celu uniknięcia nieoczekiwanych wyników.

Na poniższym zrzucie ekranu portalu można używać [](search-get-started-portal.md)tylko ostatnich dwóch pól "zapytaniu lastrenovationdate" i "Rating" w `"$orderby"` klauzuli Only.

![Definicja indeksu dla przykładu hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla przykładu hotelu")

Aby uzyskać opis atrybutów pól, zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Poznaj swoje tokeny

Podczas indeksowania, aparat wyszukiwania używa analizatora do przeprowadzenia analizy tekstu dla ciągów, maksymalizując potencjał do dopasowania w czasie wykonywania zapytania. Minimalna wielkość liter w ciągach jest niższa, ale mogą one być również poddawane Lematyzacja i zatrzymywanie usuwania wyrazów. Większe ciągi lub wyrazy złożone są zwykle podzielone na spacje, łączniki lub łączniki, a także indeksowane jako oddzielne tokeny. 

W tym miejscu warto pamiętać, że zawartość Twojego indeksu zawiera, a co w rzeczywistości może być inna. Jeśli zapytania nie zwracają oczekiwanych wyników, można sprawdzić tokeny utworzone przez analizator za pomocą [analizy tekstu (interfejs API REST)](/rest/api/searchservice/test-analyzer). Aby uzyskać więcej informacji na temat tokenizacji i wpływu na zapytania, zobacz [częściowe wyszukiwanie terminów i wzorce przy użyciu znaków specjalnych](search-query-partial-matching.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak działają żądania zapytań, wypróbuj następujące przewodniki szybki start dla wygody środowiska.

+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w usłudze REST](search-get-started-rest.md)
+ [Jak wykonywać zapytania w programie .NET](search-get-started-dotnet.md)
+ [Jak wykonywać zapytania w języku Python](search-get-started-python.md)
+ [Jak wykonywać zapytania w języku JavaScript](search-get-started-javascript.md)