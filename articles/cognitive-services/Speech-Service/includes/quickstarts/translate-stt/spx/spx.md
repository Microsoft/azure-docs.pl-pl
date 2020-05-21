---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 57383668ef025b46d0bae1f98c5ed5cd75417c63
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715029"
---
## <a name="run-the-spx-tool"></a>Uruchamianie narzędzia SPX

Teraz można przystąpić do uruchamiania narzędzia SPX, aby przetłumaczyć mowę na tekst w innym języku.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny narzędzia SPX, i wpisz:

```bash
spx translate --microphone --target de-DE
```

Narzędzie SPX spowoduje przetłumaczenie języka naturalnego w języku angielskim na tekst drukowany w języku niemieckim.
Naciśnij klawisz ENTER, aby zatrzymać narzędzie.

> [!NOTE]
> Domyślnie używany jest język angielski. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source ja-JP` do rozpoznawania mowy japońskiej.
