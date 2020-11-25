---
title: Używanie podsumowania urządzenia w usłudze StorSimple Menedżer urządzeń
description: Zawiera opis podsumowania urządzeń usługi StorSimple Menedżer urządzeń i sposobu ich używania do wyświetlania metryk magazynu i podłączonych inicjatorów oraz znajdowania numeru seryjnego i nazwy IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017155"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Korzystanie z podsumowania urządzenia w usłudze StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie
Blok podsumowania urządzenia StorSimple zawiera przegląd informacji dotyczących określonego urządzenia StorSimple, w przeciwieństwie do bloku podsumowania usługi, który zawiera informacje o wszystkich urządzeniach uwzględnionych w rozwiązaniu Microsoft Azure StorSimple.

Blok podsumowanie urządzenia zawiera widok podsumowania urządzenia z serii StorSimple 8000, które jest zarejestrowane w danym StorSimple Menedżer urządzeń, oraz wyróżnienia tych problemów dotyczących urządzeń, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania urządzenia, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

W bloku podsumowanie urządzenia są wyświetlane następujące informacje:

![Blok podsumowania urządzenia](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Pasek poleceń zarządzania

W bloku urządzenia StorSimple widoczne są opcje zarządzania urządzeniem StorSimple. Zobaczysz polecenia zarządzania w górnej części bloku i po lewej stronie. Za pomocą tych opcji można dodawać udziały lub woluminy albo aktualizować lub przełączać urządzenia do trybu failover.

![Pasek poleceń zarządzania](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Podstawy

Obszar podstawy przechwytuje niektóre ważne właściwości, takie jak stan, model, nazwa IQN docelowej i wersja oprogramowania. 

![Podstawowe informacje o urządzeniu](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorowanie

* Kafelek **alerty** zawiera migawkę wszystkich aktywnych alertów dla urządzenia pogrupowanych według ważności alertu.

    ![Kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kliknij kafelek, aby otworzyć blok **alerty** , a następnie kliknij indywidualny alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszystkie zalecane akcje. Możesz również wyczyścić alert, jeśli problem został rozwiązany.

    ![Kliknij kafelek alertu](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Kafelek **stan i kondycja** zawiera szczegółowe informacje o kondycji składników sprzętowych urządzenia, w tym o stanie urządzenia. Stan urządzenia może być w trybie offline, w trybie online, dezaktywowany lub gotowy do skonfigurowania.

    ![Kafelek stan i kondycja](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Kafelek **woluminy** zawiera podsumowanie liczby woluminów w urządzeniu pogrupowanych według stanu.

    ![Kafelek woluminy](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kliknij kafelek, aby otworzyć blok listy **woluminów** , a następnie kliknij pojedynczy wolumin, aby wyświetlić lub zmodyfikować jego właściwości.
    
    ![Kliknij kafelek woluminy](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Aby uzyskać więcej informacji, zobacz jak [zarządzać woluminami](storsimple-8000-manage-volumes-u2.md).

* Na wykresie **użycia** można wyświetlić magazyn podstawowy używany na urządzeniu i magazyn w chmurze zużyty w ciągu ostatnich 7 dni — domyślny okres.

     ![Kafelek użycie](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Aby wybrać inną skalę czasu, użyj opcji **Edytuj** w prawym górnym rogu wykresu.

     ![Edytuj wykres użycia](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Na tym wykresie można wyświetlić metryki całkowitego magazynu podstawowego (ilość danych zapisywana przez hosty na urządzenie) oraz łączny magazyn w chmurze zużyty przez urządzenie w danym okresie czasu.
  
     W tym kontekście *magazyn podstawowy* odnosi się do łącznej ilości danych zapisanych przez hosta i może być podzielony według typu woluminu: *podstawowy magazyn warstwowy* obejmuje zarówno dane przechowywane lokalnie, jak i dane warstwowe w chmurze. *Magazyn podstawowy lokalnie przypięty* obejmuje tylko dane przechowywane lokalnie. *Magazyn w chmurze*, z drugiej strony, to pomiar całkowitej ilości danych przechowywanych w chmurze. Ten magazyn zawiera dane warstwowe i kopie zapasowe. Dane przechowywane w chmurze są deduplikowane i kompresowane, natomiast podstawowy magazyn wskazuje ilość miejsca używanego do deduplikowania i kompresowania danych. (Można porównać te dwie liczby, aby uzyskać pomysł o szybkość kompresji). W przypadku magazynu podstawowego i w chmurze podane kwoty są zależne od skonfigurowanej częstotliwości śledzenia. Na przykład jeśli zostanie wybrana częstotliwość z jedną tygodnią, wykres będzie przedstawiał dane dla każdego dnia w poprzednim tygodniu.

     Aby sprawdzić ilość magazynu w chmurze zużytego w czasie, wybierz opcję **użyty magazyn w chmurze** . Aby wyświetlić łączny magazyn, który został zapisany przez hosta, wybierz **używany magazyn warstwowy** i **podstawowy, PRZYpięty lokalny** magazyn. 
     Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi StorSimple Menedżer urządzeń do monitorowania urządzenia StorSimple](./storsimple-8000-monitor-device.md).


* Kafelek **pojemność** przedstawia magazyn podstawowy, który jest inicjowany i pozostały w całym urządzeniu względem łącznego magazynu dostępnego dla tego samego. **Przygotowana odnosi się** do ilości miejsca przeznaczonego do użycia i przystosowanego do użytku, **pozostała** odnosi się do pozostałej pojemności, która może zostać zainicjowana na tym urządzeniu. 

    ![Kafelek użycie 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kliknij ten kafelek, aby zobaczyć, jak pojemność jest obsługiwana w warstwach i przypiętych lokalnie. **Pozostała pojemność warstwowa** to dostępna pojemność, która może zostać zainicjowana, w tym w chmurze, a **pozostała wartość jest** równa pozostałej pojemności na dyskach dołączonych do tego urządzenia.

    ![Kliknij wykres użycia](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [bloku podsumowania usługi StorSimple](storsimple-8000-service-dashboard.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).