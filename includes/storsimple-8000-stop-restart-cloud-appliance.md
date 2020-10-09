---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183289"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Aby zatrzymać i uruchomić urządzenie w chmurze

1. Aby zatrzymać urządzenie w chmurze, przejdź do maszyny wirtualnej dla swojego urządzenia w chmurze.
    ![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Na pasku polecenia kliknij pozycję **Stop**.

    ![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Po zatrzymaniu maszyny wirtualnej następuje cofnięcie jej przydziału. Gdy urządzenie w chmurze jest zatrzymywane, jego stan to **Cofanie przydziału**. Po zatrzymaniu urządzenia w chmurze jego stan to **Zatrzymane (cofnięty przydział)**.

    ![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Po zatrzymaniu maszyny wirtualnej kliknij przycisk **Uruchom** (przycisk staje się dostępny), aby uruchomić maszynę wirtualną. Po uruchomieniu urządzenia w chmurze jego stan to **Uruchomione**.

    ![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Użyj poniższych poleceń cmdlet do zatrzymywania i uruchamiania urządzenia w chmurze.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Aby uruchomić ponownie urządzenie w chmurze

Aby uruchomić ponownie urządzenie w chmurze, przejdź do maszyny wirtualnej dla swojego urządzenia w chmurze. Na pasku poleceń kliknij pozycję **Uruchom ponownie**. Po wyświetleniu monitu potwierdź ponowne uruchomienie. Gdy urządzenie w chmurze jest gotowe do użycia, jego stan to **Uruchomione**.

![Maszyna wirtualna urządzenia StorSimple w chmurze](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Użyj poniższego polecenia cmdlet, aby ponownie uruchomić urządzenie w chmurze.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

