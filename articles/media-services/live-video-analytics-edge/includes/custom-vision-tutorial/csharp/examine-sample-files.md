---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358287"
---
1. W Visual Studio Code przejdź do węzła src/Edge. Zobaczysz plik ENV, który został utworzony wraz z kilkoma plikami szablonu wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego z niektórymi wartościami zastępczymi. Plik ENV ma wartości dla tych zmiennych.
1. Następnie przejdź do folderu src/Cloud-to-Device-Console-App. Tutaj zobaczysz appsettings.jsw utworzonym pliku wraz z kilkoma innymi plikami:

    * C2D-Console-App. csproj: jest to plik projektu dla Visual Studio Code.
    * operations.js: ten plik zawiera listę różnych operacji, które program ma uruchomić.
    * Program. cs: Ten przykładowy kod programu:

        * Ładuje ustawienia aplikacji.
        * Wywołuje funkcję analizy filmów wideo na żywo na IoT Edge metod bezpośrednich modułu w celu utworzenia topologii, utworzenia wystąpienia grafu i aktywowania grafu.
        * Wstrzymuje pracę, aby przeanalizować dane wyjściowe grafu w oknie **terminalu** oraz zdarzenia wysyłane do centrum IoT w oknie **danych wyjściowych** .
        * Dezaktywuje wystąpienie grafu, usuwa wystąpienie grafu i usuwa topologię grafu.
