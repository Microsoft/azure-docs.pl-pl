---
title: Oceń i popraw Custom Speech dokładność — usługa rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak ilościowo mierzyć i poprawiać jakość modelu zamiany mowy na tekst lub modelu niestandardowego. Wymagane jest przetestowanie dokładności przez audio i dane transkrypcji z etykietami ludzkimi, które powinny być podane przez 30 minut.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: b8b3a0aa6d9790dbb5900eac2d79074f44a749d2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025654"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Ocenianie i poprawianie dokładności usługi Custom Speech

W tym artykule dowiesz się, jak ilościowo mierzyć i poprawiać dokładność modeli zamiany mowy na tekst firmy Microsoft lub własnych modeli niestandardowych. Wymagane jest przetestowanie dokładności przez audio i dane transkrypcji z etykietami ludzkimi, które powinny być podane przez 30 minut.

## <a name="evaluate-custom-speech-accuracy"></a>Ocena dokładności usługi Custom Speech

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
6. Kliknij pozycję **Utwórz**.

Po pomyślnym utworzeniu testu można porównać wyniki obok siebie.

### <a name="side-by-side-comparison"></a>Porównanie równoczesne

Po zakończeniu testu, wskazywanym przez zmianę stanu na *powodzenie*, można znaleźć numer funkcji Raportowanie błędów dla obu modeli uwzględnionych w teście. Kliknij nazwę testu, aby wyświetlić stronę szczegółów testowania. Ta strona szczegółów zawiera listę wszystkich wyrażenia długości w zestawie danych, wskazując wyniki rozpoznawania dwóch modeli obok transkrypcji z przesłanego zestawu danych. Aby pomóc w sprawdzeniu porównania obok siebie, można przełączać różne typy błędów, w tym Wstawianie, usuwanie i podstawianie. Nasłuchiwanie audio i porównywanie wyników rozpoznawania w każdej kolumnie, która pokazuje transkrypcję z etykietami ludzkimi i wyniki dla dwóch modeli zamiany mowy na tekst, można zdecydować, który model spełnia Twoje potrzeby i gdzie są wymagane dodatkowe szkolenia i ulepszenia.

## <a name="improve-custom-speech-accuracy"></a>Zwiększanie dokładności usługi Custom Speech

Scenariusze rozpoznawania mowy różnią się w zależności od jakości i języka audio (stylu słownictwa i mowy). W poniższej tabeli przedstawiono cztery typowe scenariusze:

| Scenariusz | Jakość dźwięku | Słownika | Styl mówiący |
|----------|---------------|------------|----------------|
| Centrum telefoniczne | Niski, 8 kHz, może być 2 ludzi na 1 kanale audio, może być skompresowany | Wąskie, unikatowe dla domeny i produktów | Konwersacja, ze luźną strukturą |
| Asystent głosowy (na przykład Cortana lub okno z napędem) | Wysoki, 16 kHz | Jednostka ciężki (tytuły utworów, produkty, lokalizacje) | Jasno określone wyrazy i frazy |
| Dyktowanie (wiadomość błyskawiczna, notatki, wyszukiwanie) | Wysoki, 16 kHz | Różnych | Tworzenie notatek |
| Podpisy kodowane wideo | Zróżnicowane, w tym różne zastosowania mikrofonu, dodaliśmy muzykę | Różne, od spotkań, odcytowanych mowy, tekstów muzycznych | Odczytuj, przygotowane lub luźno uporządkowane |

Różne scenariusze tworzą różne wyniki dotyczące jakości. W poniższej tabeli przedstawiono sposób, w jaki zawartość z tych czterech kursów jest naliczana w ramach [współczynnika błędów wyrazów](how-to-custom-speech-evaluate-data.md). W tabeli pokazano, które typy błędów są najczęściej używane w każdym scenariuszu.

| Scenariusz | Jakość rozpoznawania mowy | Błędy wstawiania | Błędy usuwania | Błędy podstawiania |
|----------|----------------------------|------------------|-----------------|---------------------|
| Centrum telefoniczne | Średni (< 30% WER) | Niska, z wyjątkiem sytuacji, w której inne osoby mówią w tle | Może być wysoka. Centra połączeń mogą być zakłóceniami, a nakładające się głośniki mogą mylić model | Średnia. Produkty i nazwiska osób mogą spowodować te błędy |
| Asystent głosowy | Wysoka (może być < 10% raportowanie błędów systemu Windows) | Niski | Niski | Średnia ze względu na tytuły utworów, nazwy produktów lub lokalizacje |
| Dyktowanie | Wysoka (może być < 10% raportowanie błędów systemu Windows) | Niski | Niski | Wys. |
| Podpisy kodowane wideo | Zależy od typu wideo (może być < 50% raportowanie błędów systemu Windows) | Niski | Może być wysokie ze względu na muzykę, szumy, jakość mikrofonu | Żargon mogą spowodować te błędy |

