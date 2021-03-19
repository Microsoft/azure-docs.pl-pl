---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183427"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje trybu konserwacji za pośrednictwem program Windows PowerShell dla usługi StorSimple

Po zastosowaniu aktualizacji trybu konserwacji do urządzenia StorSimple wszystkie żądania we/wy są wstrzymane. Usługi, takie jak nietrwały dostęp losowy (NVRAM) lub usługa klastrowania, są zatrzymane. Oba kontrolery są ponownie uruchamiany po wprowadzeniu lub wyjściu z tego trybu. Po zamknięciu tego trybu wszystkie usługi są wznawiane i są w dobrej kondycji. (Może to potrwać kilka minut).

> [!IMPORTANT]
> * Przed wprowadzeniem trybu konserwacji upewnij się, że oba kontrolery urządzeń są w dobrej kondycji w Azure Portal. Jeśli kontroler nie jest w dobrej kondycji, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków.
> * W trybie konserwacji należy najpierw zaktualizować jeden kontroler, a następnie drugi kontroler.

1. Użyj wypełnień, aby nawiązać połączenie z konsolą szeregową. Postępuj zgodnie ze szczegółowymi instrukcjami w części [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). W wierszy polecenia naciśnij klawisz **Enter**. Wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**.

2. Aby umieścić kontroler w trybie konserwacji, wpisz:
    
    `Enter-HcsMaintenanceMode`

    Oba kontrolery są ponownie uruchamiany w trybie konserwacji.

3. Zainstaluj aktualizacje trybu konserwacji. Wpisz:

    `Start-HcsUpdate`

    Zostanie wyświetlony monit o potwierdzenie. Po potwierdzeniu aktualizacji są one instalowane na kontrolerze, do którego aktualnie uzyskuje się dostęp. Po zainstalowaniu aktualizacji kontroler zostanie ponownie uruchomiony.

4. Monitorowanie stanu aktualizacji. Zaloguj się do kontrolera równorzędnego, ponieważ bieżący kontroler aktualizuje się i nie może przetwarzać żadnych innych poleceń. Wpisz:

    `Get-HcsUpdateStatus`

    Jeśli `RunInProgress` jest `True` , aktualizacja jest nadal w toku. Jeśli `RunInProgress` tak `False` , oznacza to, że aktualizacja została ukończona.

5. Po pomyślnym zastosowaniu aktualizacji oprogramowania układowego dysku i ponownym uruchomieniu zaktualizowanego kontrolera Sprawdź wersję oprogramowania układowego dysku. Na stronie zaktualizowany kontroler wpisz:

    `Get-HcsFirmwareVersion`
   
    Oczekiwane wersje oprogramowania układowego dysku to:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Wyjdź z trybu konserwacji. Dla każdego kontrolera urządzenia wpisz następujące polecenie:

    `Exit-HcsMaintenanceMode`

    Po wyjściu z trybu konserwacji kontrolery zostaną ponownie uruchomione.

7. Wróć do witryny Azure Portal. W portalu może nie być widoczne, że zainstalowano aktualizacje trybu konserwacji przez 24 godziny.