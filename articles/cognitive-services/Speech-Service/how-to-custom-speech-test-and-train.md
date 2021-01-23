---
title: Przygotowywanie danych dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Podczas testowania dokładności rozpoznawania mowy firmy Microsoft lub uczenia modeli niestandardowych będziesz potrzebować danych audio i tekstowych. Na tej stronie omówiono typy danych, sposób ich używania i zarządzania nimi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 605bae706bbc1db2e008b8d050cbba9eacd16933
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702206"
---
# <a name="prepare-data-for-custom-speech"></a>Przygotowywanie danych dla usługi Custom Speech

Podczas testowania dokładności rozpoznawania mowy firmy Microsoft lub uczenia modeli niestandardowych będziesz potrzebować danych audio i tekstowych. Na tej stronie omówiono typy danych, których potrzebuje niestandardowy model mowy.

## <a name="data-diversity"></a>Różnorodność danych

Tekst i dźwięk używany do testowania i uczenia modelu niestandardowego muszą zawierać przykłady z różnorodnych głośników i scenariuszy, które są potrzebne do rozpoznania modelu.
Te czynniki należy wziąć pod uwagę podczas zbierania danych dotyczących niestandardowego testowania i szkolenia modelu:

* Dane audio i mowy muszą obejmować rodzaje wyrażeń werbalnych, które użytkownicy będą wprowadzać podczas współpracy z modelem. Na przykład model, który podnosi i obniża wymagania dotyczące temperatury w przypadku oświadczeń, osoby mogą zażądać takich zmian.
* Dane muszą obejmować wszystkie wariancje mowy, które Twój model będzie musiał rozpoznać. Wiele czynników może zmieniać mowę, w tym akcenty, dialekty, mieszanie języka, wiek, płeć, gęstość głosu, poziom obciążeniowy i godzinę.
* Należy uwzględnić przykłady z różnych środowisk (wewnętrznych, na zewnątrz, szumów drogowych), w których będzie używany model.
* Audio należy zebrać przy użyciu urządzeń sprzętowych, które będą używane przez system produkcyjny. Jeśli model wymaga zidentyfikowania mowy zarejestrowanej na urządzeniach nagrywających o różnej jakości, dane audio podane do uczenia modelu muszą również przedstawiać te różnorodne scenariusze.
* Później możesz dodać do modelu więcej danych, ale Weź pod uwagę, że zestaw danych powinien być zróżnicowany i reprezentatywny dla projektu.
* Uwzględnienie danych, które *nie* są zawarte w niestandardowych wymaganiach dotyczących rozpoznawania modelu, może być nieszkodliwe dla jakości rozpoznawania, dlatego nie należy uwzględniać danych, które nie muszą być transkrypcja przez model.

Model przeszkolony na podzbiorze scenariuszy może działać tylko w tych scenariuszach. Starannie wybieraj dane, które reprezentują pełen zakres scenariuszy, które są potrzebne do rozpoznania modelu niestandardowego.

> [!TIP]
> Zacznij od małych zestawów przykładowych danych, które są zgodne z językiem i dźwiękiem, który zostanie napotkany przez model.
> Na przykład Zanotuj mały, ale reprezentatywny przykład audio na tym samym sprzęcie i w tym samym środowisku akustycznym, w którym model znajdzie się w scenariuszach produkcyjnych.
> Małe zbiory danych reprezentatywne dane mogą uwidaczniać problemy, zanim zainwestowano w gromadzenie znacznie większych zestawów danych do szkoleń.

## <a name="data-types"></a>Typy danych

Ta tabela zawiera listę akceptowanych typów danych, gdy należy użyć poszczególnych typów danych i zalecanej ilości. Do utworzenia modelu nie jest wymagany każdy typ danych. Wymagania dotyczące danych będą się różnić w zależności od tego, czy tworzysz test czy uczenie modelu.

