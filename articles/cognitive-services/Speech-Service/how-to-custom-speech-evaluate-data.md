---
title: Oceń dokładność dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak ilościowo mierzyć jakość modelu zamiany mowy na tekst lub modelu niestandardowego. Wymagane jest przetestowanie dokładności przez audio i dane transkrypcji z etykietami ludzkimi, które powinny być podane przez 30 minut.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cadbe79bbe0af2b5cebacb3d0c7c4e910fc7dbb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85856835"
---
# <a name="evaluate-custom-speech-accuracy"></a>Ocena dokładności usługi Custom Speech

W tym dokumencie dowiesz się, jak ilościowo mierzyć jakość modelu zamiany mowy na tekst firmy Microsoft lub modelu niestandardowego. Wymagane jest przetestowanie dokładności przez audio i dane transkrypcji z etykietami ludzkimi, które powinny być podane przez 30 minut.

## <a name="what-is-word-error-rate-wer"></a>Co to jest współczynnik błędów programu Word?

Standardem branżowym do mierzenia dokładności modelu jest *Współczynnik błędów programu Word* . Funkcja Raportowanie błędów systemu Windows zlicza błędne słowa identyfikowane podczas rozpoznawania, a następnie dzieli przez łączną liczbę wyrazów w transkrypcji z etykietami ludzkimi (pokazane poniżej jako N). Na koniec ten numer jest mnożony przez 100%, aby obliczyć raportowanie błędów.

![Formuła raportowanie błędów](./media/custom-speech/custom-speech-wer-formula.png)

Niepoprawnie zidentyfikowane słowa dzielą się na trzy kategorie:

* Wstawianie (I): wyrazy, które są niepoprawnie dodane w transkrypcji hipotezy
* Usuwanie (D): wyrazy, które nie są wykrywane w transkrypcji hipotezy
* Substytucje: słowa, które zostały zastąpione przez odwołanie i hipotezę

Oto przykład:

![Przykład niepoprawnie zidentyfikowanych wyrazów](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Rozwiązywanie problemów i ulepszanie funkcji Raportowanie błędów systemu Windows

Można użyć funkcji Raportowanie błędów z wyników rozpoznawania maszyny do oceny jakości modelu używanego w aplikacji, narzędziu lub produkcie. Raportowanie błędów systemu Windows o wartości 5%-10% jest uznawane za dobrą jakość i jest gotowe do użycia. Wartość raportowanie błędów systemu Windows jest akceptowalna, ale warto rozważyć dodatkowe szkolenie. Raportowanie błędów systemu Windows o 30% lub więcej sygnalizuje niską jakość i wymaga dostosowania i szkolenia.

Sposób dystrybucji błędów jest ważny. W przypadku napotkania wielu błędów usunięcia zazwyczaj wynika to z słabej siły sygnału audio. Aby rozwiązać ten problem, należy zebrać dane audio bliżej źródła. Błędy wstawiania oznaczają, że dźwięk został zarejestrowany w środowisku z zakłóceniami, a Crosstalk może być obecny, powodując problemy z rozpoznawaniem. Błędy podstawiania są często spotykane, gdy niewystarczająca próba dotycząca warunków specyficznych dla domeny została podana jako transkrypcja lub tekst pokrewny.

Analizując poszczególne pliki, można określić, jakiego typu błędy istnieją i które błędy są unikatowe dla określonego pliku. Zrozumienie problemów na poziomie pliku pomoże Ci usprawnić ulepszenia.

## <a name="create-a-test"></a>Tworzenie testu

Jeśli chcesz przetestować jakość modelu linii bazowej zamiany mowy na tekst firmy Microsoft lub modelu niestandardowego, który został przeszkolony, możesz porównać dwa modele obok siebie, aby oszacować dokładność. Porównanie zawiera wyniki raportowania błędów i oceny. Zwykle model niestandardowy jest porównywany z modelem bazowym firmy Microsoft.

Aby oszacować modele obok siebie:

1. Zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech).
2. Przejdź do **> zamiany mowy na tekst Custom Speech > [nazwa projektu] > testowanie**.
3. Kliknij przycisk **Dodaj test**.
4. Wybierz pozycję **Oceń dokładność**. Nadaj testowi nazwę, opis, a następnie wybierz swój audio + ludzki zestaw danych transkrypcji.
5. Wybierz maksymalnie dwa modele, które chcesz przetestować.
6. Kliknij przycisk **Utwórz**.

Po pomyślnym utworzeniu testu można porównać wyniki obok siebie.

## <a name="side-by-side-comparison"></a>Porównanie równoczesne

Po zakończeniu testu, wskazywanym przez zmianę stanu na *powodzenie*, można znaleźć numer funkcji Raportowanie błędów dla obu modeli uwzględnionych w teście. Kliknij nazwę testu, aby wyświetlić stronę szczegółów testowania. Ta strona szczegółów zawiera listę wszystkich wyrażenia długości w zestawie danych, wskazując wyniki rozpoznawania dwóch modeli obok transkrypcji z przesłanego zestawu danych. Aby pomóc w sprawdzeniu porównania obok siebie, można przełączać różne typy błędów, w tym Wstawianie, usuwanie i podstawianie. Nasłuchiwanie audio i porównywanie wyników rozpoznawania w każdej kolumnie, która pokazuje transkrypcję z etykietami ludzkimi i wyniki dla dwóch modeli zamiany mowy na tekst, można zdecydować, który model spełnia Twoje potrzeby i gdzie są wymagane dodatkowe szkolenia i ulepszenia.

## <a name="next-steps"></a>Następne kroki

* [Szkolenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
