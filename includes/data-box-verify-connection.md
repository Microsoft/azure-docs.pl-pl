---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553169"
---
Wykonaj poniższe czynności, aby nawiązać i zweryfikować połączenie z kontem magazynu.

1. W Eksploratorze usługi Storage otwórz okno dialogowe **Połącz z usługą Azure Storage**. W oknie dialogowym **Połącz z usługą Azure Storage** wybierz pozycję **Użyj nazwy i klucza konta magazynu**.

    ![Zrzut ekranu przedstawia okno dialogowe łączenie z usługą Azure Storage z wybraną opcją Użyj nazwy i klucza konta magazynu.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Wklej wartości dla swojego konta w polach **Nazwa konta** i **Klucz konta** (wartość 1 klucza ze strony **Połącz i skopiuj** w lokalnym internetowym interfejsie użytkownika). Wybierz opcję Domena punktów końcowych magazynu w polu **Inne (podaj poniżej)** , a następnie podaj punkt końcowy usługi obiektów blob, jak pokazano poniżej. Opcję **Użyj protokołu HTTP** zaznacz tylko wtedy, gdy przesyłasz za pośrednictwem protokołu *http*. Jeśli używasz protokołu *https* , nie zaznaczaj tej opcji. Wybierz pozycję **Dalej**.

    ![Zrzut ekranu przedstawia okno dialogowe łączenie z nazwą i kluczem z wprowadzonymi wartościami.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Sprawdź informacje podane w oknie dialogowym **Podsumowanie połączenia**. Wybierz pozycję **Połącz**.

    ![Zrzut ekranu przedstawia okno dialogowe Podsumowanie połączenia z wybraną pozycją Połącz.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Pomyślnie dodane konto jest wyświetlane w lewym okienku Eksploratora usługi Storage z wartościami (Zewnętrzne, Inne) dołączonymi do jego nazwy. Kliknij pozycję **Kontenery obiektów blob** , aby wyświetlić kontener.

    ![Zrzut ekranu przedstawia menu Eksploratora z wybranymi kontenerami obiektów BLOB.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
