---
title: Synonimy dla rozszerzenia zapytania w indeksie wyszukiwania
titleSuffix: Azure Cognitive Search
description: Utwórz mapę synonimów, aby rozszerzyć zakres zapytania wyszukiwania na indeks Wyszukiwanie poznawcze platformy Azure. Zakres jest rozszerzony w celu uwzględnienia odpowiednich warunków na liście.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: b62621a77f383b5c6413e7c187e7ba3d60beabad
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732091"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonimy w usłudze Azure Wyszukiwanie poznawcze

Przy użyciu map synonimów można kojarzyć równoważne warunki, rozszerzając zakres zapytania bez konieczności faktycznego dostarczania terminu. Na przykład załóżmy, że "Dog", "Canine" i "Puppy" są synonimami, zapytanie w "Canine" będzie zgodne z dokumentem zawierającym "Dog".

## <a name="create-synonyms"></a>Utwórz synonimy

Mapa synonimu to element zawartości, który może być tworzony raz i używany przez wiele indeksów. [Warstwa usług](search-limits-quotas-capacity.md#synonym-limits) określa, jak wiele różnych map synonimów można utworzyć, z przedziału od 3 do warstwy Bezpłatna i podstawowa, do 20 dla warstw Standard. 

Możesz tworzyć wiele synonimów map dla różnych języków, takich jak wersje angielskie i francuskie, lub leksykony, jeśli zawartość zawiera terminologię techniczną lub zasłoniętą. Chociaż można utworzyć wiele map synonimów, obecnie pole może korzystać tylko z jednego z nich.

Mapa synonimu składa się z nazwy, formatu i reguł, które działają jako pozycje mapy synonimów. Jedynym obsługiwanym formatem jest `solr` , a `solr` Format określa konstruowanie reguły.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Aby utworzyć mapę synonimów, użyj [tworzenia mapy synonimów (interfejs API REST)](/rest/api/searchservice/create-synonym-map) lub zestawu Azure SDK. W przypadku deweloperów języka C# zalecamy rozpoczęcie od [dodania synonimów w usłudze Azure poznawcze wyszukiwanie przy użyciu języka C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definiowanie reguł

Reguły mapowania są zgodne ze specyfikacją filtra synonimu typu "open source" w artykule Apache Solr, opisanym w tym dokumencie: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). `solr` Format obsługuje dwa rodzaje reguł:

+ równoważność (gdzie warunki są równorzędne substytuty w zapytaniu)

+ jawne mapowania (warunki są mapowane na jeden jawny termin przed wykonaniem zapytania)

