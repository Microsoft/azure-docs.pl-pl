---
title: Podstawowe informacje o usłudze SPX — Usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać narzędzia wiersza polecenia SPX do pracy z zestawem SDK mowy bez kodu i minimalnej instalacji.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 68947895891b4875ef4c57355f1236afdb8c2c7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204821"
---
# <a name="learn-the-basics-of-spx"></a>Poznaj podstawy korzystania z SPX

W tym artykule przedstawiono podstawowe wzorce użycia SPX, narzędzie wiersza polecenia do korzystania z usługi mowy bez pisania kodu. Możesz szybko przetestować główne funkcje usługi mowy, bez tworzenia środowisk programistycznych lub pisania kodu, aby sprawdzić, czy przypadki użycia mogą być odpowiednio spełnione. Ponadto protokół SPX jest gotowy do produkcji i może służyć do automatyzowania prostych przepływów pracy w usłudze mowy, przy użyciu `.bat` skryptów powłoki lub.

## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym jest subskrypcja usługi Azure Speech. Zapoznaj się z [przewodnikiem](get-started.md#new-resource) dotyczącym tworzenia nowej subskrypcji, jeśli jeszcze jej nie masz.

## <a name="download-and-install"></a>Pobieranie i instalowanie

Protokół SPX jest dostępny w systemach Windows i Linux. Zacznij od pobrania [archiwum zip](https://aka.ms/speech/spx-zips.zip), a następnie wyodrębnij go. Protokół SPX wymaga środowiska uruchomieniowego .NET Core lub .NET Framework, a następujące wersje są obsługiwane przez platformę:

* Windows: [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471), [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
* Linux: [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)

Po zainstalowaniu środowiska uruchomieniowego przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobrania i Wyodrębnij wymagany podkatalog ( `spx-net471` na przykład). W wierszu polecenia Zmień katalog na tę lokalizację, a następnie uruchom polecenie, `spx` Aby uruchomić aplikację.

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z usługi SPX, musisz najpierw wprowadzić klucz subskrypcji mowy i informacje o regionie. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .

## <a name="basic-usage"></a>Podstawowy sposób użycia

W tej sekcji przedstawiono kilka podstawowych poleceń SPX, które często są przydatne podczas pierwszego testowania i eksperymentowania. Zacznij od przeprowadzenia rozpoznawania mowy przy użyciu domyślnego mikrofonu, uruchamiając następujące polecenie.

```shell
spx recognize --microphone
```

Po wprowadzeniu polecenia protokół SPX rozpocznie nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymuje się po naciśnięciu klawisza `ENTER` . Zarejestrowane mowę są następnie rozpoznawane i konwertowane na tekst w danych wyjściowych konsoli. Synteza zamiany tekstu na mowę jest również łatwa w użyciu SPX. 

Uruchomienie następującego polecenia spowoduje wprowadzenie tekstu jako dane wejściowe i wyjście z wydanej zamiany mowy na bieżące aktywne urządzenie wyjściowe.

```shell
spx synthesize --text "Testing synthesis using SPX" --speakers
```

Oprócz rozpoznawania mowy i syntezy, można także przeprowadzić Tłumaczenie mowy przy użyciu SPX. Podobnie jak powyżej polecenie rozpoznawania mowy, uruchom następujące polecenie, aby przechwycić dźwięk z domyślnego mikrofonu i wykonać translację do tekstu w języku docelowym.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

W tym poleceniu należy określić zarówno źródło (język do **przetłumaczenia**), jak i obiekt docelowy (język do tłumaczenia **na**). Użycie `--microphone` argumentu spowoduje nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymanie po naciśnięciu klawisza `ENTER` . Dane wyjściowe to tłumaczenie tekstu w języku docelowym, zapisywane w pliku tekstowym.

> [!NOTE]
> Zapoznaj się z listą wszystkich obsługiwanych języków z odpowiednimi kodami ustawień regionalnych w [artykule język i ustawienia regionalne](language-support.md) .

## <a name="batch-operations"></a>Operacje wsadowe

Polecenia w poprzedniej sekcji doskonale sprawdzają się, jak działa usługa Speech. Jednak podczas oceniania, czy mogą być spełnione przypadki użycia, może być konieczne wykonanie operacji wsadowych względem zakresu danych wejściowych, które już masz, aby zobaczyć, w jaki sposób usługa obsługuje wiele scenariuszy. W tej sekcji pokazano, jak:

* Uruchamianie rozpoznawania mowy usługi Batch w katalogu plików audio
* Wykonaj iterację `.tsv` pliku i uruchom syntezę zamiany tekstu na mowę

## <a name="batch-speech-recognition"></a>Rozpoznawanie mowy w usłudze Batch

Jeśli masz katalog plików audio, możesz łatwo korzystać z SPX, aby szybko uruchomić rozpoznawanie mowy w usłudze Batch. Po prostu uruchom następujące polecenie, wskazując katalog za pomocą `--files` polecenia. W tym przykładzie dołączysz `\*.wav` do katalogu, aby rozpoznać wszystkie pliki znajdujące się `.wav` w katalogu. Dodatkowo należy określić `--threads` argument, aby uruchomić rozpoznawanie w przypadku 10 wątków równoległych.

> [!NOTE]
> `--threads`Argument może być również używany w następnej sekcji dla `spx synthesize` poleceń, a dostępne wątki będą zależeć od procesora i bieżącego procentu obciążenia.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Rozpoznane dane wyjściowe mowy są zapisywane `speech_output.tsv` przy użyciu `--output file` argumentu. Poniżej znajduje się przykład struktury pliku wyjściowego.

    audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
    sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
    sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.

## <a name="batch-text-to-speech-synthesis"></a>Synteza zamiany tekstu na mowę

Najprostszym sposobem uruchomienia funkcji zamiany tekstu na mowę jest utworzenie nowego `.tsv` pliku z wartościami rozdzielanymi znakami tabulacji i użycie `--foreach` polecenia w SPX. Weź pod uwagę następujący plik `text_synthesis.tsv` :

    audio.output    text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using SPX to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

 Następnie należy uruchomić polecenie, aby wskazać `text_synthesis.tsv` , wykonać syntezę dla każdego `text` pola i napisać wynik do odpowiedniej `audio.output` ścieżki jako `.wav` plik. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

To polecenie jest odpowiednikiem uruchomienia `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **dla każdego** rekordu w `.tsv` pliku. Kilka rzeczy do zanotowania:

* Nagłówki kolumn `audio.output` i `text` , odpowiadają odpowiednio do argumentów wiersza polecenia `--audio output` i `--text` . Wieloczęściowe argumenty wiersza polecenia, takie jak `--audio output` powinny być sformatowane w pliku bez spacji, brak wiodących kresek i kropki oddzielające ciągi, np. `audio.output` . Wszystkie inne istniejące argumenty wiersza polecenia można dodać do pliku jako dodatkowe kolumny przy użyciu tego wzorca.
* Gdy plik jest sformatowany w ten sposób, żadne dodatkowe argumenty nie są wymagane do przesłania do `--foreach` .
* Upewnij się, że poszczególne wartości są oddzielane `.tsv` za pomocą **karty**.

Jednak jeśli masz `.tsv` plik podobny do poniższego przykładu, z nagłówkami kolumn, które **nie pasują** do argumentów wiersza polecenia:

    wav_path    str_text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using SPX to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

Można zastąpić te nazwy pól prawidłowymi argumentami, używając następującej składni w `--foreach` wywołaniu. Jest to takie samo wywołanie jak powyżej.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Następne kroki

* Ukończ [rozpoznawanie mowy](./quickstarts/speech-to-text-from-microphone.md) lub Przewodniki Szybki Start dotyczące funkcji rozpoznawania [mowy przy](./quickstarts/text-to-speech.md) użyciu zestawu SDK.
