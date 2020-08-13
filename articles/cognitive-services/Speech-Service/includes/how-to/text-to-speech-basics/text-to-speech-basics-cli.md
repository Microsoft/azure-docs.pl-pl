---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173136"
---
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