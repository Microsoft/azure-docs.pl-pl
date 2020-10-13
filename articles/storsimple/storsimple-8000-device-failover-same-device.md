---
title: Samouczek — przechodzenie w tryb failover urządzenia fizycznego StorSimple na to samo urządzenie
description: Zapoznaj się z czynnościami wymaganymi do przełączenia w tryb failover urządzenia fizycznego z serii StorSimple 8000 do samego siebie, jeśli wystąpi awaria.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184333"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Przechodzenie do trybu failover urządzenia fizycznego StorSimple na tym samym urządzeniu

## <a name="overview"></a>Omówienie

W tym samouczku opisano kroki wymagane do przejścia w tryb failover urządzenia fizycznego z serii StorSimple 8000 do samego siebie w przypadku awarii. StorSimple używa funkcji przełączania do trybu failover urządzenia do migrowania danych z źródłowego urządzenia fizycznego w centrum danych do innego urządzenia fizycznego. Wskazówki zawarte w tym samouczku odnoszą się do urządzeń fizycznych z serii StorSimple 8000 z wersjami oprogramowania Update 3 i nowszymi.

Aby dowiedzieć się więcej o przełączaniu do trybu failover urządzeń i sposobie ich użycia w celu odzyskania po awarii, przejdź do [trybu failover i odzyskiwania po awarii dla urządzeń z serii StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Aby przełączyć urządzenie fizyczne w tryb failover na inne urządzenie fizyczne, przejdź do trybu [failover na tym samym urządzeniu fizycznym StorSimple](storsimple-8000-device-failover-physical-device.md). Aby przełączyć urządzenie fizyczne StorSimple do trybu failover w urządzeniu w chmurze StorSimple, przejdź do obszaru przełączenia w tryb [failover na urządzenie w chmurze StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zawarto przegląd zagadnień związanych z trybem failover urządzenia. Aby uzyskać więcej informacji, przejdź do [typowych zagadnień dotyczących trybu failover urządzeń](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Procedura przełączenia w tryb failover na to samo urządzenie

Wykonaj poniższe czynności, jeśli chcesz przełączyć się do trybu failover na tym samym urządzeniu.

1. Utwórz migawki w chmurze dla wszystkich woluminów na urządzeniu. Aby uzyskać więcej informacji, przejdź do obszaru [Tworzenie kopii zapasowych za pomocą usługi StorSimple Menedżer urządzeń](storsimple-8000-manage-backup-policies-u2.md).
2. Zresetuj urządzenie do domyślnych ustawień fabrycznych. Postępuj zgodnie ze szczegółowymi instrukcjami w temacie [jak zresetować urządzenie StorSimple do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Przejdź do usługi StorSimple Menedżer urządzeń, a następnie wybierz pozycję **urządzenia**. W bloku **urządzenia** stare urządzenie powinno być wyświetlane jako w **trybie offline**.

    ![Urządzenie źródłowe w trybie offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Skonfiguruj urządzenie i zarejestruj je ponownie za pomocą usługi StorSimple Menedżer urządzeń. Nowo zarejestrowane urządzenie powinno być widoczne jako **gotowe do skonfigurowania**. Nazwa urządzenia dla nowego urządzenia jest taka sama jak na starym urządzeniu, ale dołączona z cyfrą, aby wskazać, że urządzenie zostało zresetowane do domyślnych ustawień fabrycznych i zarejestrowane ponownie.

    ![Nowo zarejestrowane urządzenie gotowe do skonfigurowania](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Na nowym urządzeniu Ukończ konfigurację urządzenia. Aby uzyskać więcej informacji, przejdź do [kroku 4: kończenie minimalnej konfiguracji urządzenia](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). W bloku **urządzenia** stan urządzenia zmieni się na **online**.

   > [!IMPORTANT]
   > **Najpierw należy wykonać minimalną konfigurację lub błąd odzyskiwania po awarii.**

    ![Nowo zarejestrowane urządzenie w trybie online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Wybierz stare urządzenie (stan offline) i na pasku poleceń kliknij pozycję **tryb failover**. W bloku **tryb failover** wybierz pozycję stare urządzenie jako źródło i określ urządzenie docelowe jako nowo zarejestrowane urządzenie.

    ![Podsumowanie trybu failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Aby uzyskać szczegółowe instrukcje, zobacz Przechodzenie do trybu failover na innym urządzeniu fizycznym.

7. Tworzone jest zadanie przywracania urządzenia, które można monitorować w bloku **zadania** .

8. Po pomyślnym zakończeniu zadania uzyskaj dostęp do nowego urządzenia i przejdź do bloku **kontenery woluminów** . Sprawdź, czy wszystkie kontenery woluminów ze starego urządzenia zostały zmigrowane do nowego urządzenia.

   ![Zmigrowane kontenery woluminów](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Po zakończeniu pracy w trybie failover można dezaktywować i usuwać stare urządzenie z portalu. Wybierz stare urządzenie (offline), kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Dezaktywuj**. Gdy urządzenie zostanie zdezaktywowane, stan urządzenia zostanie zaktualizowany.

     ![Zdezaktywowano urządzenie źródłowe](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Wybierz dezaktywowane urządzenie, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Usuń**. Spowoduje to usunięcie urządzenia z listy urządzeń.

    ![Usunięto urządzenie źródłowe](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Następne kroki

* Po przejściu w tryb failover może być konieczne [Dezaktywowanie lub usunięcie urządzenia StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Aby uzyskać informacje o sposobach korzystania z usługi StorSimple Menedżer urządzeń, przejdź do pozycji [Korzystanie z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

