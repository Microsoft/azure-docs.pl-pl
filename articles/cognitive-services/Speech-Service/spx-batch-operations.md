---
title: Operacje wsadowe interfejsu wiersza polecenia mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wykonywać zamianę wsadową na tekst (rozpoznawanie mowy), przekształcać tekst wsadowe na mowę (synteza mowy) za pomocą interfejsu wiersza polecenia mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540060"
---
# <a name="speech-cli-batch-operations"></a>Operacje wsadowe interfejsu wiersza polecenia mowy

Typowe zadania związane z korzystaniem z usług Azure Speech Services to operacje wsadowe. W tym artykule dowiesz się, jak wykonywać wsadowe zamiany mowy na tekst (rozpoznawanie mowy), wsadowe zamiany tekstu na mowę (synteza mowy) za pomocą interfejsu wiersza polecenia mowy. W szczególności dowiesz się, jak:

* Uruchamianie rozpoznawania mowy usługi Batch w katalogu plików audio
* Uruchamianie syntezy mowy partii przez iterację w `.tsv` pliku

## <a name="batch-speech-to-text-speech-recognition"></a>Zamiana mowy na tekst w usłudze Batch (rozpoznawanie mowy)

Usługa mowy jest często używana do rozpoznawania mowy z plików audio. W tym przykładzie dowiesz się, jak wykonać iterację katalogu przy użyciu interfejsu wiersza polecenia mowy, aby przechwycić dane wyjściowe rozpoznawania dla każdego `.wav` pliku. `--files`Flaga służy do wskazywania w katalogu, w którym są przechowywane pliki audio, a symbol wieloznaczny `*.wav` jest używany do określenia interfejsu wiersza polecenia mowy do uruchamiania rozpoznawania dla każdego pliku z rozszerzeniem `.wav` . Dane wyjściowe dla każdego pliku rozpoznawania są zapisywane jako wartości rozdzielane tabulatorami w `speech_output.tsv` .

> [!NOTE]
> `--threads`Argument może być również używany w następnej sekcji `spx synthesize` poleceń, a dostępne wątki będą zależeć od procesora i jego bieżącego procentu obciążenia.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Poniżej znajduje się przykład struktury pliku wyjściowego.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Przetwarzanie wsadowe tekstu na mowę (synteza mowy)

Najprostszym sposobem uruchomienia funkcji zamiany tekstu na mowę jest utworzenie nowego `.tsv` pliku z wartościami rozdzielanymi znakami tabulacji i użycie `--foreach` polecenia w interfejsie CLI. Możesz utworzyć `.tsv` plik za pomocą ulubionego edytora tekstu, w tym przykładzie, Przywołajmy go `text_synthesis.tsv` :

>[!IMPORTANT]
> Podczas kopiowania zawartości tego pliku tekstowego upewnij się, że **plik nie zawiera** spacji między lokalizacją pliku a tekstem. Czasami podczas kopiowania zawartości z tego przykładu karty są konwertowane na spacje, co powoduje `spx` niepowodzenie polecenia po uruchomieniu.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Następnie należy uruchomić polecenie, aby wskazać `text_synthesis.tsv` , wykonać syntezę dla każdego `text` pola i napisać wynik do odpowiedniej `audio.output` ścieżki jako `.wav` plik.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

To polecenie jest odpowiednikiem uruchomienia `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **dla każdego** rekordu w `.tsv` pliku.

Kilka rzeczy do zanotowania:

* Nagłówki kolumn `audio.output` i `text` , odpowiadają odpowiednio do argumentów wiersza polecenia `--audio output` i `--text` . Wieloczęściowe argumenty wiersza polecenia, takie jak `--audio output` powinny być sformatowane w pliku bez spacji, brak wiodących kresek oraz kropki oddzielające ciągi, na przykład `audio.output` . Wszystkie inne istniejące argumenty wiersza polecenia można dodać do pliku jako dodatkowe kolumny przy użyciu tego wzorca.
* Gdy plik jest sformatowany w ten sposób, żadne dodatkowe argumenty nie są wymagane do przesłania do `--foreach` .
* Upewnij się, że poszczególne wartości są oddzielane `.tsv` za pomocą **karty**.

Jednak jeśli masz `.tsv` plik podobny do poniższego przykładu, z nagłówkami kolumn, które **nie pasują** do argumentów wiersza polecenia:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Można zastąpić te nazwy pól prawidłowymi argumentami, używając następującej składni w `--foreach` wywołaniu. Jest to takie samo wywołanie jak powyżej.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Następne kroki

* [Omówienie interfejsu wiersza polecenia usługi Mowa](./spx-overview.md)
* [Interfejs wiersza polecenia mowy — Szybki Start](./spx-basics.md)
