---
title: Zwróć odpowiedź semantyczną
titleSuffix: Azure Cognitive Search
description: Opisuje składową odpowiedzi semantycznej i sposób uzyskiwania odpowiedzi z zestawu wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722433"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Zwracanie semantyki odpowiedzi na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Wyszukiwanie semantyczne jest w publicznej wersji zapoznawczej, dostępne tylko za pomocą interfejsu API REST w wersji zapoznawczej. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Te funkcje są rozliczane. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

W przypadku tworzenia [zapytania semantycznego](semantic-how-to-query-request.md)można opcjonalnie wyodrębnić zawartość z najbardziej pasujących dokumentów, które są "odpowiedzi" bezpośrednio na zapytanie. Odpowiedź może zawierać jedną lub więcej odpowiedzi, którą można następnie renderować na stronie wyszukiwania, aby ulepszyć środowisko użytkownika aplikacji.

W tym artykule dowiesz się, jak zażądać odpowiedzi semantycznej, rozpakować odpowiedzi i dowiedzieć się, jakie charakterystyki zawartości najbardziej sprzyjają tworzeniu odpowiedzi o wysokiej jakości.

## <a name="prerequisites"></a>Wymagania wstępne

Wszystkie wymagania wstępne dotyczące [zapytań semantycznych](semantic-how-to-query-request.md) dotyczą także odpowiedzi, w tym warstwy usług i regionu.

+ Logika zapytania musi zawierać parametry kwerendy semantycznej oraz parametr "Answers". Parametry wymagane zostały omówione w tym artykule.

+ Ciągi zapytania wprowadzone przez użytkownika muszą być sformułowane w języku, który ma cechy pytania (co to jest, gdzie, kiedy, jak).

+ Dokumenty wyszukiwania muszą zawierać tekst mający charakterystykę odpowiedzi i ten tekst musi znajdować się w jednym z pól wymienionych w "searchFields". Na przykład, mając zapytanie "co to jest tabela skrótów", jeśli żadna z searchFields nie zawiera żadnych fragmentów, które zawierają "tablicę skrótów to...", nie będzie można zwrócić odpowiedzi.

## <a name="what-is-a-semantic-answer"></a>Czym jest odpowiedź semantyczna?

Odpowiedź semantyczna to podstruktura [odpowiedzi na zapytanie semantyczne](semantic-how-to-query-request.md). Składa się z co najmniej jednego fragmentu Verbatim z dokumentu wyszukiwania, sformułowany jako odpowiedź na zapytanie, które wygląda jak pytanie. Aby można było zwrócić odpowiedź, frazy lub zdania muszą znajdować się w dokumencie wyszukiwania, który ma charakterystykę języka odpowiedzi, a zapytanie musi być pożądane jako pytanie.

W celu wybrania najlepszej odpowiedzi Wyszukiwanie poznawcze używać modelu z czytaniem informacji na komputerze. Model tworzy zestaw potencjalnych odpowiedzi z dostępnej zawartości i gdy osiągnie wysoki poziom ufności, zaproponuje odpowiedź.

Odpowiedzi są zwracane jako niezależny obiekt najwyższego poziomu w ładunku odpowiedzi na zapytania, który można wybrać do renderowania na stronach wyszukiwania po stronie wyników wyszukiwania. Strukturalnie jest to element Array w odpowiedzi składającej się z tekstu, klucza dokumentu i oceny zaufania.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Jak zażądać odpowiedzi semantycznych w zapytaniu

Aby można było zwrócić semantykę odpowiedzi, zapytanie musi mieć semantykę "querytype", "queryLanguage", "searchFields" i parametr "Answers". Określenie parametru "Answers" nie gwarantuje, że otrzymasz odpowiedź, ale żądanie musi zawierać ten parametr, jeśli przetwarzanie odpowiedzi ma być wywoływane.

Parametr "searchFields" ma kluczowe znaczenie dla zwrócenia odpowiedzi o wysokiej jakości, zarówno w przypadku zawartości, jak i kolejności (patrz poniżej). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Ciąg zapytania nie może mieć wartości null i powinien być sformułowany jako pytanie. W tej wersji zapoznawczej wartości "querytype" i "queryLanguage" muszą być ustawione dokładnie tak, jak pokazano w przykładzie.

