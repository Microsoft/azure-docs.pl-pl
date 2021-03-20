---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183264"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne aktualizacje za pośrednictwem program Windows PowerShell dla usługi StorSimple
1. Otwórz konsolę szeregową urządzenia i wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Wpisz hasło. Domyślne hasło to *Password1*. 
2. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability`
   
    Otrzymasz powiadomienie o dostępności aktualizacji oraz o tym, czy aktualizacje są zakłócone, czy nie zakłócają działania.
3. W wierszu polecenia wpisz polecenie:
   
     `Start-HcsUpdate`
   
    Rozpocznie się proces aktualizacji.

> [!IMPORTANT]
> * To polecenie dotyczy tylko zwykłych aktualizacji. To polecenie jest uruchamiane tylko na jednym kontrolerze, ale oba kontrolery zostaną zaktualizowane. 
> * Podczas procesu aktualizacji można zauważyć tryb failover kontrolera; Jednak w trybie failover nie wpłynie to na dostępność systemu ani działanie.
> 
> 

