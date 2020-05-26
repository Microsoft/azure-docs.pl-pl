---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806409"
---
## <a name="enable-microphone"></a>Włącz mikrofon

Podłącz mikrofon komputera i włącz go, a następnie wyłącz wszystkie aplikacje, które mogą również korzystać z mikrofonu. Niektóre komputery mają wbudowany mikrofon, a inne wymagają konfiguracji urządzenia Bluetooth.

## <a name="run-the-speech-cli"></a>Uruchamianie interfejsu wiersza polecenia mowy

Teraz możesz przystąpić do uruchamiania interfejsu wiersza polecenia mowy, aby rozpoznać mowę z mikrofonu.

1. **Uruchom aplikację** — w wierszu polecenia przejdź do katalogu, który zawiera plik binarny interfejsu CLI mowy, i wpisz:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Domyślny interfejs wiersza polecenia mowy w języku angielskim. Możesz wybrać inny język [z tabeli Zamiana mowy na tekst](../../../../language-support.md).
    > Na przykład Dodaj `--source de-DE` do rozpoznawania języka niemieckiego.

2. **Rozpocznij rozpoznawanie** — Zacznij mówić do mikrofonu. W czasie rzeczywistym zobaczysz transkrypcję słów do tekstu. Interfejs wiersza polecenia mowy zostanie zatrzymany po upływie okresu wyciszenia lub po naciśnięciu klawiszy CTRL + C.
