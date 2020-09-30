---
title: Odwołanie do składni wzorca — LUIS
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane są używane przez aplikację kliencką.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542193"
---
# <a name="pattern-syntax"></a>Składnia wzorca

Składnia wzorca jest szablonem dla elementu wypowiedź. Szablon powinien zawierać słowa i jednostki, które mają być dopasowane, a także słowa i [znaki interpunkcyjne](luis-reference-application-settings.md#punctuation-normalization) , które mają być ignorowane. To **nie** jest wyrażenie regularne.

> [!CAUTION]
> Wzorce obejmują tylko nadrzędne jednostki uczenia maszynowego, a nie podjednostki.

Jednostki we wzorcu są ujęte w nawiasy klamrowe `{}` . Wzorce mogą obejmować jednostki i jednostki z rolami. [Wzorzec. any](luis-concept-entity-types.md#patternany-entity) jest jednostką używaną tylko w wzorcach.

Składnia wzorca obsługuje następującą składnię:

|Funkcja|Składnia|Poziom zagnieżdżenia|Przykład|
|--|--|--|--|
|jednostka| {} — nawiasy klamrowe|2|Gdzie jest formularz {Entity-Name}?|
|optional|[] — nawiasy kwadratowe<BR><BR>Istnieje limit 3 na poziomach zagnieżdżenia dowolnej kombinacji opcjonalne i grupowania |2|Znak zapytania jest opcjonalny [?]|
|grouping|() — nawiasy|2|jest (a \| b)|
|lub| \| -pionowy pasek (potok)<br><br>Istnieje limit 2 na pionowych paskach (lub) w jednej grupie |-|WHERE jest formularzem ({form-Name-Short} &#x7c; {forma-Name-Long} &#x7c; {form-Number})|
|Początek i/lub koniec elementu wypowiedź|^ — karetka|-|^ Rozpocznij wypowiedź<br>wypowiedź wykonuje ^<br>{ścisłe dopasowanie literału dla całej wypowiedź z {Number} jednostką ^|

## <a name="nesting-syntax-in-patterns"></a>Zagnieżdżanie składni w wzorcach

**Opcjonalna** składnia, z nawiasami kwadratowymi, może być zagnieżdżona na dwa poziomy. Na przykład: `[[this]is] a new form`. Ten przykład umożliwia korzystanie z następujących wyrażenia długości:

|Zagnieżdżony opcjonalny przykład wypowiedź|Objaśnienie|
|--|--|
|to jest nowy formularz|dopasowuje wszystkie wyrazy we wzorcu|
|jest nowym formularzem|dopasowuje zewnętrzne słowo opcjonalne i słowa inne niż opcjonalne we wzorcu|
|nowy formularz|Dopasowuje tylko wymagane wyrazy|

Składnia **grupowania** z nawiasami może być zagnieżdżona na dwa poziomy. Na przykład: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ta funkcja umożliwia dopasowanie dowolnej z trzech jednostek.

Jeśli jednostki Entity1 jest lokalizacją z rolami, takimi jak Origin (Seattle) i Destination (Kair), a Entity 2 to znana nazwa budynku z jednostki listy (RedWest-C), następująca wyrażenia długości zostałaby zamapowana na ten wzorzec:

|Przykład zagnieżdżonej wypowiedź grupowania|Objaśnienie|
|--|--|
|RedWest-C|dopasowuje zewnętrzną jednostkę grupowania|
|Seattle|dopasowuje jedną z wewnętrznych jednostek grupowania|
|Cairo|dopasowuje jedną z wewnętrznych jednostek grupowania|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limity zagnieżdżania dla grup z opcjonalną składnią

Kombinacja **grupowania** z **opcjonalną** składnią ma limit 3 poziomów zagnieżdżenia.

|Dozwolone|Przykład|
|--|--|
|Tak|([(TEST1 &#x7c; TEST2)] &#x7c; test3)|
|Nie|([([TEST1] &#x7c; TEST2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limity zagnieżdżania dla grup ze składnią lub-w

Kombinacja **grupowania** z składnią **lub-** w ma limit 2 pionowych słupków.

|Dozwolone|Przykład|
|--|--|
|Tak|(TEST1 &#x7c; TEST2 &#x7c; (test3 &#x7c; test4))|
|Nie|(TEST1 &#x7c; TEST2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki do szablonu wzorca
Aby dodać jednostkę do szablonu wzorca, należy ująć nazwę jednostki za pomocą nawiasów klamrowych, takich jak `Who does {Employee} manage?` .

|Wzorzec z jednostką|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Składnia służąca do dodawania jednostki i roli do szablonu wzorca
Rola jednostki jest oznaczona jako `{entity:role}` Nazwa jednostki, po której następuje dwukropek, a następnie nazwa roli. Aby dodać jednostkę z rolą do szablonu wzorca, należy ująć nazwę jednostki i nazwę roli za pomocą nawiasów klamrowych, takich jak `Book a ticket from {Location:Origin} to {Location:Destination}` .

|Wzorzec z rolami jednostek|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Składnia służąca do dodawania wzorca. dowolny do szablonu wzorca
Wzorzec. Każda jednostka umożliwia dodanie jednostki o różnej długości do wzorca. Tak długo, jak następuje szablon wzorca, wzorzec. any może być dowolną długością.

Aby dodać **wzorzec. dowolna** jednostka do szablonu wzorca, otaczająca wzorzec. Każda jednostka z nawiasami klamrowymi, taka jak `How much does {Booktitle} cost and what format is it available in?` .

|Wzorzec ze wzorcem. dowolna jednostka|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Tytuły książek we wzorcu|
|--|
|Ile kosztuje **ten koszt książki** i w jakim formacie jest dostępny?|
|Ile jest **poproszonych** kosztów i w jakim formacie jest dostępny?|
|Jak dużo trwa **chcesz wiedzieć zdarzenia w czasie nocnym i w** jakim formacie jest dostępny?|

Słowa tytułu książki nie są mylące dla LUIS, ponieważ LUIS wie, gdzie się znajduje tytuł książki, na podstawie wzorca. dowolna jednostka.

## <a name="explicit-lists"></a>Listy jawne

Utwórz [jawną listę](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) za pomocą interfejsu API tworzenia, aby zezwolić na wyjątek, gdy:

* Wzorzec zawiera [wzorzec. any](luis-concept-entity-types.md#patternany-entity)
* Składnia wzorca pozwala na możliwość nieprawidłowej ekstrakcji jednostek na podstawie wypowiedź.

Załóżmy na przykład, że istnieje wzorzec zawierający zarówno składnię opcjonalną, `[]` , jak i składnię jednostki, `{}` połączone w sposób nieprawidłowego wyodrębnienia danych.

Rozważmy wiadomość e-mail z wzorcem "[Find]" {subject} [od {Person}] ".

W poniższym wyrażenia długości jednostka **podmiotu** i **osoba osoby** są wyodrębniane poprawnie i niepoprawnie:

|Wypowiedź|Jednostka|Poprawna Ekstrakcja|
|--|--|:--:|
|Wyślij wiadomość e-mail na temat psów z Krzysztof|subject = psy<br>osoba = Krzysztof|✔|
|Wyślij wiadomość e-mail na temat Man z La Mancha|podmiot = człowiek<br>osoba = La Mancha|X|

W powyższej tabeli podmiot powinien być `the man from La Mancha` (tytułem książki), ale ponieważ temat zawiera słowo opcjonalne `from` , tytuł jest nieprawidłowo przewidziany.

Aby naprawić ten wyjątek dla wzorca, Dodaj `the man from la mancha` jako jawne dopasowanie listy dla jednostki {subject} za pomocą [interfejsu API tworzenia dla jawnej listy](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Składnia służąca do oznaczania tekstu opcjonalnego w szablonie wypowiedź
Oznacz opcjonalny tekst w wypowiedź przy użyciu składni nawiasu kwadratowego wyrażenia regularnego `[]` . Tekst opcjonalny może zagnieżdżać nawiasy kwadratowe tylko do dwóch nawiasów.

|Wzorzec z opcjonalnym tekstem|Znaczenie|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` i `from {person}` są opcjonalne|
|"Czy jesteś w stanie pomóc mi [?]|Znak interpunkcyjny jest opcjonalny|

Znaki interpunkcyjne ( `?` , `!` , `.` ) powinny być ignorowane i należy je zignorować przy użyciu składni nawiasów kwadratowych w wzorcach.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Jak dodać wzorce](luis-how-to-model-intent-pattern.md)
* [Jak dodać wzorzec. dowolna jednostka](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Koncepcje wzorców](luis-concept-patterns.md)

Dowiedz się, w jaki sposób [tonacji](luis-reference-prebuilt-sentiment.md) jest zwracany w odpowiedzi. JSON.