| Typ danych | Używany do testowania | Zalecana ilość | Używany do szkolenia | Zalecana ilość |
|-----------|-----------------|----------|-------------------|----------|
| [Dźwięk](#audio-data-for-testing) | Tak<br>Używane na potrzeby inspekcji wizualnej | 5 plików audio | Nie | Nie dotyczy |
| [Zapisy audio + oznakowane przez człowieka](#audio--human-labeled-transcript-data-for-testingtraining) | Tak<br>Służy do obliczania dokładności | 0,5 – 5 godzin audio | Tak | 1-20 godz. audio |
| [Powiązany tekst](#related-text-data-for-training) | Nie | Nie dotyczy | Tak | 1-200 MB powiązanego tekstu |

Podczas uczenia nowego modelu Zacznij od [pokrewnego tekstu](#related-text-data-for-training). Te dane będą już ulepszać rozpoznawanie specjalnych terminów i fraz. Szkolenia z tekstem są znacznie szybsze niż szkolenia z użyciem dźwięku (minuty a dni).

Pliki powinny być pogrupowane według typu w zestawie danych i przekazywane jako plik. zip. Każdy zestaw danych może zawierać tylko jeden typ danych.

> [!TIP]
> Aby szybko rozpocząć pracę, rozważ użycie przykładowych danych. Zapoznaj się z tym repozytorium GitHub, aby uzyskać <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">przykładowe dane <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech</a>

## <a name="upload-data"></a>Przekazywanie danych

Aby przekazać dane, przejdź do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech </a>. W portalu kliknij pozycję **Przekaż dane** , aby uruchomić kreatora i utworzyć swój pierwszy zestaw danych. Przed umożliwieniem przekazania danych użytkownik zostanie poproszony o wybranie typu danych mowy dla zestawu danych.

![Zrzut ekranu, który podświetla opcję przekazywania audio z portalu mowy.](./media/custom-speech/custom-speech-select-audio.png)

Każdy przekazywany zestaw danych musi spełniać wymagania dotyczące wybranego typu danych. Dane muszą być poprawnie sformatowane przed ich przekazaniem. Prawidłowo sformatowane dane zapewniają, że będzie ono prawidłowo przetwarzane przez usługę Custom Speech. Wymagania są wymienione w poniższych sekcjach.

Po przekazaniu zestawu danych można korzystać z kilku opcji:

* Możesz przejść do karty **testowanie** i wizualnie sprawdzić dane dotyczące transkrypcji audio lub audio oraz ludzkich etykiet.
* Możesz przejść do karty **uczenie** i użyć danych audio + ludzkiego transkrypcji lub powiązanych danych tekstowych do uczenia modelu niestandardowego.

## <a name="audio-data-for-testing"></a>Dane audio do testowania

Dane audio są optymalne do testowania dokładności linii bazowej "zamiany mowy na tekst" firmy Microsoft lub modelu niestandardowego. Należy pamiętać, że dane audio są używane do sprawdzenia dokładności mowy w odniesieniu do wydajności określonego modelu. Jeśli chcesz określić liczbę dokładności dla modelu, użyj [audio + oznakowane dane transkrypcji](#audio--human-labeled-transcript-data-for-testingtraining).

Użyj tej tabeli, aby upewnić się, że pliki audio są poprawnie sformatowane do użytku z Custom Speech:

| Właściwość                 | Wartość                 |
|--------------------------|-----------------------|
| Format pliku              | RIFF (WAV)            |
| Częstotliwość próbkowania              | 8 000 Hz lub 16 000 Hz |
| Kanały                 | 1 (mono)              |
| Maksymalna długość na dźwięk | 2 godziny               |
| Format próbki            | PCM, 16-bitowe           |
| Format archiwum           | zip                  |
| Maksymalny rozmiar archiwum     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Podczas przekazywania szkoleń i testowania danych rozmiar pliku zip nie może przekroczyć 2 GB. Jeśli potrzebujesz więcej danych do szkolenia, Podziel je na kilka plików zip i przekaż je oddzielnie. Później możesz wybrać opcję uczenia się z *wielu* zestawów danych. Jednak można testować tylko z *jednego* zestawu danych.

Aby sprawdzić właściwości dźwięku lub przekonwertować istniejące audio na odpowiednie formaty, użyj <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Poniżej przedstawiono kilka przykładów działania poszczególnych działań za pomocą wiersza polecenia SoX:

| Działanie | Opis | SoX — polecenie |
|----------|-------------|-------------|
| Sprawdź format dźwięku | Użyj tego polecenia, aby sprawdzić<br>Format pliku dźwiękowego. | `sox --i <filename>` |
| Konwertuj format audio | Użyj tego polecenia, aby skonwertować<br>plik audio do pojedynczego kanału, 16-bitowy, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + oznakowane przez człowieka dane zapisu do testowania/uczenia

Aby zmierzyć dokładność dokładności zamiany mowy na tekst firmy Microsoft podczas przetwarzania plików audio, należy zapewnić, aby porównanie było oznaczone przez człowieka (słowo-by-Word). Chociaż transkrypcja przez człowieka jest często czasochłonna, konieczne jest oszacowanie dokładności i uczenie modelu dla przypadków użycia. Należy pamiętać, że ulepszenia w zakresie rozpoznawania będą tylko tak dobre jak dostarczone dane. Z tego powodu ważne jest, aby przekazywać tylko transkrypcje o wysokiej jakości.

Pliki audio mogą mieć cisz na początku i na końcu nagrywania. Jeśli to możliwe, Uwzględnij co najmniej pół drugiego wyciszenia przed i po mowę w każdym pliku przykładowym. Chociaż dźwięk z niskim woluminem nagrywania lub zakłóceniem niezakłóconego tła nie jest pomocny, nie powinien on wpływać na model niestandardowy. Przed rozpoczęciem zbierania próbek audio należy zawsze rozważyć uaktualnienie sprzętu z mikrotelefonu i przetwarzania sygnałów.

| Właściwość                 | Wartość                               |
|--------------------------|-------------------------------------|
| Format pliku              | RIFF (WAV)                          |
| Częstotliwość próbkowania              | 8 000 Hz lub 16 000 Hz               |
| Kanały                 | 1 (mono)                            |
| Maksymalna długość na dźwięk | 2 godziny (testowanie)/60 s (szkolenie) |
| Format próbki            | PCM, 16-bitowe                         |
| Format archiwum           | zip                                |
| Maksymalny rozmiar pliku zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Podczas przekazywania szkoleń i testowania danych rozmiar pliku zip nie może przekroczyć 2 GB. Można testować tylko z *jednego* zestawu danych, pamiętając, aby zachować go w odpowiednim rozmiarze pliku. Ponadto każdy plik szkoleniowy nie może przekroczyć 60 sekund, w przeciwnym razie wystąpi błąd.

Aby rozwiązać problemy, takie jak usuwanie lub podstawianie wyrazów, wymagana jest znaczna ilość danych w celu usprawnienia rozpoznawania. Ogólnie rzecz biorąc, zaleca się zapewnienie transkrypcji słów-by-Word dla około 10 do 20 godzin audio. Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

Na przykład:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Transkrypcja powinna być kodowana za pomocą kodowania UTF-8 ze znacznikiem kolejności bajtów (BOM).

Transkrypcje są normalizowane pod względem tekstu, aby mogły być przetwarzane przez system. Istnieje jednak kilka ważnych normalizacji, które należy wykonać przed przekazaniem danych do programu Speech Studio. Aby uzyskać odpowiedni język do użycia podczas przygotowywania transkrypcji, zobacz [jak utworzyć transkrypcję z oznaczeniem ludzkim](how-to-custom-speech-human-labeled-transcriptions.md)

Po zebraniu plików audio i odpowiednich transkrypcji należy je spakować jako jeden plik zip przed przekazaniem do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech </a>. Poniżej znajduje się przykładowy zestaw danych z trzema plikami audio i plik transkrypcji z etykietami ludzkimi:

> [!div class="mx-imgBorder"]
> ![Wybierz dźwięk z portalu mowy](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Zobacz [Konfigurowanie konta platformy Azure](custom-speech-overview.md#set-up-your-azure-account) , aby uzyskać listę zalecanych regionów dla subskrypcji usługi mowy. Skonfigurowanie subskrypcji mowy w jednym z tych regionów spowoduje skrócenie czasu potrzebnego do uczenia modelu. W tych regionach szkolenia mogą przetwarzać około 10 godzin pracy audio w porównaniu do 1 godziny dziennie w innych regionach. Jeśli nie można wykonać szkolenia modelu w ciągu tygodnia, model zostanie oznaczony jako niepowodzenie.

Nie wszystkie modele podstawowe obsługują szkolenia z danymi audio. Jeśli model podstawowy nie obsługuje tego typu, usługa zignoruje dźwięk i po prostu nawiąże połączenie z tekstem transkrypcji. W takim przypadku szkolenie będzie takie samo jak szkolenie związane z tekstem.

## <a name="related-text-data-for-training"></a>Powiązane dane tekstowe do szkolenia

Nazwy produktów lub funkcje, które są unikatowe, powinny zawierać powiązane dane tekstowe do szkoleń. Pokrewny tekst pomaga zapewnić poprawne rozpoznawanie. W celu usprawnienia rozpoznawania można podać dwa typy powiązanych danych tekstowych:

| Typ danych | Jak te dane ulepszają rozpoznawanie |
|-----------|------------------------------------|
| Zdania (wyrażenia długości) | Popraw dokładność podczas rozpoznawania nazw produktów lub słownika właściwych dla branż w kontekście zdania. |
| Wymowy | Popraw wymowę nietypowych warunków, akronimów lub innych wyrazów z niezdefiniowanymi wymowiemi. |

Zdania można podać jako pojedynczy plik tekstowy lub wiele plików tekstowych. Aby poprawić dokładność, użyj danych tekstowych, które są bliżej oczekiwanego wyrażenia długości. Wymowy należy dostarczyć jako pojedynczy plik tekstowy. Wszystkie elementy można spakować jako jeden plik zip i przekazać je do <a href="https://speech.microsoft.com/customspeech" target="_blank">portalu <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech </a>.

Szkolenie z pokrewnym tekstem zwykle kończy się w ciągu kilku minut.

### <a name="guidelines-to-create-a-sentences-file"></a>Wskazówki dotyczące tworzenia pliku zdań

Aby utworzyć niestandardowy model przy użyciu zdań, należy podać listę przykładowych wyrażenia długości. Wyrażenia długości _nie_ muszą być kompletne ani gramatycznie prawidłowe, ale muszą dokładnie odzwierciedlać dane wejściowe, których oczekujesz w środowisku produkcyjnym. Jeśli chcesz, aby pewne terminy miały zwiększoną wagę, Dodaj kilka zdań zawierających te określone warunki.

Ogólnie rzecz biorąc, adaptacja modelu jest najbardziej skuteczna, gdy tekst szkolenia jest jak najbliżej rzeczywistego tekstu oczekiwanego w środowisku produkcyjnym. Żargon charakterystyczne dla domeny i frazy, które chcesz rozszerzyć, powinny być zawarte w tekście szkoleniowym. Gdy to możliwe, spróbuj mieć jedno zdanie lub słowo kluczowe, które są kontrolowane w osobnym wierszu. Słowa kluczowe i frazy, które są dla Ciebie ważne (na przykład nazwy produktów), można skopiować kilka razy. Należy jednak pamiętać, że nie Kopiuj zbyt wiele — może to mieć wpływ na ogólną częstotliwość rozpoznawania.

Użyj tej tabeli, aby upewnić się, że plik powiązane dane dla wyrażenia długości został poprawnie sformatowany:

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM |
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalna wielkość pliku | 200 MB |

Ponadto należy uwzględnić następujące ograniczenia:

* Unikaj powtarzających się znaków, wyrazów lub grup wyrazów więcej niż trzy razy. Na przykład: "aaaa", "tak tak, tak jak tak" lub "jest to, że jest to". Usługa mowy może upuszczać wiersze z zbyt dużą liczbą powtórzeń.
* Nie używaj znaków specjalnych ani znaków UTF-8 powyżej `U+00A1` .
* Identyfikatory URI zostaną odrzucone.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Wskazówki dotyczące tworzenia pliku wymowy

Jeśli istnieją nietypowe terminy bez standardowych wymowy, których użytkownicy będą napotykać lub których używają, możesz podać niestandardowy plik wymowy, aby poprawić rozpoznawanie.

> [!IMPORTANT]
> Nie zaleca się używania niestandardowych plików wymowy, aby zmienić wymowę typowych wyrazów.

Obejmuje to przykłady mówionych wypowiedź i niestandardową wymowę dla każdej z nich:

| Rozpoznany/wyświetlany formularz | Formularz mówiony |
|--------------|--------------------------|
| 3CPO | trzy c p o |
| CNTK | c n t k |
| IEEE | Potrójna e |

Mówiony formularz jest wyszukiwaną sekwencją fonetyczną. Może składać się z litery, słów, sylab lub kombinacji wszystkich trzech.

Dostosowana wymowa jest dostępna w języku angielskim ( `en-US` ) i niemieckim ( `de-DE` ). W tej tabeli przedstawiono obsługiwane znaki według języka:

| Język | Regionalne | Znaki |
|----------|--------|------------|
| Angielski | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Niemiecki | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Skorzystaj z poniższej tabeli, aby upewnić się, że pokrewny plik danych dla wymowy jest poprawnie sformatowany. Pliki wymowy są małe i powinny mieć tylko kilka kilobajtów rozmiaru.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | BOM UTF-8 (ANSI jest również obsługiwany w języku angielskim) |
| Liczba wymowy na wiersz | 1 |
| Maksymalna wielkość pliku | 1 MB (1 KB dla warstwy Bezpłatna) |

## <a name="next-steps"></a>Następne kroki

* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)
* [Szkolenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](./how-to-custom-speech-train-model.md)