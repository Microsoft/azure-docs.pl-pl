---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 3e4edc55834d322dede36e9f429f5af7a4a79846
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375840"
---
Wykonaj poniższe czynności, aby nawiązać i zweryfikować połączenie z kontem magazynu.

1. W Eksploratorze usługi Storage otwórz okno dialogowe **Połącz z usługą Azure Storage**. W oknie dialogowym **Połącz z usługą Azure Storage** wybierz pozycję **Użyj nazwy i klucza konta magazynu**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Wklej wartości dla swojego konta w polach **Nazwa konta** i **Klucz konta** (wartość 1 klucza ze strony **Połącz i skopiuj** w lokalnym internetowym interfejsie użytkownika). Wybierz opcję Domena punktów końcowych magazynu w polu **Inne (podaj poniżej)** , a następnie podaj punkt końcowy usługi obiektów blob, jak pokazano poniżej. Opcję **Użyj protokołu HTTP** zaznacz tylko wtedy, gdy przesyłasz za pośrednictwem protokołu *http*. Jeśli używasz protokołu *https* , nie zaznaczaj tej opcji. Wybierz pozycję **Dalej**.

    ![Pulpit nawigacyjny urządzenie Data Box 2](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Sprawdź informacje podane w oknie dialogowym **Podsumowanie połączenia**. Wybierz pozycję **Połącz**.

    ![Pulpit nawigacyjny urządzenie Data Box 3](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Pomyślnie dodane konto jest wyświetlane w lewym okienku Eksploratora usługi Storage z wartościami (Zewnętrzne, Inne) dołączonymi do jego nazwy. Kliknij pozycję **Kontenery obiektów blob** , aby wyświetlić kontener.

    ![Pulpit nawigacyjny urządzenie Data Box 4](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
