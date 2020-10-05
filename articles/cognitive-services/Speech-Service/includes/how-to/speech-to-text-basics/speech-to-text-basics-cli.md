---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327128"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania i transkrypcja mowy (często nazywanej zamianą mowy na tekst). W tym przewodniku szybki start dowiesz się, jak korzystać z interfejsu wiersza polecenia mowy w aplikacjach i produktach do wykonywania konwersji wysokiej jakości zamiany mowy na tekst.

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