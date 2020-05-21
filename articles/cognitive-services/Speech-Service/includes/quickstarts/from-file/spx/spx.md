---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714693"
---
## <a name="find-a-file-that-contains-speech"></a>Znajdowanie pliku zawierającego mowę

Narzędzie SPX może rozpoznawać mowę w wielu formatach plików i w językach naturalnych. W tym przewodniku szybki start można użyć pliku WAV (16kHz lub 8kHz, 16-bitowy i mono PCM), który zawiera angielską mowę.

1. Pobierz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Skopiuj `whatstheweatherlike.wav` plik do tego samego katalogu co plik binarny narzędzia SPX.

## <a name="run-the-spx-tool"></a>Uruchamianie narzędzia SPX

Teraz można przystąpić do uruchamiania narzędzia SPX w celu rozpoznawania mowy znalezionej w pliku dźwiękowym.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny narzędzia SPX, i wpisz:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Domyślnie używany jest język angielski. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

W narzędziu SPX zostanie wyświetlony tekst transkrypcji mowy na ekranie. Następnie narzędzie SPX zostanie zamknięte.
