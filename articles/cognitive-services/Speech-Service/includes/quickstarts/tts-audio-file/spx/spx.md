---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806425"
---
## <a name="run-the-speech-cli"></a>Uruchamianie interfejsu wiersza polecenia mowy

Teraz możesz uruchomić interfejs wiersza polecenia mowy, aby wytłumaczyć mowę z tekstu na nowy plik audio.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny interfejsu CLI mowy, i wpisz:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Interfejs wiersza polecenia mowy spowoduje utworzenie języka naturalnego w języku angielskim w `greetings.wav` pliku audio.
W systemie Windows można odtworzyć plik audio, wprowadzając polecenie `start greetings.wav` .
