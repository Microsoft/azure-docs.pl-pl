---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183261"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować poprawki trybu konserwacji za pośrednictwem program Windows PowerShell dla usługi StorSimple
> [!IMPORTANT]
> W trybie konserwacji należy najpierw zastosować poprawkę na jednym kontrolerze, a następnie na drugim kontrolerze.
> 
> 

1. Umieść urządzenie w trybie konserwacji. Zobacz [krok 2. wprowadzanie trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step2) , aby uzyskać instrukcje dotyczące sposobu wprowadzania trybu konserwacji.
2. Aby zastosować poprawkę, wpisz:
   
     `Start-HcsHotfix` 
3. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawek.
4. Pojawi się monit o potwierdzenie. Wpisz **Y** , aby kontynuować instalację poprawki.
5. Po zastosowaniu poprawki na jednym kontrolerze Zaloguj się do drugiego kontrolera. Zastosuj poprawkę jak dla poprzedniego kontrolera.
6. Po zastosowaniu poprawek zamknij tryb konserwacji. Zobacz [krok 4. Wyjdź z trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step4) , aby uzyskać instrukcje.

