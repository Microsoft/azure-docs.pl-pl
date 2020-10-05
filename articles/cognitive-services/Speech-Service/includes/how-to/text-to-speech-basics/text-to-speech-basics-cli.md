---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332466"
---
W tym przewodniku szybki start przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Syntetyzowanie mowy przy użyciu głośnika

Teraz możesz przystąpić do uruchamiania interfejsu wiersza polecenia mowy, aby wyszukać mowę z tekstu. W wierszu poleceń przejdź do katalogu, który zawiera plik binarny interfejsu wiersza polecenia mowy. Następnie uruchom następujące polecenie.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Interfejs wiersza polecenia mowy umożliwia tworzenie języka naturalnego w języku angielskim przez prelegenta komputerów.

## <a name="synthesize-speech-to-a-file"></a>Wyrównać mowę do pliku

Uruchom następujące polecenie, aby zmienić dane wyjściowe głośników na `.wav` plik.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Interfejs wiersza polecenia mowy spowoduje utworzenie języka naturalnego w języku angielskim w `greetings.wav` pliku audio.
W systemie Windows można odtworzyć plik audio, wprowadzając polecenie `start greetings.wav` .