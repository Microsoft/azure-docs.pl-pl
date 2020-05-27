---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806401"
---
## <a name="run-the-speech-cli"></a>Uruchamianie interfejsu wiersza polecenia mowy

Teraz można przystąpić do uruchamiania interfejsu wiersza polecenia mowy, aby przetłumaczyć mowę na tekst w innym języku.

W wierszu polecenia przejdź do katalogu, który zawiera plik binarny interfejsu CLI mowy, i wpisz:

```bash
spx translate --microphone --target de-DE
```

Interfejs wiersza polecenia funkcji rozpoznawania mowy przetłumaczy język naturalny na tekst wydrukowany w języku niemieckim.
Naciśnij klawisz ENTER, aby zatrzymać narzędzie.

> [!NOTE]
> Domyślny interfejs wiersza polecenia mowy w języku angielskim. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
> Na przykład Dodaj `--source ja-JP` do rozpoznawania mowy japońskiej.
