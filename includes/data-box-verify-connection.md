---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183898"
---
Wykonaj poniższe czynności, aby nawiązać i zweryfikować połączenie z kontem magazynu.

1. W Eksploratorze usługi Storage otwórz okno dialogowe **Połącz z usługą Azure Storage**. W oknie dialogowym **Połącz z usługą Azure Storage** wybierz pozycję **Użyj nazwy i klucza konta magazynu**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Wklej wartości dla swojego konta w polach **Nazwa konta** i **Klucz konta** (wartość 1 klucza ze strony **Połącz i skopiuj** w lokalnym internetowym interfejsie użytkownika). Wybierz opcję Domena punktów końcowych magazynu w polu **Inne (podaj poniżej)**, a następnie podaj punkt końcowy usługi obiektów blob, jak pokazano poniżej. Opcję **Użyj protokołu HTTP** zaznacz tylko wtedy, gdy przesyłasz za pośrednictwem protokołu *http*. Jeśli używasz protokołu *https*, nie zaznaczaj tej opcji. Wybierz opcję **Dalej**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Sprawdź informacje podane w oknie dialogowym **Podsumowanie połączenia**. Wybierz pozycję **Połącz**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Pomyślnie dodane konto jest wyświetlane w lewym okienku Eksploratora usługi Storage z wartościami (Zewnętrzne, Inne) dołączonymi do jego nazwy. Kliknij pozycję **Kontenery obiektów blob**, aby wyświetlić kontener.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
