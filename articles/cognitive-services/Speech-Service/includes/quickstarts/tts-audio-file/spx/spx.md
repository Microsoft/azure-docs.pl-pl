---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715181"
---
## <a name="run-the-spx-tool"></a>Uruchamianie narzędzia SPX

Teraz możesz przystąpić do uruchamiania narzędzia SPX, aby wytłumaczyć mowę z tekstu na nowy plik audio.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny narzędzia SPX, i wpisz:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Narzędzie SPX spowoduje utworzenie języka naturalnego w języku angielskim w `greetings.wav` pliku audio.
W systemie Windows można odtworzyć plik audio, wprowadzając polecenie `start greetings.wav` .
