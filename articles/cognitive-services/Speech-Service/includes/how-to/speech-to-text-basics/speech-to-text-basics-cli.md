---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570604"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Zamiana mowy na tekst z mikrofonu

Podłącz mikrofon komputera i włącz go, a następnie wyłącz wszystkie aplikacje, które mogą również korzystać z mikrofonu. Niektóre komputery mają wbudowany mikrofon, a inne wymagają konfiguracji urządzenia Bluetooth.

Teraz możesz przystąpić do uruchamiania interfejsu wiersza polecenia mowy, aby rozpoznać mowę z mikrofonu. W wierszu polecenia przejdź do katalogu, który zawiera plik binarny CLI, i uruchom następujące polecenie.

```bash
spx recognize --microphone
```

> [!NOTE]
> Domyślny interfejs wiersza polecenia mowy w języku angielskim. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

Porozmawiaj z mikrofonem i zobaczysz transkrypcję słów do tekstu w czasie rzeczywistym. Interfejs wiersza polecenia mowy zostanie zatrzymany po upływie okresu wyciszenia lub po naciśnięciu klawiszy CTRL + C.

## <a name="speech-to-text-from-audio-file"></a>Zamiana mowy na tekst z pliku audio

Interfejs wiersza polecenia mowy umożliwia rozpoznawanie mowy w wielu formatach plików i językach naturalnych. W tym przykładzie można użyć dowolnego pliku WAV (16kHz lub 8kHz, 16-bitowy i mono PCM), który zawiera angielską mowę. Lub jeśli chcesz uzyskać szybki przykład, Pobierz plik <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a> i skopiuj go do tego samego katalogu co plik binarny interfejsu wiersza polecenia mowy.

Teraz można przystąpić do uruchamiania interfejsu wiersza polecenia mowy w celu rozpoznawania mowy znalezionej w pliku audio przez uruchomienie następującego polecenia.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Domyślny interfejs wiersza polecenia mowy w języku angielskim. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

Interfejs wiersza polecenia mowy wyświetli tekst transkrypcji mowy na ekranie.