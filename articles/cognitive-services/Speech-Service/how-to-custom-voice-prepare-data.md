---
title: Jak przygotować dane dla niestandardowej usługi rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Utwórz niestandardowy głos dla swojej marki za pomocą usługi mowy. Podajesz nagrania programu Studio i skojarzone skrypty, Usługa generuje unikatowy model głosowy dostosowany do zapisanego głosu. Użyj tego głosu, aby wypróbować mowę w produktach, narzędziach i aplikacjach.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 28cc0e27e5ac97ca52f5e94a556795b1404f6961
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663200"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Przygotowywanie danych do tworzenia niestandardowego głosu

Gdy wszystko jest gotowe do utworzenia niestandardowego głosu zamiany tekstu na mowę dla aplikacji, pierwszym krokiem jest zebranie nagrań audio i skojarzonych skryptów, aby rozpocząć uczenie modelu głosowego. Usługa mowy używa tych danych do utworzenia unikatowego głosu dopasowanego do głosu w nagraniach. Po przeszkoleniu głosu możesz rozpocząć uczenie mowy w aplikacjach.

Możesz zacząć od małej ilości danych, aby utworzyć weryfikację koncepcji. Jednak im więcej danych jest zapewnianych przez użytkownika, tym bardziej naturalnie jest dźwięk. Aby można było nauczyć własny model głosu zamiany tekstu na mowę, potrzebne są nagrania audio i powiązane transkrypcje tekstu. Na tej stronie zostanie przejrzane typy danych, sposób ich użycia oraz sposób zarządzania każdym z nich.

## <a name="data-types"></a>Typy danych

Zestaw danych szkolenia głosowego zawiera nagrania audio i plik tekstowy z skojarzonymi transkrypcjami. Każdy plik audio powinien zawierać pojedynczy wypowiedź (jedno zdanie lub pojedynczy tryb dla systemu dialogu) i może być krótszy niż 15 sekund.

W niektórych przypadkach możesz nie mieć odpowiedniego zestawu danych, który będzie gotowy do testowania niestandardowego szkolenia głosowego z dostępnymi plikami audio, krótkimi lub długimi, z lub bez transkrypcji. Firma Microsoft udostępnia narzędzia (beta), które ułatwiają segmentację dźwięku w wyrażenia długości i przygotowanie transkrypcji przy użyciu [interfejsu API transkrypcji usługi Batch](batch-transcription.md).

Ta tabela zawiera listę typów danych i sposób ich użycia w celu utworzenia niestandardowego modelu głosu zamiany tekstu na mowę.

