---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183258"
---
#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**.
2. Wpisz hasło. Domyślne hasło to **Password1**.
3. W wierszu polecenia wpisz
   
     `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący o tym, że tryb konserwacji przerwie wszystkie żądania we/wy i łączy się z klasycznym portalem Azure i zostanie wyświetlony monit o potwierdzenie. Wpisz **Y** , aby przejść do trybu konserwacji.
   
    Oba kontrolery zostaną uruchomione ponownie. Po ponownym uruchomieniu zostanie wyświetlony kolejny komunikat informujący o tym, że urządzenie jest w trybie konserwacji.

