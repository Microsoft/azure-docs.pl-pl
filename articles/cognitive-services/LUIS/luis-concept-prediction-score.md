---
title: Wyniki przewidywania — LUIS
description: Wynik przewidywania wskazuje stopień zaufania usługi interfejsu API LUIS w celu przewidywania wyników w oparciu o wypowiedź użytkownika.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316464"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Wyniki przewidywania wskazują dokładność przewidywania dla zamiar i jednostek

Wynik przewidywania wskazuje, że stopień zaufania LUIS jest przeznaczony do przewidywania wyników wypowiedź użytkownika.

Wynik przewidywania jest z przedziału od zera (0) do jednego (1). Przykładem wysoce niebezpiecznego wyniku LUIS jest 0,99. Przykładem wyniku o niskiej pewności jest 0,01.

|Wartość wyniku|Ufność|
|--|--|
|1|określone dopasowanie|
|0,99|wysoki poziom pewności|
|0,01|niski poziom pewności|
|0|Niepowodzenie dopasowania|

## <a name="top-scoring-intent"></a>Cel oceny górnej

Każde przewidywanie wypowiedź zwraca zamiar oceny górnej. To prognozowanie to liczbowe porównanie wyników przewidywania.

## <a name="proximity-of-scores-to-each-other"></a>Bliskość wyników do siebie

2 pierwsze wyniki mogą mieć bardzo małą różnicę między nimi. LUIS nie wskazuje tej bliskości innej niż zwraca górny wynik.

## <a name="return-prediction-score-for-all-intents"></a>Zwróć ocenę prognoz dla wszystkich intencji

Wyniki testu lub punktu końcowego mogą obejmować wszystkie intencje. Ta konfiguracja jest ustawiana w punkcie końcowym przy użyciu poprawnej pary nazwa/wartość QueryString.

|Interfejs API przewidywania|Nazwa QueryString|
|--|--|
|Czytanie|`show-all-intents=true`|
|Wersja 2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Zapoznaj się z intencjami podobnymi do wyników

Przegląd oceny dla wszystkich intencji to dobry sposób na sprawdzenie, czy nie tylko jest to poprawna metoda określona, ale że wynik następnego zidentyfikowanego celu jest znacząco i ciągle niższy dla wyrażenia długości.

Jeśli wiele intencji ma zamknięte wyniki prognozowania na podstawie kontekstu wypowiedź, LUIS może przełączać się między intencjami. Aby rozwiązać ten problem, Kontynuuj dodawanie wyrażenia długości do każdego zamiaru z większą różnorodnością różnic kontekstowych lub aplikację kliencką, taką jak rozmowa bot, wybierz programowe opcje dotyczące obsługi 2 najważniejszych założeń.

Jeśli 2 intencje mają zbyt zbliżone wyniki, ich kolejność może zostać odwrócona z powodu **trenowania niedeterministycznego**. Górny wynik może stać się drugim u góry, a drugi wynik może stać się pierwszym górnym wynikiem. Aby uniknąć tej sytuacji, należy dodać przykład wyrażenia długości do każdego z dwóch najlepszych zamiar dla tego wypowiedź z wyborem słowa i kontekstem, który odróżnia 2 intencje. Dwa intencje powinny mieć taką samą liczbę przykładowych wyrażenia długości. Reguła kciuka do oddzielenia, aby zapobiec występowaniu z poziomu szkolenia, to 15% różnica w ocenie.

Możesz wyłączyć **niedeterministyczne szkolenie** , [przekształcenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Różnice związane z przewidywaniami między różnymi sesjami szkoleniowymi

W przypadku uczenia tego samego modelu w innej aplikacji, a wyniki nie są takie same, różnica wynika z faktu, że jest to **niedeterministyczne szkolenie** (element losowości). Po drugie, każdy z zamiaru wypowiedź do więcej niż jednego celu oznacza, że najwyższy zamiar dla tego samego wypowiedź może ulec zmianie w zależności od szkoleń.

Jeśli rozmowa bot wymaga określonego wyniku LUIS, aby wskazać wiarygodność, należy użyć różnicy wynikowej między dwoma najczęściej używanymi intencjami. Ta sytuacja zapewnia elastyczność w zakresie różnic w szkoleniu.

Możesz wyłączyć **niedeterministyczne szkolenie** , [przekształcenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notacja E (wykładnik)

Wyniki przewidywania mogą korzystać z notacji wykładnika, która jest _wyświetlana_ powyżej zakresu 0-1, na przykład `9.910309E-07` . Ten wynik wskazuje bardzo **małą** liczbę.

|Wynik w notacji E |Rzeczywisty wynik|
|--|--|
|9.910309 e-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Ustawienia aplikacji

Użyj [ustawień aplikacji](luis-reference-application-settings.md) , aby kontrolować sposób oceny efektów diakrytycznych i interpunkcji.

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.
