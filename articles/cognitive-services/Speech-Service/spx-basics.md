---
title: Podstawowe informacje o interfejsie wiersza polecenia mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać narzędzia poleceń interfejsu wiersza polecenia mowy do pracy z usługą mowy bez kodu i minimalnej konfiguracji.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 7ca290b7b7df9e4e03aec78b611361ba52942523
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286693"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Poznaj podstawowe informacje o interfejsie wiersza polecenia mowy

Ten artykuł zawiera informacje o podstawowych wzorcach użycia interfejsu wiersza polecenia mowy, który umożliwia korzystanie z usługi mowy bez pisania kodu. Możesz szybko przetestować główne funkcje usługi mowy, bez tworzenia środowisk programistycznych lub pisania kodu, aby sprawdzić, czy przypadki użycia mogą być odpowiednio spełnione. Ponadto interfejs wiersza polecenia mowy jest gotowy do produkcji i może służyć do automatyzowania prostych przepływów pracy w usłudze mowy przy użyciu `.bat` skryptów powłoki lub.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Podstawowy sposób użycia

W tej sekcji przedstawiono kilka podstawowych poleceń SPX, które często są przydatne podczas pierwszego testowania i eksperymentowania. Zacznij od przejrzenia pomocy wbudowanej do narzędzia, uruchamiając następujące polecenie.

```shell
spx
```

Uwaga **:** tematy pomocy wymienione po prawej stronie parametrów poleceń. Możesz wprowadzić te polecenia, aby uzyskać szczegółową pomoc dotyczącą poleceń podrzędnych.

Tematy pomocy można wyszukiwać według słowa kluczowego. Na przykład wprowadź następujące polecenie, aby wyświetlić listę przykładów użycia interfejsu wiersza polecenia mowy:

```shell
spx help find --topics "examples"
```

Wprowadź następujące polecenie, aby wyświetlić opcje polecenia Rozpoznaj:

```shell
spx help recognize
```

Teraz Użyj usługi Speech do przeprowadzenia rozpoznawania mowy przy użyciu domyślnego mikrofonu, uruchamiając następujące polecenie.

```shell
spx recognize --microphone
```

Po wprowadzeniu polecenia protokół SPX rozpocznie nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymuje się po naciśnięciu klawisza `ENTER` . Zarejestrowane mowę są następnie rozpoznawane i konwertowane na tekst w danych wyjściowych konsoli. Synteza zamiany tekstu na mowę jest również łatwa w użyciu interfejsu wiersza polecenia. 

Uruchomienie następującego polecenia spowoduje wprowadzenie tekstu jako dane wejściowe i wyjście z wydanej zamiany mowy na bieżące aktywne urządzenie wyjściowe.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Oprócz rozpoznawania mowy i syntezy, można także przeprowadzić Tłumaczenie mowy przy użyciu interfejsu wiersza polecenia mowy. Podobnie jak powyżej polecenie rozpoznawania mowy, uruchom następujące polecenie, aby przechwycić dźwięk z domyślnego mikrofonu i wykonać translację do tekstu w języku docelowym.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

W tym poleceniu należy określić zarówno źródło (język do **przetłumaczenia** ), jak i obiekt docelowy (język do tłumaczenia **na** ). Użycie `--microphone` argumentu spowoduje nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymanie po naciśnięciu klawisza `ENTER` . Dane wyjściowe to tłumaczenie tekstu w języku docelowym, zapisywane w pliku tekstowym.

> [!NOTE]
> Zapoznaj się z listą wszystkich obsługiwanych języków z odpowiednimi kodami ustawień regionalnych w [artykule język i ustawienia regionalne](language-support.md) .

### <a name="configuration-files-in-the-datastore"></a>Pliki konfiguracji w magazynie danych

Zachowanie interfejsu wiersza polecenia mowy może polegać na ustawieniach w plikach konfiguracji, do których można odwoływać się w ramach wywołań interfejsu wiersza polecenia mowy przy użyciu znaku @.
Interfejs wiersza polecenia mowy zapisuje nowe ustawienie w nowym `./spx/data` podkatalogu, który tworzy w bieżącym katalogu roboczym.
Podczas wyszukiwania wartości konfiguracji interfejs wiersza polecenia mowy wyszukuje w bieżącym katalogu roboczym, a następnie w magazynie danych pod adresem `./spx/data` , a następnie w innych magazynach danych, łącznie z końcowym magazynem danych tylko do odczytu w `spx` pliku binarnym.
Poprzednio magazyn danych został użyty do zapisania `@key` `@region` wartości i, więc nie trzeba ich określić przy użyciu każdego wywołania wiersza polecenia.
Przy użyciu plików konfiguracji można także przechowywać własne ustawienia konfiguracji, a nawet używać ich do przekazywania adresów URL lub innej zawartości dynamicznej wygenerowanej w czasie wykonywania.

W tej sekcji pokazano użycie pliku konfiguracji w lokalnym magazynie danych do przechowywania i pobierania ustawień poleceń przy użyciu `spx config` i przechowywania danych wyjściowych z interfejsu wiersza polecenia mowy przy użyciu `--output` opcji.

