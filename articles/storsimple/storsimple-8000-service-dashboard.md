---
title: Używanie bloku podsumowania usługi dla urządzenia z serii StorSimple 8000
description: Opisuje blok podsumowania usługi StorSimple i wyjaśnia, jak go używać do monitorowania kondycji rozwiązania StorSimple.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514952"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Korzystanie z bloku podsumowania usługi dla urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Blok podsumowania usługi StorSimple Device Manager zawiera widok podsumowania wszystkich urządzeń podłączonych do usługi StorSimple Device Manager, co umożliwia wyróżnienie tych urządzeń, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania usługi, wyjaśniono zawartość i funkcję pulpitu nawigacyjnego oraz opisano zadania, które można wykonać na tej stronie.

![Podsumowanie usługi](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Polecenia zarządzania

W bloku podsumowanie usługi StorSimple widoczne są opcje zarządzania usługą StorSimple Device Manager i urządzeniami serii StorSimple 8000 zarejestrowanymi w tej usłudze. Zobaczysz polecenia zarządzania w górnej części bloku i po lewej stronie.

![Pasek poleceń](./media/storsimple-8000-service-dashboard/service-summary2.png)

Te opcje służą do wykonywania różnych operacji, takich jak dodawanie udziałów lub woluminów lub monitorowanie różnych zadań uruchomionych na urządzeniach StorSimple.


## <a name="essentials"></a>Essentials

Obszar podstawy przechwytuje niektóre ważne właściwości, takie jak, grupę zasobów, lokalizację i subskrypcję, w ramach której utworzono Device Manager StorSimple.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi StorSimple Device Manager

* Kafelek **alerty** zawiera migawkę wszystkich aktywnych alertów na wszystkich urządzeniach pogrupowanych według ważności alertu.

    ![Kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Kliknięcie kafelka powoduje otwarcie bloku **alerty** , gdzie można kliknąć indywidualny alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszystkie zalecane akcje. Możesz również wyczyścić alert, jeśli problem został rozwiązany.

    ![Kliknij kafelek alerty](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Na kafelku **pojemność** są wyświetlane podstawowe magazyny, które są udostępniane i pozostają na wszystkich urządzeniach względem całkowitej ilości dostępnego miejsca na wszystkich urządzeniach. **Przygotowana odnosi się** do ilości miejsca do magazynowania przygotowanego i przystosowanego do użycia, **pozostała** odnosi się do pozostałej pojemności, która może zostać zainicjowana na wszystkich urządzeniach.

    ![Kafelek pojemności](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Pozostała pojemność warstwowa** to dostępna pojemność, która może zostać zainicjowana, w tym w chmurze, a **pozostała wartość jest** równa pozostałej pojemności na dyskach dołączonych do urządzeń serii StorSimple 8000.


* Na wykresie **użycia** można zobaczyć odpowiednie metryki dla urządzeń. Można wyświetlić magazyn podstawowy używany na wszystkich urządzeniach i magazyn w chmurze zużyty przez urządzenia w ciągu ostatnich 7 dni — domyślny okres. 

    ![Kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Aby wybrać inną skalę czasu, użyj opcji **Edytuj** w prawym górnym rogu wykresu.

     ![Kliknij kafelek użycie](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Eksportuj dane wykresu](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Kafelek **urządzenia** zawiera podsumowanie liczby urządzeń z serii StorSimple 8000 w StorSimple Device Manager pogrupowanych według stanu urządzenia. 

    ![Kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kliknij ten kafelek, aby otworzyć blok listy **urządzeń** , a następnie kliknij pojedyncze urządzenie, aby przejść do szczegółów dotyczących urządzenia. Można również wykonywać działania specyficzne dla urządzenia z danego bloku podsumowania urządzenia. Aby uzyskać więcej informacji na temat bloku podsumowanie urządzenia, przejdź do [bloku podsumowanie urządzenia](storsimple-8000-device-dashboard.md).

    ![Kliknij kafelek urządzenia](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności

Aby wyświetlić różne operacje wykonywane w ramach StorSimple Device Manager, kliknij link **dzienniki aktywności** po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do bloku **dzienniki aktywności** , w którym można zobaczyć podsumowanie ostatnich wykonywanych operacji.

![Dzienniki aktywności](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [używania usługi StorSimple Device Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

