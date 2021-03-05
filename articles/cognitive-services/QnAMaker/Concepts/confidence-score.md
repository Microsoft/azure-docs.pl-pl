---
title: Wynik pewności — QnA Maker
titleSuffix: Azure Cognitive Services
description: Gdy zapytanie użytkownika jest dopasowane do bazy wiedzy, QnA Maker zwraca odpowiednie odpowiedzi wraz z oceną ufności.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: fcfc70f7bfb9e2bb1b1a0edbffdc1282056e5fa7
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214052"
---
# <a name="the-confidence-score-of-an-answer"></a>Wynik pewności odpowiedzi
Gdy zapytanie użytkownika jest dopasowane do bazy wiedzy, QnA Maker zwraca odpowiednie odpowiedzi wraz z oceną ufności. Ten wynik wskazuje, że odpowiedź jest odpowiednim dopasowaniem dla danego zapytania użytkownika.

Wynik pewności jest liczbą z zakresu od 0 do 100. Wynik 100 prawdopodobnie pasuje do dokładnego, a wynik 0 oznacza, że nie znaleziono zgodnej odpowiedzi. Im wyższy wynik — tym większy poziom zaufania odpowiedzi. Dla danego zapytania można zwrócić wiele odpowiedzi. W takim przypadku odpowiedzi są zwracane w kolejności zmniejszania wyniku zaufania.

W poniższym przykładzie można zobaczyć jedną jednostkę QnA z 2 pytaniami.


