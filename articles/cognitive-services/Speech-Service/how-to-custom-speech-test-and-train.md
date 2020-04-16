---
title: Przygotowywanie danych do mowy niestandardowej — usługa mowy
titleSuffix: Azure Cognitive Services
description: Podczas testowania dokładności rozpoznawania mowy firmy Microsoft lub szkolenia modeli niestandardowych, trzeba dane audio i tekstowe. Na tej stronie omówimy typy danych, jak z nich korzystać i nimi zarządzać.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 78857709447f99895c36f23d8760f44f8468ba7c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402144"
---
# <a name="prepare-data-for-custom-speech"></a>Przygotowywanie danych dla usługi Custom Speech

Podczas testowania dokładności rozpoznawania mowy firmy Microsoft lub szkolenia modeli niestandardowych, trzeba dane audio i tekstowe. Na tej stronie omówimy typy danych, jak z nich korzystać i nimi zarządzać.

## <a name="data-types"></a>Typy danych

Ta tabela zawiera listę akceptowanych typów danych, kiedy każdy typ danych powinien być używany, oraz zalecaną ilość. Nie każdy typ danych jest wymagany do utworzenia modelu. Wymagania dotyczące danych będą się różnić w zależności od tego, czy tworzysz test, czy trenujesz model.

| Typ danych | Używany do testowania | Zalecana ilość | Używany do szkolenia | Zalecana ilość |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Tak<br>Używany do kontroli wzroku | Ponad 5 plików audio | Nie | N/a |
| [Transkrypcje audio + oznaczone przez człowieka](#audio--human-labeled-transcript-data-for-testingtraining) | Tak<br>Służy do oceny dokładności | 0,5-5 godzin dźwięku | Tak | 1-1000 godzin dźwięku |
| [Tekst pokrewny](#related-text-data-for-training) | Nie | N/a | Tak | 1-200 MB powiązanego tekstu |

Pliki powinny być pogrupowane według typu do zestawu danych i przekazywane jako plik zip. Każdy zestaw danych może zawierać tylko jeden typ danych.

> [!TIP]
> Aby szybko rozpocząć, należy rozważyć użycie przykładowych danych. Zobacz to repozytorium GitHub, <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">aby uzyskać przykładowe dane <span class="docon docon-navigate-external x-hidden-focus"></span> mowy niestandardowej</a>

## <a name="upload-data"></a>Przekazywanie danych

Aby przekazać dane, przejdź do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"> </span>Mowy niestandardowej </a>. W portalu kliknij pozycję **Przekaż dane,** aby uruchomić kreatora i utworzyć pierwszy zestaw danych. Przed przesłaniem danych zostaniesz poproszony o wybranie typu danych mowy dla zestawu danych.

![Wybieranie dźwięku z portalu mowy](./media/custom-speech/custom-speech-select-audio.png)

Każdy przesłany zestaw danych musi spełniać wymagania dotyczące wybranego typu danych. Dane muszą być poprawnie sformatowane przed ich przesłaniem. Poprawnie sformatowane dane zapewniają, że będą dokładnie przetwarzane przez usługę Mowy niestandardowej. Wymagania są wymienione w poniższych sekcjach.

Po przesłaniu zestawu danych masz kilka opcji:

* Możesz przejść do karty **Testowanie** i wizualnie sprawdzić tylko dźwięk lub audio + dane transkrypcji oznaczone przez człowieka.
* Można przejść do **szkolenia** kartę i użyć audio + dane transkrypcji człowieka lub powiązanych danych tekstowych do szkolenia modelu niestandardowego.

## <a name="audio-data-for-testing"></a>Dane audio do testowania

Dane audio są optymalne do testowania dokładności bazowego modelu mowy do tekstu firmy Microsoft lub modelu niestandardowego. Należy pamiętać, że dane audio są używane do sprawdzania dokładności mowy w odniesieniu do wydajności określonego modelu. Jeśli chcesz określić dokładność modelu, użyj danych [transkrypcyjnych oznaczonych etykietą audio + człowieka.](#audio--human-labeled-transcript-data-for-testingtraining)

Ta tabela umożliwia poprawne sformatowanie plików audio do użytku z mowę niestandardową:

| Właściwość                 | Wartość                 |
|--------------------------|-----------------------|
| Format pliku              | RIFF (WAV)            |
| Częstotliwość próbkowania              | 8000 Hz lub 16 000 Hz |
| Kanały                 | 1 (mono)              |
| Maksymalna długość dźwięku | 2 godziny               |
| Przykładowy format            | PCM, 16-bitowy           |
| Format archiwum           | zip                  |
| Maksymalny rozmiar archiwum     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Podczas przekazywania danych szkoleniowych i testowych rozmiar pliku zip nie może przekraczać 2 GB. Jeśli potrzebujesz więcej danych do szkolenia, podziel je na kilka plików .zip i przekaż je oddzielnie. Później można wybrać do pociągu z *wielu* zestawów danych. Można jednak testować tylko z *jednego* zestawu danych.

Użyj <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> aby zweryfikować właściwości dźwięku lub przekonwertować istniejący dźwięk na odpowiednie formaty. Poniżej znajduje się kilka przykładów, jak każdy z tych działań można wykonać za pośrednictwem wiersza polecenia SoX:

| Działanie | Opis | Polecenie SoX |
|----------|-------------|-------------|
| Sprawdź format dźwięku | Użyj tego polecenia, aby sprawdzić<br>formatu pliku audio. | `sox --i <filename>` |
| Konwertowanie formatu audio | Użyj tego polecenia, aby przekonwertować<br>jednokanałowy, 16-bitowy, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + dane transkrypcji oznaczone przez człowieka do testowania/szkolenia

Aby zmierzyć dokładność dokładności mowy do tekstu firmy Microsoft podczas przetwarzania plików audio, należy podać transkrypcje oznaczone przez człowieka (słowo po tekście) do porównania. Podczas gdy transkrypcja oznaczona przez człowieka jest często czasochłonna, konieczne jest oszacowanie dokładności i przeszkolenie modelu dla przypadków użycia. Należy pamiętać, że ulepszenia w rozpoznawaniu będą tylko tak dobre, jak dostarczone dane. Z tego powodu ważne jest, aby przesyłano tylko wysokiej jakości transkrypcje.

| Właściwość                 | Wartość                               |
|--------------------------|-------------------------------------|
| Format pliku              | RIFF (WAV)                          |
| Częstotliwość próbkowania              | 8000 Hz lub 16 000 Hz               |
| Kanały                 | 1 (mono)                            |
| Maksymalna długość dźwięku | 2 godziny (testowanie) / 60 s (szkolenie) |
| Przykładowy format            | PCM, 16-bitowy                         |
| Format archiwum           | zip                                |
| Maksymalny rozmiar suwaka         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Podczas przekazywania danych szkoleniowych i testowych rozmiar pliku zip nie może przekraczać 2 GB. Można przetestować tylko z *jednego* zestawu danych, należy zachować go w odpowiednim rozmiarze pliku. Ponadto każdy plik szkolenia nie może przekraczać 60 sekund, w przeciwnym razie będzie błąd.

Aby rozwiązać problemy, takie jak usunięcie lub zastąpienie wyrazów, znaczna ilość danych jest wymagana w celu poprawy rozpoznawania. Ogólnie rzecz biorąc zaleca się dostarczanie transkrypcji słowo po słowach dla około 10 do 1000 godzin dźwięku. Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

  Przykład:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Transkrypcja powinna być kodowana za pomocą kodowania UTF-8 ze znacznikiem kolejności bajtów (BOM).

Transkrypcje są normalizowane pod względem tekstu, aby mogły być przetwarzane przez system. Istnieje jednak kilka ważnych normalizacji, które należy wykonać przed przesłaniem danych do studia mowy. Aby uzyskać odpowiedni język używany podczas przygotowywania transkrypcji, zobacz [Jak utworzyć transkrypcję oznaczona przez człowieka](how-to-custom-speech-human-labeled-transcriptions.md)

Po zebraniu plików audio i odpowiednich transkrypcjach należy spakować je jako pojedynczy plik zip przed przesłaniem do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"> </span>Mowy niestandardowej </a>. Poniżej znajduje się przykładowy zestaw danych z trzema plikami audio i plikiem transkrypcji oznaczonym przez człowieka:

> [!div class="mx-imgBorder"]
> ![Wybieranie dźwięku z portalu mowy](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Powiązane dane tekstowe do szkoleń

Nazwy produktów lub funkcje, które są unikatowe, powinny zawierać powiązane dane tekstowe do szkolenia. Powiązany tekst pomaga zapewnić prawidłowe rozpoznawanie. W celu poprawy rozpoznawania można podać dwa rodzaje powiązanych danych tekstowych:

| Typ danych | W jaki sposób dane te poprawiają rozpoznawanie |
|-----------|------------------------------------|
| Zdania (wypowiedzi) | Zwiększ dokładność podczas rozpoznawania nazw produktów lub słownictwa specyficznego dla branży w kontekście zdania. |
| Wymowy | Popraw wymowę nietypowych terminów, akronimów lub innych słów za pomocą niezdefiniowanych wymow. |

Zdania mogą być dostarczane jako pojedynczy plik tekstowy lub wiele plików tekstowych. Aby zwiększyć dokładność, należy użyć danych tekstowych, który jest bliżej oczekiwanych wypowiedzi mówionych. Wymowa powinna być dostarczana jako pojedynczy plik tekstowy. Wszystko może być pakowane jako pojedynczy plik zip i przesyłane do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"> </span>Mowy niestandardowej </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Wskazówki dotyczące tworzenia pliku zdań

Aby utworzyć model niestandardowy przy użyciu zdań, należy podać listę wypowiedzi przykładowych. Wypowiedzi _nie_ muszą być kompletne lub poprawne gramatycznie, ale muszą dokładnie odzwierciedlać wypowiedzi danych wejściowych, których oczekujesz w produkcji. Jeśli chcesz, aby niektóre terminy miały większą wagę, dodaj kilka zdań, które zawierają te konkretne terminy.

Jako ogólne wskazówki, dostosowanie modelu jest najbardziej skuteczne, gdy tekst szkolenia jest jak najbardziej zbliżony do rzeczywistego tekstu oczekiwanego w produkcji. Żargon i wyrażenia specyficzne dla domeny, które chcesz poprawić, powinny zostać uwzględnione w tekście szkolenia. Jeśli to możliwe, spróbuj kontrolować jedno zdanie lub słowo kluczowe w osobnym wierszu. W przypadku słów kluczowych i fraz, które są dla Ciebie ważne (na przykład nazwy produktów), możesz je skopiować kilka razy. Ale pamiętaj, nie kopiuj zbyt wiele - może to wpłynąć na ogólny wskaźnik rozpoznawania.

Ta tabela służy do upewnienia się, że powiązany plik danych dla wypowiedzi jest poprawnie sformatowany:

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM |
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalna wielkość pliku | 200 MB |

Ponadto należy uwzględnić następujące ograniczenia:

* Unikaj powtarzania znaków więcej niż cztery razy. Na przykład: "aaaa" lub "uuuu".
* Nie używaj znaków specjalnych ani znaków UTF-8 powyżej `U+00A1`.
* Identyfikatory URI zostaną odrzucone.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Wskazówki dotyczące tworzenia pliku wymowy

Jeśli istnieją nietypowe terminy bez standardowych wymow, które użytkownicy napotkają lub używają, można podać niestandardowy plik wymowy, aby poprawić rozpoznawanie.

> [!IMPORTANT]
> Nie zaleca się używania niestandardowych plików wymowy w celu zmiany wymowy typowych słów.

Obejmuje to przykłady wypowiedzi mówionej i niestandardowej wymowy dla każdego:

| Rozpoznany/wyświetlony formularz | Forma mówiona |
|--------------|--------------------------|
| 3CPO (właśc. | trzy c p o |
| CNTK | c n t k |
| Ieee | i potrójny e |

Forma mówiona jest sekwencją fonetyczną przeliterowane. Może składać się z litery, słów, sylab lub kombinacji wszystkich trzech.

Dostosowana wymowa jest dostępna`en-US`w języku`de-DE`angielskim ( ) i niemieckim ( ). W tej tabeli przedstawiono obsługiwane znaki według języka:

| Język | Ustawienia regionalne | Znaki |
|----------|--------|------------|
| Polski | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Niemiecki | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Poniższa tabela umożliwia upewnienie się, że powiązany plik danych dla wymowy jest poprawnie sformatowany. Pliki wymowy są małe i powinny mieć tylko kilka kilobajtów.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM (ANSI jest również obsługiwany w języku angielskim) |
| Liczba wymow na linię | 1 |
| Maksymalna wielkość pliku | 1 MB (1 KB dla warstwy bezpłatnej) |

## <a name="next-steps"></a>Następne kroki

* [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