Poniższy przykład czyści `@my.defaults` plik konfiguracji, dodaje pary klucz-wartość dla **klucza** i **regionu** w pliku, a następnie używa konfiguracji w wywołaniu `spx recognize` .

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Zawartość dynamiczną można także zapisać w pliku konfiguracji. Na przykład następujące polecenie tworzy niestandardowy model mowy i zapisuje adres URL nowego modelu w pliku konfiguracji. Następne polecenie czeka, aż model w tym adresie URL będzie gotowy do użycia przed zwróceniem.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Poniższy przykład zapisuje dwa adresy URL do `@my.datasets.txt` pliku konfiguracji.
W tym scenariuszu `--output` może zawierać opcjonalne słowo kluczowe **Add** , aby utworzyć plik konfiguracji lub dołączyć do istniejącego pliku.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Aby uzyskać więcej informacji na temat plików magazynu danych, w tym używania domyślnych plików konfiguracji ( `@spx.default` , `@default.config` i `@*.default.config` dla ustawień domyślnych specyficznych dla poleceń), wprowadź następujące polecenie:

```shell
spx help advanced setup
```

## <a name="batch-operations"></a>Operacje wsadowe

Polecenia w poprzedniej sekcji doskonale sprawdzają się, jak działa usługa Speech. Jednak podczas oceniania, czy mogą być spełnione przypadki użycia, może być konieczne wykonanie operacji wsadowych względem zakresu danych wejściowych, które już masz, aby zobaczyć, w jaki sposób usługa obsługuje wiele scenariuszy. W tej sekcji pokazano, jak:

* Uruchamianie rozpoznawania mowy usługi Batch w katalogu plików audio
* Wykonaj iterację `.tsv` pliku i uruchom syntezę zamiany tekstu na mowę

## <a name="batch-speech-recognition"></a>Rozpoznawanie mowy w usłudze Batch

Jeśli masz katalog plików audio, możesz łatwo uruchomić interfejs wiersza polecenia mowy, aby szybko uruchamiać rozpoznawanie mowy w usłudze Batch. Po prostu uruchom następujące polecenie, wskazując katalog za pomocą `--files` polecenia. W tym przykładzie dołączysz `\*.wav` do katalogu, aby rozpoznać wszystkie pliki znajdujące się `.wav` w katalogu. Dodatkowo należy określić `--threads` argument, aby uruchomić rozpoznawanie w przypadku 10 wątków równoległych.

> [!NOTE]
> `--threads`Argument może być również używany w następnej sekcji `spx synthesize` poleceń, a dostępne wątki będą zależeć od procesora i jego bieżącego procentu obciążenia.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Rozpoznane dane wyjściowe mowy są zapisywane `speech_output.tsv` przy użyciu `--output file` argumentu. Poniżej znajduje się przykład struktury pliku wyjściowego.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Synteza zamiany tekstu na mowę

Najprostszym sposobem uruchomienia funkcji zamiany tekstu na mowę jest utworzenie nowego `.tsv` pliku z wartościami rozdzielanymi znakami tabulacji `--foreach` . Weź pod uwagę następujący plik `text_synthesis.tsv` :

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Następnie należy uruchomić polecenie, aby wskazać `text_synthesis.tsv` , wykonać syntezę dla każdego `text` pola i napisać wynik do odpowiedniej `audio.output` ścieżki jako `.wav` plik. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

To polecenie jest odpowiednikiem uruchomienia `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **dla każdego** rekordu w `.tsv` pliku. Kilka rzeczy do zanotowania:

* Nagłówki kolumn `audio.output` i `text` , odpowiadają odpowiednio do argumentów wiersza polecenia `--audio output` i `--text` . Wieloczęściowe argumenty wiersza polecenia, takie jak `--audio output` powinny być sformatowane w pliku bez spacji, brak wiodących kresek i kropki oddzielające ciągi, np. `audio.output` . Wszystkie inne istniejące argumenty wiersza polecenia można dodać do pliku jako dodatkowe kolumny przy użyciu tego wzorca.
* Gdy plik jest sformatowany w ten sposób, żadne dodatkowe argumenty nie są wymagane do przesłania do `--foreach` .
* Upewnij się, że poszczególne wartości są oddzielane `.tsv` za pomocą **karty**.

Jednak jeśli masz `.tsv` plik podobny do poniższego przykładu, z nagłówkami kolumn, które **nie pasują** do argumentów wiersza polecenia:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Można zastąpić te nazwy pól prawidłowymi argumentami, używając następującej składni w `--foreach` wywołaniu. Jest to takie samo wywołanie jak powyżej.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Następne kroki

* Ukończ [rozpoznawanie mowy](get-started-speech-to-text.md?pivots=programmer-tool-spx) lub Przewodniki Szybki Start dotyczące funkcji rozpoznawania [mowy przy](get-started-text-to-speech.md?pivots=programmer-tool-spx) użyciu interfejsu wiersza polecenia mowy.