| Typ danych | Opis | Kiedy stosować | Wymagana jest dodatkowa usługa | Ilość szkoleniowa modelu | Ustawienia regionalne |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Poszczególne wyrażenia długości + pasujące transkrypcje** | Kolekcja (zip) plików audio (. wav) jako pojedyncze wyrażenia długości. Każdy plik audio powinien mieć długość co najmniej 15 sekund, sparowany z sformatowaną transkrypcją (. txt). | Profesjonalne nagrania z pasującymi transkrypcjami | Gotowe do uczenia się. | Brak twardych wymagań dla en-US i zh-CN. Więcej niż 2000 wyrażenia długości różne ustawienia regionalne. | [Wszystkie niestandardowe ustawienia regionalne głosu](language-support.md#customization) |
| **Długi dźwięk + transkrypcja (beta)** | Kolekcja (zip) długich, niesegmentowych plików audio (dłużej niż 20 sekund) sparowana z transkrypcją (. txt), która zawiera wszystkie wymawiane słowa. | Masz pliki audio i pasujące transkrypcje, ale nie są one podzielone na wyrażenia długości. | Segmentacja (przy użyciu transkrypcji partii).<br>Przekształcanie formatu audio, gdy jest to wymagane. | Brak wymagań twardych  | [Wszystkie niestandardowe ustawienia regionalne głosu](language-support.md#customization) |
| **Tylko audio (beta)** | Kolekcja (zip) plików audio bez transkrypcji. | Dostępne są tylko pliki audio, bez transkrypcji. | Segmentacja + generacja transkrypcji (przy użyciu transkrypcji partii).<br>Przekształcanie formatu audio, gdy jest to wymagane.| Brak wymagań twardych | [Wszystkie niestandardowe ustawienia regionalne głosu](language-support.md#customization) |

Pliki powinny być pogrupowane według typu w zestawie danych i przekazywane jako plik zip. Każdy zestaw danych może zawierać tylko jeden typ danych.

> [!NOTE]
> Maksymalna liczba zestawów danych, które mogą zostać zaimportowane na subskrypcję, to 10. zip plików dla użytkowników bezpłatnej subskrypcji (F0) i użytkowników 500 for Standard Subscription (S0).

## <a name="individual-utterances--matching-transcript"></a>Poszczególne wyrażenia długości + pasujące transkrypcje

Możesz przygotować nagrania poszczególnych wyrażenia długości i zgodne transkrypcji na dwa sposoby. Napisz skrypt i odczytaj go za pomocą głosu talent lub użyj publicznie dostępnego dźwięku i transkrypcja go do tekstu. Jeśli wykonasz te czynności, Edytuj disfluencies z plików audio, takich jak "UM" i innych dźwięków Filler, stutters, mumbled słów lub wymowy.

Aby utworzyć dobrą czcionkę głosową, Utwórz nagrania w cichym pokoju przy użyciu mikrofonu o wysokiej jakości. Istotna jest stała ilość głosu, częstotliwość mówienia, gęstość głosu i mannerisms.

> [!TIP]
> Aby utworzyć głos do użycia w środowisku produkcyjnym, zalecamy korzystanie z profesjonalnego nagrywania programu Studio i głosu talent. Aby uzyskać więcej informacji, zobacz [jak zarejestrować przykłady głosu dla niestandardowego głosu](record-custom-voice-samples.md).

### <a name="audio-files"></a>Pliki audio

Każdy plik audio powinien zawierać pojedynczy wypowiedź (pojedyncze zdanie lub pojedynczy tryb okna dialogowego), krótszy niż 15 sekund. Wszystkie pliki muszą znajdować się w tym samym języku mówionym. Niestandardowe głosy tekstu na mowę dla wielu języków nie są obsługiwane, z wyjątkiem Chinese-English dwujęzykowego. Każdy plik audio musi mieć unikatową nazwę pliku numerycznego z rozszerzeniem nazwy pliku WAV.

Postępuj zgodnie z poniższymi wskazówkami podczas przygotowywania dźwięku.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | RIFF (. wav), pogrupowane w pliku. zip |
| Częstotliwość próbkowania | Co najmniej 16 000 Hz |
| Format próbki | PCM, 16-bitowe |
| Nazwa pliku | Numeryczne, z rozszerzeniem. wav. Nie jest dozwolone Duplikowanie nazw plików. |
| Długość audio | Krótsze niż 15 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

> [!NOTE]
> pliki. wav o współczynniku próbkowania mniejszym niż 16 000 Hz zostaną odrzucone. Jeśli plik. zip zawiera pliki WAV z różnymi współczynnikami próbkowania, zostaną zaimportowane tylko te, które są równe lub większe niż 16 000 Hz. Portal zaimportuje teraz archiwum zip do 200 MB. Można jednak przekazać wiele archiwów.

### <a name="transcripts"></a>Transkrypcji

Plik transkrypcji jest plikiem w postaci zwykłego tekstu. Te wskazówki służą do przygotowania transkrypcji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (. txt) |
| Format kodowania | ANSI/ASCII, UTF-8, UTF-8 — BOM, UTF-16-LE lub UTF-16. W przypadku metody zh-CN, ANSI/ASCII i UTF-8 nie są obsługiwane. |
| Liczba wypowiedzi w wierszu | **Jeden** — każdy wiersz pliku transkrypcji powinien zawierać nazwę jednego z plików audio, po którym następuje odpowiednie transkrypcje. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t). |
| Maksymalna wielkość pliku | 2048 MB |

Poniżej znajduje się przykład sposobu, w jaki transkrypcje są zorganizowane wypowiedź przez wypowiedź w jednym pliku txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Należy pamiętać, że transkrypcje są 100% dokładne transkrypcje odpowiedniego dźwięku. Błędy w transkrypcjach spowodują utratę jakości podczas uczenia się.

> [!TIP]
> Podczas tworzenia głosów produkcji zamiany tekstu na mowę wybierz pozycję wyrażenia długości (lub napisz skrypty), która uwzględnia zarówno pokrycie, jak i wydajność. Masz problemy z uzyskaniem żądanych wyników? [Skontaktuj się z niestandardowym zespołem mowy,](mailto:speechsupport@microsoft.com) aby dowiedzieć się więcej o tym, jak się skontaktować.

## <a name="long-audio--transcript-beta"></a>Długi dźwięk + transkrypcja (beta)

W niektórych przypadkach może nie być dostępne żadne segmenty audio. Udostępniamy usługę (beta) za pomocą niestandardowego portalu głosowego, która pomaga w segmentacji długich plików audio i tworzeniu transkrypcji. Należy pamiętać, że ta usługa zostanie obciążona opłatą za użycie subskrypcji zamiany mowy na tekst.

> [!NOTE]
> Usługa segmentacji Long-audio będzie korzystać z funkcji transkrypcji przetwarzania wsadowego zamiany mowy na tekst, która obsługuje tylko użytkowników Standard Subscription (S0). Podczas przetwarzania segmentacji pliki audio i transkrypcje zostaną również wysłane do usługi Custom Speech, aby uściślić model rozpoznawania, dzięki czemu można poprawić dokładność danych. W trakcie tego procesu nie będą przechowywane żadne dane. Po zakończeniu segmentacji tylko segmenty wyrażenia długości i ich transkrypcje mapowania będą przechowywane na potrzeby pobierania i uczenia.

### <a name="audio-files"></a>Pliki audio

Postępuj zgodnie z poniższymi wskazówkami podczas przygotowywania audio dla segmentacji.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Plik RIFF (. wav) z częstotliwością próbkowania wynoszącą co najmniej 16 kHz-16 bitów w module PCM lub MP3 z szybkością bitową wynoszącą co najmniej 256 KB/s, pogrupowane w pliku. zip |
| Nazwa pliku | Obsługiwane znaki ASCII i Unicode. Nie są dozwolone zduplikowane nazwy. |
| Długość audio | Dłużej niż 20 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

Wszystkie pliki audio powinny być pogrupowane w pliku zip. Pliki. wav i. mp3 można umieścić w jednym pliku zip audio. Na przykład można przekazać plik zip zawierający plik audio o nazwie "kingstory. wav", 45-Second-Long i innego dźwięku o nazwie "queenstory.mp3", 200-Second-Long. Wszystkie pliki. mp3 zostaną przekształcone w formacie wav po przetworzeniu.

### <a name="transcripts"></a>Transkrypcji

Transkrypcje muszą zostać przygotowane do specyfikacji wymienionych w tej tabeli. Każdy plik audio musi być zgodny z transkrypcją.

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Zwykły tekst (. txt), pogrupowany w pliku. zip |
| Nazwa pliku | Użyj tej samej nazwy co pasujący plik audio |
| Format kodowania | UTF-8 — tylko BOM |
| Liczba wypowiedzi w wierszu | Bez ograniczeń |
| Maksymalna wielkość pliku | 2048 MB |

Wszystkie pliki transkrypcji w tym typie danych powinny być pogrupowane w pliku zip. Na przykład przekazano plik zip zawierający plik audio o nazwie "kingstory. wav", 45 s Long, a drugi o nazwie "queenstory.mp3", 200 s Long. Należy przekazać inny plik zip zawierający dwa transkrypcje, jeden o nazwie "kingstory.txt", drugi jeden "queenstory.txt". W każdym zwykłym pliku tekstowym należy zapewnić pełną transkrypcję dla pasującego dźwięku.

Po pomyślnym przekazaniu zestawu danych pomożemy Ci podzielić plik audio na wyrażenia długości w oparciu o podaną transkrypcję. Możesz sprawdzić segment wyrażenia długości i pasujące transkrypcje, pobierając zestaw danych. Unikatowe identyfikatory będą przypisywane do wyrażenia długości z segmentami automatycznie. Ważne jest, aby upewnić się, że transkrypcje są zgodne z dokładnością do 100%. Błędy w transkrypcjach mogą zmniejszyć dokładność podczas segmentacji audio i dodatkowo wprowadzić utratę jakości w fazie szkoleniowej, która jest dostępna później.

## <a name="audio-only-beta"></a>Tylko audio (beta)

Jeśli nie masz transkrypcji dla nagrań dźwiękowych, użyj opcji **tylko audio** , aby przekazać dane. Nasz system może pomóc Ci segmentować i transkrypcja pliki audio. Należy pamiętać, że ta usługa będzie uwzględniać użycie subskrypcji zamiany mowy na tekst.

Postępuj zgodnie z poniższymi wskazówkami podczas przygotowywania dźwięku.

> [!NOTE]
> Usługa segmentacji Long-audio będzie korzystać z funkcji transkrypcji przetwarzania wsadowego zamiany mowy na tekst, która obsługuje tylko użytkowników Standard Subscription (S0).

| Właściwość | Wartość |
| -------- | ----- |
| Format pliku | Plik RIFF (. wav) z częstotliwością próbkowania wynoszącą co najmniej 16 kHz-16 bitów w module PCM lub MP3 z szybkością bitową wynoszącą co najmniej 256 KB/s, pogrupowane w pliku. zip |
| Nazwa pliku | Obsługiwane znaki ASCII i Unicode. Nie można duplikować nazwy. |
| Długość audio | Dłużej niż 20 sekund |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2048 MB |

Wszystkie pliki audio powinny być pogrupowane w pliku zip. Po pomyślnym przekazaniu zestawu danych pomożemy Ci podzielić plik audio na wyrażenia długości w oparciu o usługę transkrypcji przetwarzania wsadowego mowy. Unikatowe identyfikatory będą przypisywane do wyrażenia długości z segmentami automatycznie. Zgodne transkrypcje zostaną wygenerowane przy użyciu funkcji rozpoznawania mowy. Wszystkie pliki. mp3 zostaną przekształcone w formacie wav po przetworzeniu. Możesz sprawdzić segment wyrażenia długości i pasujące transkrypcje, pobierając zestaw danych.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: zapisywanie przykładów głosu](record-custom-voice-samples.md)