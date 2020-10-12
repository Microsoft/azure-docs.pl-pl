---
title: Blok podsumowania usługi StorSimple Virtual Array | Microsoft Docs
description: Opisuje blok podsumowania usługi StorSimple Menedżer urządzeń i wyjaśnia, jak go używać do monitorowania kondycji macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514522"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Korzystanie z bloku podsumowania usługi StorSimple Menedżer urządzeń połączony z StorSimple wirtualną macierzy
## <a name="overview"></a>Omówienie
Blok podsumowania usługi dla StorSimple Menedżer urządzeń zawiera widok podsumowania macierzy wirtualnych StorSimple (znany również jako StorSimple lokalne urządzenia wirtualne lub urządzenia wirtualne), które są połączone z usługą, oraz wyróżni te, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania usługi, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

![Pulpit nawigacyjny usług](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Polecenia zarządzania i podstawowe informacje
W bloku podsumowania StorSimple są wyświetlane opcje zarządzania usługą StorSimple Menedżer urządzeń oraz wirtualnymi tablicami zarejestrowanymi dla tej usługi. Zobaczysz polecenia zarządzania w górnej części bloku i po lewej stronie.

Te opcje służą do wykonywania różnych operacji, takich jak dodawanie udziałów lub woluminów lub monitorowanie różnych zadań uruchomionych w macierzach wirtualnych.

Obszar podstawy przechwytuje niektóre ważne właściwości, takie jak, grupę zasobów, lokalizację i subskrypcję, w ramach której utworzono Menedżer urządzeń StorSimple.

## <a name="storsimple-device-manager-service-summary"></a>Podsumowanie usługi StorSimple Menedżer urządzeń
* Kafelek **alerty** zawiera migawkę wszystkich aktywnych alertów na wszystkich urządzeniach wirtualnych pogrupowanych według ważności alertu. Kliknięcie kafelka powoduje otwarcie bloku **alerty** , gdzie można kliknąć indywidualny alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszystkie zalecane akcje. Możesz również wyczyścić alert, jeśli problem został rozwiązany.
* Na kafelku **pojemność** są wyświetlane podstawowe magazyny, które są udostępniane i pozostałe dla wszystkich urządzeń wirtualnych względem całkowitej ilości dostępnego miejsca na wszystkich urządzeniach wirtualnych. **Przygotowana odnosi się** do ilości miejsca przeznaczonego do użycia i przystosowanego do użytku, **pozostała** odnosi się do pozostałej pojemności, która może być obsługiwana przez wszystkie urządzenia wirtualne. **Pozostała pojemność warstwowa** to dostępna pojemność, która może zostać zainicjowana, w tym w chmurze, a **pozostała wartość jest** równa pozostałej pojemności na dyskach dołączonych do macierzy wirtualnych.
* Na wykresie **użycia** można zobaczyć odpowiednie metryki dla urządzeń wirtualnych. Można wyświetlić magazyn podstawowy używany na wszystkich urządzeniach wirtualnych, a także magazyn w chmurze zużyty przez urządzenia wirtualne w ciągu ostatnich 7 dni — domyślny okres. Użyj opcji **Edytuj** w prawym górnym rogu wykresu, aby wybrać inną skalę czasu.
* Kafelek **urządzenia** zawiera podsumowanie liczby tablic wirtualnych w StorSimple Menedżer urządzeń pogrupowane według stanu urządzenia. Kliknij ten kafelek, aby otworzyć blok listy **urządzeń** , a następnie kliknij pojedyncze urządzenie, aby przejść do szczegółów dotyczących urządzenia. Można również wykonywać działania specyficzne dla urządzenia z danego bloku podsumowania urządzenia. Aby uzyskać więcej informacji na temat bloku podsumowanie urządzenia, przejdź do [bloku podsumowanie urządzenia](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Wyświetlanie dzienników aktywności
Aby wyświetlić różne operacje wykonywane w ramach StorSimple Menedżer urządzeń, kliknij link **dzienniki aktywności** po lewej stronie bloku podsumowania usługi StorSimple. Spowoduje to przejście do bloku **dzienniki aktywności** , w którym można zobaczyć podsumowanie ostatnich wykonywanych operacji.

![Dzienniki aktywności](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak zarządzać wirtualną tablicą StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

