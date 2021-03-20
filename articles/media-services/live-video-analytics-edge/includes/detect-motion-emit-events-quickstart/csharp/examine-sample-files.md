---
ms.openlocfilehash: 1e3ba4b39baa045f35c232fa97c14bc78d8de5ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88691115"
---
1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *. env* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego, gdzie zmienne są używane dla niektórych właściwości. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.js* pliku i kilku innych plików:

    * ***C2D-Console-App. csproj*** — plik projektu dla Visual Studio Code.
    * ***operations.js*** listę operacji, które program ma uruchomić.
    * ***Program. cs*** — przykładowy kod programu. Ten kod:
    
      * Ładuje ustawienia aplikacji.
      * Wywołuje metody bezpośrednie, które są udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](../../../direct-methods.md).
      * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
      * Wywołuje bezpośrednie metody czyszczenia zasobów.   