+ Parametr "searchFields" określa, które pola ciągów dostarczają tokeny do modelu wyodrębniania. Te same pola, które tworzą podpisy, również generują odpowiedzi. Aby uzyskać precyzyjne wskazówki dotyczące sposobu ustawiania tego pola tak, aby działały zarówno dla napisów, jak i odpowiedzi, zobacz [Set searchFields](semantic-how-to-query-request.md#searchfields). 

+ W przypadku "odpowiedzi" konstrukcja parametru jest `"answers": "extractive"` , gdzie zwracana jest domyślna liczba odpowiedzi. Liczbę odpowiedzi można zwiększyć, dodając liczbę, jak pokazano w powyższym przykładzie, maksymalnie pięć.  Niezależnie od tego, czy potrzebujesz więcej niż jednej odpowiedzi, zależy od środowiska użytkownika aplikacji i sposobu renderowania wyników.

## <a name="deconstruct-an-answer-from-the-response"></a>Dekonstrukcja odpowiedzi z odpowiedzi

Odpowiedzi są podane w @search.answers tablicy, która pojawia się w pierwszej kolejności w odpowiedzi. Jeśli odpowiedź jest nieokreślona, odpowiedź zostanie wyświetlona jako `"@search.answers": []` . Podczas projektowania strony wyników wyszukiwania, która zawiera odpowiedzi, pamiętaj, aby obsługiwać przypadki, w których nie można znaleźć odpowiedzi.

W programie @search.answers "klucz" jest kluczem dokumentu lub identyfikatorem dopasowania. Za pomocą interfejsu API [dokumentu wyszukiwania](/rest/api/searchservice/lookup-document) można pobrać wszystkie lub wszystkie części dokumentu wyszukiwania do uwzględnienia na stronie wyszukiwania lub stronie szczegółowej.

Zarówno "text", jak i "wyróżnieni" zapewniają identyczną zawartość, zarówno w postaci zwykłego tekstu, jak i z wyróżnieniami. Domyślnie wyróżnione są style, `<em>` które można przesłonić przy użyciu istniejących parametrów highlightPreTag i highlightPostTag. Jak zauważono w innym miejscu, substancja odpowiedzi jest Verbatim zawartość z dokumentu wyszukiwania. Model wyodrębniania szuka właściwości odpowiedzi, aby znaleźć odpowiednią zawartość, ale nie redaguje nowego języka w odpowiedzi.

"Wynik" to wynik pewności, który odzwierciedla siłę odpowiedzi. Jeśli odpowiedź zawiera wiele odpowiedzi, ten wynik służy do określenia kolejności. Najważniejsze odpowiedzi i górne podpisy mogą pochodzić z różnych dokumentów wyszukiwania, w których najwyższej odpowiedzi pochodzi z jednego dokumentu, a górny podpis z innego, ale w ogólności zobaczysz te same dokumenty w górnych pozycjach w każdej tablicy.

Po odpowiedzi następuje Tablica "wartość", która zawsze zawiera wyniki, napisy i wszystkie pola, które są domyślnie dostępne do pobierania. Jeśli określono parametr SELECT, Tablica "value" jest ograniczona do określonych pól. Aby uzyskać więcej informacji na temat elementów w odpowiedzi, zobacz [Tworzenie kwerendy semantycznej](semantic-how-to-query-request.md).

W odpowiedzi na zapytanie "jak w chmurach" jest zwracana następująca odpowiedź:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Porady dotyczące tworzenia odpowiedzi o wysokiej jakości

Aby uzyskać najlepsze wyniki, zwróć odpowiedzi semantyczne w dokumencie korpus o następujących cechach:

+ element "searchFields" musi zawierać pola, które oferują wystarczającą ilość tekstu, w którym prawdopodobnie zostanie znaleziona odpowiedź. Tylko tekst Verbatim z dokumentu może być wyświetlany jako odpowiedź.

+ ciągi zapytania nie mogą mieć wartości null (Search = `*` ), a ciąg powinien mieć charakterystykę pytania, w przeciwieństwie do wyszukiwania słów kluczowych (sekwencyjna lista dowolnych terminów lub fraz). Jeśli ciąg zapytania nie wygląda na odpowiedź, przetwarzanie odpowiedzi jest pomijane, nawet jeśli żądanie określa "odpowiedzi" jako parametr zapytania.

+ Wyodrębnianie semantyczne i podsumowywanie ma limity dotyczące liczby tokenów na dokument w odpowiednim czasie. W praktyce, jeśli masz duże dokumenty, które są uruchamiane na setkach stron, należy najpierw spróbować podzielić zawartość na mniejsze dokumenty.

## <a name="next-steps"></a>Następne kroki

+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)
+ [Algorytm klasyfikacji semantycznej](semantic-ranking.md)
+ [Algorytm klasyfikacji podobieństwa](index-ranking-similarity.md)
+ [Tworzenie zapytania semantycznego](semantic-how-to-query-request.md)