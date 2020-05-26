---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806301"
---
## <a name="find-a-file-that-contains-speech"></a>Znajdowanie pliku zawierającego mowę

Interfejs wiersza polecenia mowy umożliwia rozpoznawanie mowy w wielu formatach plików i językach naturalnych. W tym przewodniku szybki start można użyć pliku WAV (16kHz lub 8kHz, 16-bitowy i mono PCM), który zawiera angielską mowę.

1. Pobierz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Skopiuj `whatstheweatherlike.wav` plik do tego samego katalogu co plik binarny interfejsu wiersza polecenia mowy.

## <a name="run-the-speech-cli"></a>Uruchamianie interfejsu wiersza polecenia mowy

Teraz można przystąpić do uruchamiania interfejsu wiersza polecenia mowy w celu rozpoznania mowy znalezionej w pliku dźwiękowym.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny interfejsu CLI mowy, i wpisz:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Domyślny interfejs wiersza polecenia mowy w języku angielskim. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

Interfejs wiersza polecenia mowy wyświetli tekst transkrypcji mowy na ekranie. Następnie interfejs wiersza polecenia mowy zostanie zamknięty.