Określenie składników funkcji Raportowanie błędów systemu Windows (liczba błędów wstawienia, usunięcia i podstawiania) ułatwia określenie rodzaju danych do dodania w celu usprawnienia modelu. Użyj [portalu Custom Speech](https://speech.microsoft.com/customspeech) , aby wyświetlić jakość modelu linii bazowej. W portalu są raportowane stawki błędów dotyczące wstawiania, podstawiania i usuwania, które są łączone w wskaźniku jakości raportowanie błędów systemu Windows.

## <a name="improve-model-recognition"></a>Ulepszanie rozpoznawania modelu

Błędy rozpoznawania można zmniejszyć, dodając dane szkoleniowe w [portalu Custom Speech](https://speech.microsoft.com/customspeech). 

Zaplanuj obsługę modelu niestandardowego, dodając materiały źródłowe okresowo. Niestandardowy model wymaga dodatkowego szkolenia, aby zachować świadomość zmian w jednostkach. Na przykład może być konieczne zaktualizowanie nazw produktów, nazw utworów lub nowych lokalizacji usług.

W poniższych sekcjach opisano, jak każdy rodzaj dodatkowych danych szkoleniowych może zmniejszyć liczbę błędów.

### <a name="add-related-text-sentences"></a>Dodaj powiązane zdania tekstowe

Dodatkowe powiązane zdania tekstowe mogą przede wszystkim ograniczyć błędy podstawiania związane z błędami rozpoznawania typowych słów i słów specyficznych dla domeny, pokazując je w kontekście. Słowa specyficzne dla domeny mogą być nietypowe lub wykonane słowa, ale ich wymowa musi być prosta do rozpoznania.

> [!NOTE]
> Unikaj powiązanych zdań tekstowych, które zawierają szum, takie jak nierozpoznawalne znaki lub wyrazy.

### <a name="add-audio-with-human-labeled-transcripts"></a>Dodaj dźwięk z transkrypcjami z etykietami ludzkimi

Dźwięk z transkrypcjami z etykietami ludzkimi oferuje większość ulepszeń, jeśli dźwięk pochodzi z docelowego przypadku użycia. Przykłady muszą obejmować pełen zakres mowy. Na przykład centrum połączeń dla sklepu detalicznego może uzyskać większość wywołań dotyczących Swimwear i przeciwglasseń w ciągu roku letniego. Zapewnij, że przykład obejmuje pełen zakres mowy, który chcesz wykryć.

Należy wziąć pod uwagę następujące szczegóły:

* Custom Speech może przechwycić tylko kontekst słowa, aby zmniejszyć liczbę błędów podstawiania, bez błędów wstawiania ani usuwania.
* Unikaj przykładów zawierających błędy transkrypcji, ale Uwzględnij różnorodność jakości audio.
* Unikaj zdań, które nie są związane z Twoją domeną problemu. Niepowiązane zdania mogą uszkodzić model.
* Gdy jakość transkrypcji jest różna, można zduplikować wyjątkowo dobre zdania (na przykład doskonałe transkrypcje zawierające kluczowe frazy) w celu zwiększenia ich wagi.

### <a name="add-new-words-with-pronunciation"></a>Dodawanie nowych wyrazów z wymowęm

Wyrazy, które są tworzone lub wysoce wyspecjalizowane mogą mieć unikatowe wymowy. Te wyrazy można rozpoznać, jeśli wyraz może zostać podzielony na mniejsze słowa, aby go wyróżnić. Na przykład, aby rozpoznać **konsolę Xbox**, Wymawiaj jako **pole X**. Takie podejście nie spowoduje zwiększenia ogólnej dokładności, ale może zwiększyć rozpoznawanie tych słów kluczowych.

> [!NOTE]
> Ta technika jest dostępna tylko w niektórych językach. Aby uzyskać szczegółowe informacje, zobacz Dostosowywanie wymowy w [tabeli zamiany mowy na tekst](language-support.md) .

## <a name="sources-by-scenario"></a>Źródła według scenariusza

W poniższej tabeli przedstawiono scenariusze rozpoznawania głosu i wymieniono materiały źródłowe, które należy wziąć pod uwagę w ramach trzech kategorii zawartości szkoleniowej wymienionych powyżej.

| Scenariusz | Powiązane zdania tekstowe | Zapisy audio + oznakowane przez człowieka | Nowe wyrazy z wymowęm |
|----------|------------------------|------------------------------|------------------------------|
| Centrum telefoniczne             | dokumenty marketingowe, witryna sieci Web, przeglądy produktów powiązane z działaniem centrum połączeń | wywołania centrum połączeń uzyskanego przez ludzi | warunki, które mają niejednoznaczne wymowy (zobacz konsolę Xbox powyżej) |
| Asystent głosowy         | wystaw zdania przy użyciu wszystkich kombinacji poleceń i jednostek | Rejestruj głosy głosujące na urządzeniu i transkrypcja na tekst | nazwy (filmy, utwory muzyczne, produkty), które mają unikatowe wymowy |
| Dyktowanie               | zapisywane dane wejściowe, takie jak wiadomości błyskawiczne lub wiadomości e-mail | podobne do powyżej | podobne do powyżej |
| Podpisy kodowane wideo | Skrypty pokazu TV, filmy, zawartość marketingowa, podsumowania wideo | dokładne transkrypcje filmów wideo | podobne do powyżej |

## <a name="next-steps"></a>Następne kroki

* [Trenowanie i wdrażanie modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotowywanie i testowanie danych](./how-to-custom-speech-test-and-train.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)