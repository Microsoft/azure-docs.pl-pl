---
title: Ulepszanie modelu dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Szczególne rodzaje transkrypcji i powiązanego tekstu mogą zwiększyć dokładność rozpoznawania dla modelu zamiany mowy na tekst w oparciu o scenariusz mówiący.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727879"
---
# <a name="improve-custom-speech-accuracy"></a>Zwiększanie dokładności usługi Custom Speech

W tym artykule dowiesz się, jak poprawić jakość modelu niestandardowego, dodając dźwięk, transkrypcje z etykietą i powiązanego tekstu.

## <a name="accuracy-in-different-scenarios"></a>Dokładność w różnych scenariuszach

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

- [Szkolenie modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
- [Inspekcja danych](how-to-custom-speech-inspect-data.md)