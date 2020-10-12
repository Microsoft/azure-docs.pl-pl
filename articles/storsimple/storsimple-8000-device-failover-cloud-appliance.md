---
title: Tryb failover i odzyskiwanie po awarii do urządzenia w chmurze StorSimple
description: Dowiedz się, jak przełączyć urządzenie fizyczne z serii StorSimple 8000 do urządzenia w chmurze.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774464"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Przechodzenie w tryb failover do urządzenia w chmurze StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do przejścia w tryb failover urządzenia fizycznego z serii StorSimple 8000 do StorSimpleego urządzenia w chmurze, jeśli wystąpi awaria. StorSimple korzysta z funkcji przełączania do trybu failover urządzenia do migrowania danych z źródłowego urządzenia fizycznego w centrum danych do urządzenia w chmurze działającego na platformie Azure. Wskazówki zawarte w tym samouczku dotyczą urządzeń fizycznych z serii StorSimple 8000 i urządzeń w chmurze z uruchomionymi wersjami oprogramowania Update 3 i nowszymi.

Aby dowiedzieć się więcej o przełączaniu do trybu failover urządzeń i sposobie ich użycia w celu odzyskania po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przełączyć urządzenie fizyczne StorSimple na inne urządzenie fizyczne, przejdź do trybu [failover na urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przełączyć urządzenie w tryb failover, przejdź do trybu [failover na tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zawarto przegląd zagadnień związanych z trybem failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących trybu failover urządzeń](storsimple-8000-device-failover-disaster-recovery.md).

- Przed uruchomieniem tej procedury należy utworzyć i skonfigurować urządzenie w chmurze StorSimple. Jeśli uruchomiona jest wersja Update 3 lub nowsza, należy rozważyć użycie urządzenia w chmurze 8020 na potrzeby odzyskiwania po awarii. Model 8020 ma 64 TB i używa Premium Storage. Aby uzyskać więcej informacji, przejdź do [wdrażania urządzenia w chmurze StorSimple i zarządzania nim](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Kroki umożliwiające przełączenie w tryb failover do urządzenia w chmurze

Wykonaj następujące kroki, aby przywrócić urządzenie do docelowego urządzenia w chmurze StorSimple.

1.  Sprawdź, czy kontener woluminów do przełączenia w tryb failover ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do obszaru [Tworzenie kopii zapasowych za pomocą usługi StorSimple Menedżer urządzeń](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **urządzenia** przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Wybierz i kliknij urządzenie źródłowe. Urządzenie źródłowe ma kontenery woluminów do przełączenia w tryb failover. Przejdź do pozycji **ustawienia > kontenery woluminów**.

    ![Wybierz urządzenie 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Wybierz kontener woluminów, który chcesz przełączyć do trybu failover na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Przełącz do trybu offline** , aby przełączyć wolumin w tryb offline.

    ![Wybierz urządzenie 3](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Powtórz ten proces dla wszystkich woluminów w kontenerze woluminów.

     ![Wybierz urządzenie 4](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które chcesz przełączyć do trybu failover na innym urządzeniu.

7. Wróć do bloku **urządzenia** . Na pasku poleceń kliknij pozycję **tryb failover**.

    ![Kliknij pozycję Tryb failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. W bloku **tryb failover** wykonaj następujące czynności:
   
    1. Kliknij pozycję **Źródło**. Wybierz kontenery woluminów do przełączenia w tryb failover. **Wyświetlane są tylko kontenery woluminów ze skojarzonymi migawkami w chmurze i woluminami w trybie offline.**
        ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kliknij pozycję **cel**. Wybierz docelowe urządzenie w chmurze z listy rozwijanej dostępnych urządzeń. **Na liście są wyświetlane tylko urządzenia, które mają wystarczającą pojemność do obsługi kontenerów woluminów źródłowych.**

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Sprawdź ustawienia trybu failover w obszarze **Podsumowanie** i zaznacz pole wyboru wskazujące, że woluminy w wybranych kontenerach woluminów są w trybie offline. 

        ![Przegląd ustawień trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Tworzone jest zadanie trybu failover. Aby monitorować zadanie trybu failover, kliknij powiadomienie o zadaniu.

    ![Monitorowanie zadania trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Po zakończeniu pracy w trybie failover Wróć do bloku **urządzenia** .

    1. Wybierz urządzenie, które zostało użyte jako cel dla trybu failover.

       ![Wybierz urządzenie 5](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kliknij pozycję **kontenery woluminów**. Wszystkie kontenery woluminów oraz woluminy ze starego urządzenia powinny być wymienione na liście.

       Jeśli kontener woluminów do przełączenia w tryb failover ma woluminy przypięte lokalnie, te woluminy są przenoszone w tryb failover jako woluminy warstwowe. Woluminy przypięte lokalnie nie są obsługiwane na urządzeniu w chmurze StorSimple.

       ![Wyświetl kontenery woluminów docelowych](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Następne kroki

* Po przejściu w tryb failover może być konieczne [Dezaktywowanie lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Aby uzyskać informacje o sposobach korzystania z usługi StorSimple Menedżer urządzeń, przejdź do pozycji [Korzystanie z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

