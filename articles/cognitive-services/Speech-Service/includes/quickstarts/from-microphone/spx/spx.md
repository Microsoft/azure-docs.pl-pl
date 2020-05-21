---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715317"
---
## <a name="enable-microphone"></a>Włącz mikrofon

Podłącz mikrofon komputera i włącz go, a następnie wyłącz wszystkie aplikacje, które mogą również korzystać z mikrofonu. Niektóre komputery mają wbudowany mikrofon, a inne wymagają konfiguracji urządzenia Bluetooth.

## <a name="run-the-spx-tool"></a>Uruchamianie narzędzia SPX

Teraz wszystko jest gotowe do uruchomienia narzędzia SPX do rozpoznawania mowy z mikrofonu.

1. **Uruchom aplikację** — w wierszu polecenia przejdź do katalogu, który zawiera plik binarny narzędzia SPX, i wpisz:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Domyślnie używany jest język angielski. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
    > Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

2. **Rozpocznij rozpoznawanie** — Zacznij mówić do mikrofonu. W czasie rzeczywistym zobaczysz transkrypcję słów do tekstu. Narzędzie SPX zostanie zatrzymane po upływie okresu wyciszenia lub po naciśnięciu klawiszy CTRL + C.
