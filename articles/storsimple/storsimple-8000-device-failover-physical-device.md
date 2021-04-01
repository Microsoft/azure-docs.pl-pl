---
title: Tryb failover, odzyskiwanie po awarii na inne urządzenie z StorSimple 8000
description: Dowiedz się, jak przełączyć urządzenie fizyczne z serii StorSimple 8000 na inne urządzenie fizyczne.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017138"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Przechodzenie w tryb failover na urządzenie fizyczne z serii StorSimple 8000

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do przejścia w tryb failover urządzenia fizycznego z serii StorSimple 8000 na inne urządzenie fizyczne StorSimple, jeśli wystąpi awaria. StorSimple używa funkcji przełączania do trybu failover urządzenia do migrowania danych z źródłowego urządzenia fizycznego w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku odnoszą się do urządzeń fizycznych z serii StorSimple 8000 z wersjami oprogramowania Update 3 i nowszymi.

Aby dowiedzieć się więcej o przełączaniu do trybu failover urządzeń i sposobie ich użycia w celu odzyskania po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przełączyć urządzenie fizyczne StorSimple do trybu failover w urządzeniu w chmurze StorSimple, przejdź do obszaru przełączenia w tryb [failover na urządzenie w chmurze StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Aby przełączyć urządzenie fizyczne w tryb failover, przejdź do trybu [failover na tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zawarto przegląd zagadnień związanych z trybem failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących trybu failover urządzeń](storsimple-8000-device-failover-disaster-recovery.md).

- W centrum danych musi być wdrożone urządzenie fizyczne z serii StorSimple 8000. Na urządzeniu musi być uruchomiona wersja Update 3 lub nowsza. Aby uzyskać więcej informacji, przejdź do obszaru [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Kroki umożliwiające przełączenie w tryb failover na urządzenie fizyczne

Wykonaj następujące kroki, aby przywrócić urządzenie do docelowego urządzenia fizycznego.

1. Sprawdź, czy kontener woluminów do przełączenia w tryb failover ma skojarzone migawki w chmurze. Aby uzyskać więcej informacji, przejdź do obszaru [Tworzenie kopii zapasowych za pomocą usługi StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).
2. Przejdź do StorSimple Device Manager a następnie kliknij pozycję **urządzenia**. W bloku **urządzenia** przejdź do listy urządzeń połączonych z usługą.
    ![Wybierz urządzenie](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Wybierz i kliknij urządzenie źródłowe. Urządzenie źródłowe ma kontenery woluminów do przełączenia w tryb failover. Przejdź do pozycji **ustawienia > kontenery woluminów**.
4. Wybierz kontener woluminów, który chcesz przełączyć do trybu failover na innym urządzeniu. Kliknij kontener woluminów, aby wyświetlić listę woluminów w tym kontenerze. Wybierz wolumin, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Przełącz do trybu offline** , aby przełączyć wolumin w tryb offline. Powtórz ten proces dla wszystkich woluminów w kontenerze woluminów.
5. Powtórz poprzedni krok dla wszystkich kontenerów woluminów, które chcesz przełączyć do trybu failover na innym urządzeniu.
6. Wróć do bloku **urządzenia** . Na pasku poleceń kliknij pozycję **tryb failover**.
    ![Kliknij pozycję Tryb failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. W bloku **tryb failover** wykonaj następujące czynności:
   
   1. Kliknij pozycję **Źródło**. Zostaną wyświetlone kontenery woluminów z woluminami skojarzonymi z migawkami w chmurze. Tylko wyświetlane kontenery kwalifikują się do przejścia w tryb failover. Na liście kontenerów woluminów wybierz kontenery woluminów, które chcesz przełączyć do trybu failover. **Wyświetlane są tylko kontenery woluminów ze skojarzonymi migawkami w chmurze i woluminami w trybie offline.**

       ![Wybierz źródło](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kliknij pozycję **cel**. W przypadku kontenerów woluminów wybranych w poprzednim kroku wybierz urządzenie docelowe z listy rozwijanej dostępne urządzenia. Na liście są wyświetlane tylko urządzenia, które mają wystarczającą pojemność do obsługi kontenerów woluminów źródłowych.

        ![Wybieranie obiektu docelowego](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Na koniec Przejrzyj wszystkie ustawienia trybu failover w obszarze **Podsumowanie**. Po przejrzeniu ustawień zaznacz pole wyboru wskazujące, że woluminy w wybranych kontenerach woluminów są w trybie offline. Kliknij przycisk **OK**.

       ![Przegląd ustawień trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple tworzy zadanie trybu failover. Kliknij powiadomienie o zadaniu, aby monitorować zadanie trybu failover w bloku **zadania** .

    Jeśli kontener woluminów w trybie failover zawiera woluminy lokalne, w kontenerze zostaną wyświetlone pojedyncze zadania przywracania dla każdego woluminu lokalnego (nie dla woluminów warstwowych). Ukończenie tych zadań przywracania może zająć dużo czasu. Prawdopodobnie zadanie trybu failover może zostać wykonane wcześniej. Te woluminy będą mieć lokalne gwarancje tylko po zakończeniu zadań przywracania.

    ![Monitorowanie zadania trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Po zakończeniu pracy w trybie failover przejdź do bloku **urządzenia** .
   
   1. Wybierz urządzenie, które było używane jako urządzenie docelowe dla procesu przełączania do trybu failover.

       ![Wybierz urządzenie 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Przejdź do bloku **kontenery woluminów** . Wszystkie kontenery woluminów oraz woluminy ze starego urządzenia powinny być wymienione na liście.

       ![Wyświetl kontenery woluminów docelowych](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Następne kroki

* Po przejściu w tryb failover może być konieczne [Dezaktywowanie lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobach korzystania z usługi StorSimple Device Manager, przejdź do pozycji [Korzystanie z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

