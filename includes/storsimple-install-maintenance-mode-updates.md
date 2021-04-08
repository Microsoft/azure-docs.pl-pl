---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183266"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować aktualizacje trybu konserwacji za pośrednictwem program Windows PowerShell dla usługi StorSimple
1. Jeśli jeszcze tego nie zrobiono, uzyskaj dostęp do konsoli szeregowej urządzenia i wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. 
2. Wpisz hasło. Domyślne hasło to **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability` 
4. Otrzymasz powiadomienie o dostępności aktualizacji oraz o tym, czy aktualizacje są zakłócone, czy nie zakłócają działania. Aby zastosować aktualizacje zakłócające, należy przełączyć urządzenie w tryb konserwacji. Zobacz [krok 2. Wprowadź tryb konserwacji](../articles/storsimple/storsimple-update-device.md#step2) , aby uzyskać instrukcje.
5. Gdy urządzenie jest w trybie konserwacji, w wierszu polecenia wpisz: `Start-HcsUpdate`
6. Pojawi się monit o potwierdzenie. Po potwierdzeniu aktualizacji zostaną one zainstalowane na kontrolerze, do którego aktualnie uzyskuje się dostęp. Po zainstalowaniu aktualizacji kontroler zostanie uruchomiony ponownie. 
7. Monitorowanie stanu aktualizacji. Wpisz:
   
    `Get-HcsUpdateStatus`
   
    Jeśli `RunInProgress` jest `True` , aktualizacja jest nadal w toku. Jeśli `RunInProgress` tak `False` , oznacza to, że aktualizacja została ukończona.  
8. Gdy aktualizacja jest zainstalowana na bieżącym kontrolerze, a została uruchomiona ponownie, nawiąż połączenie z drugim kontrolerem i wykonaj kroki od 1 do 6.
9. Po zaktualizowaniu obu kontrolerów należy wyjść z trybu konserwacji. Zobacz [krok 4. Wyjdź z trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step4) , aby uzyskać instrukcje.

