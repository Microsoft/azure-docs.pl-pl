---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691122"
---
1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *. env* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego, gdzie zmienne są używane dla niektórych właściwości. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.js* pliku i kilku innych plików:

    * ***operations.js*** listę operacji, które program ma uruchomić.
    * **Main.py** — przykładowy kod programu. Ten kod:
    
      * Ładuje ustawienia aplikacji.
      * Wywołuje metody bezpośrednie, które są udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](../../../direct-methods.md).
      * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
      * Wywołuje bezpośrednie metody czyszczenia zasobów.   