![Przykładowa para QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Dla powyższego przykładu — możesz oczekiwać wyników, takich jak przykładowy zakres wyników poniżej — dla różnych typów zapytań użytkowników:


![Zakres oceny rangi](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Poniższa tabela wskazuje typowe zaufanie skojarzone z danym wynikiem.

|Wartość wyniku|Znaczenie oceny|Przykładowe zapytanie|
|--|--|--|
|90 – 100|Niemal dokładne dopasowanie zapytania użytkownika i pytania bazy wiedzy|"Moje zmiany nie są aktualizowane w KB po opublikowaniu"|
|> 70|Wysoki poziom pewności — zazwyczaj dobrą odpowiedzią, która całkowicie odpowiada na zapytanie użytkownika|"Opublikowano moją KB, ale nie jest ona aktualizowana"|
|50 – 70|Średni poziom pewności — zwykle dość dobry odpowiedź, która powinna odpowiedzieć na główne intencje kwerendy użytkownika|"Czy warto zapisać moje aktualizacje przed opublikowaniem artykułu KB?"|
|30 - 50|Niski poziom pewności — zwykle powiązana odpowiedź, która częściowo odpowiada na intencję użytkownika|"Do czego służy usługa zapisywania i uczenie?"|
|< 30|Bardzo niski poziom pewności — zazwyczaj nie odpowiada na kwerendę użytkownika, ale zawiera pasujące wyrazy lub frazy |"Gdzie można dodać synonimy do mojej KB"|
|0|Brak dopasowania, więc odpowiedź nie jest zwracana.|"Ile kosztuje usługa"|

## <a name="choose-a-score-threshold"></a>Wybierz próg oceny
W powyższej tabeli przedstawiono wyniki, które są oczekiwane w większości artykułów bazy wiedzy. Jednak ponieważ każda baza wiedzy różni się od siebie i ma różne typy słów, intencje i cele — zalecamy przetestowanie i wybranie progu, który najlepiej działa. Domyślnie próg jest ustawiany na 0, tak aby wszystkie możliwe odpowiedzi są zwracane. Zalecany próg, który powinien być dla większości artykułów bazy wiedzy, wynosi **50**.

Podczas wybierania progu należy wziąć pod uwagę równowagę między dokładnością i pokryciem, a także dostosować wartość progową w zależności od wymagań.

- Jeśli **dokładność** (lub precyzja) jest ważniejsze dla danego scenariusza, zwiększ wartość progową. Dzięki temu za każdym razem, gdy zwracasz odpowiedź, będzie to znacznie trudniejsze rozwiązanie, a znacznie bardziej prawdopodobnie jest to odpowiedź dla użytkowników. W takim przypadku można zakończyć opuszczanie dalszych pytań bez odpowiedzi. *Na przykład:* Jeśli wprowadzisz próg **70**, możesz pominąć niektóre niejednoznaczne przykłady polubimy "co to jest zapisywanie i uczenie?".

- Jeśli **pokrycie** (lub odwołanie) jest ważniejsze i chcesz odpowiedzieć na tyle, na ile to możliwe, nawet jeśli istnieje tylko część relacji z pytaniem użytkownika, Obniż wartość progu. Oznacza to, że może wystąpić więcej przypadków, w których odpowiedź nie odpowiada rzeczywistej kwerendzie użytkownika, ale podaje nieco inną odpowiedź. *Na przykład:* w przypadku progu **30** można udzielić odpowiedzi na zapytania, takie jak "gdzie mogę edytować moją KB?".

> [!NOTE]
> Nowsze wersje QnA Maker obejmują ulepszenia logiki oceniania i mogą wpływać na wartość progową. Za każdym razem, gdy aktualizujesz usługę, pamiętaj o przetestowaniu i dostosowaniu progu, jeśli jest to konieczne. W [tym miejscu](https://www.qnamaker.ai/UserSettings)możesz sprawdzić wersję usługi QNA, a także zapoznać się z artykułem jak uzyskać najnowsze aktualizacje w [tym miejscu](../How-To/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Ustaw próg

Ustaw ocenę wartości progowej jako właściwość [treści JSON interfejsu API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Oznacza to, że ustawisz go dla każdego wywołania do GenerateAnswer.

W środowisku bot należy ustawić wynik jako część obiektu options z [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) lub [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Popraw wyniki pewności
Aby poprawić wynik pewności konkretnej odpowiedzi dla kwerendy użytkownika, można dodać zapytanie użytkownika do bazy wiedzy jako alternatywne pytanie dotyczące tej odpowiedzi. Można również użyć [zmian wyrazów](/rest/api/cognitiveservices/qnamaker/alterations/replace) bez uwzględniania wielkości liter, aby dodać synonimy do słów kluczowych w KB.


## <a name="similar-confidence-scores"></a>Podobne oceny pewności
Gdy wiele odpowiedzi ma podobny wynik zaufania, istnieje prawdopodobieństwo, że zapytanie jest zbyt ogólne i dlatego dopasowane z równym prawdopodobieństwem z wieloma odpowiedziami. Spróbuj zapewnić większą strukturę bazami, tak aby każda jednostka QnA miała odrębny cel.


## <a name="confidence-score-differences-between-test-and-production"></a>Różnice oceny ufności między testem a produkcją
Wynik zaufania odpowiedzi może zmienić się nieznaczny między testem a opublikowaną wersją bazy wiedzy, nawet jeśli zawartość jest taka sama. Wynika to z faktu, że zawartość testu i opublikowanej bazy wiedzy znajdują się w różnych indeksach Wyszukiwanie poznawcze platformy Azure.

Indeks testu zawiera wszystkie pary QnA baz wiedzy. Podczas wykonywania zapytania względem indeksu testowego zapytanie dotyczy całego indeksu, a wyniki są ograniczone do partycji dla tej konkretnej bazy wiedzy. Jeśli wyniki zapytania testowego mają negatywny wpływ na możliwość zweryfikowania bazy wiedzy, możesz:
* Organizuj bazę wiedzy przy użyciu jednego z następujących elementów:
    * 1 zasób ograniczono do 1 KB: Ogranicz pojedynczy zasób QnA (oraz otrzymany indeks testu Wyszukiwanie poznawcze platformy Azure) do jednej bazy wiedzy.
    * 2 zasoby — 1 dla testu, 1 dla produkcji: mają dwa QnA Maker zasoby, przy użyciu jednej do testowania (z własnymi indeksami testowymi i produkcyjnymi) oraz jeden dla produktu (również ma własne indeksy testowe i produkcyjne)
* i, zawsze używaj tych samych parametrów, takich jak **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** , podczas wysyłania zapytań do bazy wiedzy dotyczącej testów i produkcji

Po opublikowaniu bazy wiedzy zawartość pytania i odpowiedzi bazy wiedzy przechodzi z indeksu testu do indeksu produkcyjnego w usłudze Azure Search. Zobacz, jak działa operacja [publikowania](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Jeśli masz bazę wiedzy w różnych regionach, każdy region używa własnego indeksu Wyszukiwanie poznawcze platformy Azure. Ponieważ są używane różne indeksy, wyniki nie będą dokładnie takie same.


## <a name="no-match-found"></a>Nie znaleziono dopasowania
Gdy nie zostanie znalezione żadne dobre dopasowanie przez rangę, zwracana jest Ocena zaufania 0,0 lub "none", a domyślna odpowiedź to "brak dobrego dopasowania znalezionych w KB". Można zastąpić tę [domyślną odpowiedź](../How-To/metadata-generateanswer-usage.md) w bot lub kod aplikacji wywołujący punkt końcowy. Alternatywnie można również ustawić odpowiedź zastąpień na platformie Azure i zmienić wartość domyślną dla wszystkich baz wiedzy wdrożonych w określonej usłudze QnA Maker.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Najlepsze praktyki](./best-practices.md)