Każda reguła musi być rozdzielone znakiem nowego wiersza ( `\n` ). Można zdefiniować maksymalnie 5 000 reguł na potrzeby mapowania synonimów w ramach bezpłatnej usługi i reguł 20 000 na mapę w innych warstwach. Każda reguła może mieć do 20 rozszerzeń (lub elementów w regule). Aby uzyskać więcej informacji, zobacz [limity synonimów](search-limits-quotas-capacity.md#synonym-limits).

Analizatory zapytań będą w małych przypadkach małymi lub mieszanymi terminami, ale jeśli chcesz zachować znaki specjalne w ciągu, takie jak przecinek lub kreska, Dodaj odpowiednie znaki ucieczki podczas tworzenia mapy synonimów. 

### <a name="equivalency-rules"></a>Reguły równoważności

Reguły dotyczące równoważnych terminów są rozdzielane przecinkami w ramach tej samej reguły. W pierwszym przykładzie zapytanie na `USA` zostanie rozwinięte do `USA` lub `"United States"` lub `"United States of America"` . Zwróć uwagę, że jeśli chcesz dopasować frazę, sama kwerenda musi być zapytaniem o frazę zawartą w cudzysłowie.

W przypadku równoważności, zapytanie dla `dog` spowoduje rozwinięcie zapytania, aby uwzględnić `puppy` i `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Jawne mapowanie

Reguły dla jawnego mapowania są oznaczone strzałką `=>` . Gdy jest określony, terminowa sekwencja zapytania wyszukiwania odpowiadającego lewej stronie `=>` zostanie zastąpiona alternatywami po prawej stronie w czasie wykonywania zapytania.

W jawnym przypadku zapytanie dla `Washington` `Wash.` lub `WA` zostanie napisaną jako `WA` , a aparat zapytań szuka tylko dopasowań w danym okresie `WA` . Jawne mapowanie ma zastosowanie tylko w określonym kierunku i nie zapisuje zapytania `WA` do `Washington` w tym przypadku.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Znaki specjalne ucieczki

Jeśli konieczne jest zdefiniowanie synonimów zawierających przecinki lub inne znaki specjalne, można wyjść z nich przy użyciu ukośnika odwrotnego, takiego jak w poniższym przykładzie:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Ponieważ ukośnik odwrotny jest samym znakiem specjalnym w innych językach, takich jak JSON i C#, prawdopodobnie trzeba będzie dwukrotnie go zmienić. Na przykład kod JSON wysłany do interfejsu API REST dla powyższej mapy synonimu będzie wyglądać następująco:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Przekazuj mapy synonimów i zarządzaj nimi

Jak wspomniano wcześniej, można utworzyć lub zaktualizować mapę synonimu bez zakłócania obciążeń zapytań i indeksowania. Mapa synonimów jest obiektem autonomicznym (na przykład indeksami lub źródłami danych) i tak długo, jak nie jest używane żadne pole, aktualizacje nie powodują błędów indeksowania ani zapytań. Jednak po dodaniu mapy synonimu do definicji pola, jeśli następnie usuniesz mapę synonimów, wszelkie zapytania zawierające pola, których to dotyczy, zakończą się niepowodzeniem z błędem 404.

Tworzenie, aktualizowanie i usuwanie mapy synonimów jest zawsze operacją całego dokumentu, co oznacza, że nie można zaktualizować ani usunąć części mapy synonim przyrostowo. Aktualizacja nawet jednej reguły wymaga ponownego załadowania.

## <a name="assign-synonyms-to-fields"></a>Przypisywanie synonimów do pól

Po przekazaniu mapy synonimów można włączyć synonimy dla pól typu `Edm.String` lub `Collection(Edm.String)` , w polach posiadających `"searchable":true` . Jak wspomniano, definicja pola może używać tylko jednej mapy synonimów.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Zapytanie o równoważne lub mapowane pola

Dodanie synonimów nie powoduje nakładania nowych wymagań dotyczących konstruowania zapytań. Zapytania warunkowe i zwrotne można wydać podobnie jak przed dodaniem synonimów. Jedyną różnicą jest to, że jeśli w mapie synonimów istnieje termin zapytania, aparat zapytań poszerzy lub ponownie napisze termin lub frazę, w zależności od reguły.

## <a name="how-synonyms-interact-with-other-features"></a>Sposób współdziałania synonimów z innymi funkcjami

Funkcja synonimy ponownie zapisuje oryginalne zapytanie przy użyciu synonimów z operatorem OR. Z tego powodu wyróżnianie trafień i profile oceniania traktują pierwotny termin i synonimy jako równoważne.

Synonimy mają zastosowanie tylko do zapytań wyszukiwania i nie są obsługiwane dla filtrów, zestawów reguł, autouzupełniania lub sugestii. Autouzupełnianie i sugestie są oparte tylko na oryginalnym okresie; w odpowiedzi nie pojawiają się dopasowania synonimów.

Rozszerzenia synonimów nie mają zastosowania do terminów wyszukiwania w postaci symboli wieloznacznych; wyrażenia prefix, rozmyte i wyrażeń regularnych nie są rozwinięte.

Jeśli trzeba wykonać pojedyncze zapytanie, które stosuje rozwinięcie synonimu i symbole wieloznaczne, wyrażenie regularne lub rozmyte, można połączyć zapytania przy użyciu składni lub. Na przykład, aby połączyć synonimy z symbolami wieloznacznymi dla prostej składni zapytania, może to być termin `<query> | <query>*` .

Jeśli masz istniejący indeks w środowisku programistycznym (nieprodukcyjnym), eksperymentuj z małym słownikiem, aby dowiedzieć się, jak dodanie synonimów zmienia środowisko wyszukiwania, w tym wpływ na profile oceniania, wyróżnianie trafień i sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie mapy synonimów (interfejs API REST)](/rest/api/searchservice/create-synonym-map)