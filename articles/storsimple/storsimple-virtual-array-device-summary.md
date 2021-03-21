---
title: Blok podsumowania urządzenia macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje blok podsumowania urządzenia StorSimple Device Manager i wyjaśnia, jak go używać do monitorowania kondycji macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005881"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Korzystanie z bloku podsumowanie urządzenia StorSimple Device Manager połączony z StorSimple wirtualną macierzy

## <a name="overview"></a>Omówienie

Blok StorSimple Device Manager Device zawiera widok podsumowania macierzy wirtualnej StorSimple, która jest zarejestrowana w danym StorSimple Device Manager, z uwzględnieniem problemów z urządzeniem, które wymagają uwagi administratora systemu. W tym samouczku przedstawiono blok podsumowania urządzenia, wyjaśniono zawartość i funkcję oraz opisano zadania, które można wykonać z tego bloku.

W bloku podsumowanie urządzenia są wyświetlane następujące informacje:

![Pulpit nawigacyjny urządzenia](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Zarządzanie

W bloku urządzenia StorSimple widoczne są opcje zarządzania urządzeniem StorSimple. Zobaczysz polecenia zarządzania w górnej części bloku i po lewej stronie. Użyj tych opcji, aby dodać udziały lub woluminy, lub zaktualizuj lub przełączać macierz wirtualną do trybu failover.

Obszar podstawy przechwytuje niektóre ważne właściwości, takie jak stan, model, wersja oprogramowania, a także link do **internetowego interfejsu użytkownika** tablicy. W przypadku sieci wewnętrznej można bezpośrednio uruchomić [lokalny interfejs użytkownika sieci Web](storsimple-ova-web-ui-admin.md) w celu administrowania wirtualną tablicą.

![Podstawowe informacje o urządzeniu](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Podsumowanie urządzenia StorSimple

* Kafelek **alerty** zawiera migawkę wszystkich aktywnych alertów dla macierzy wirtualnej, pogrupowanych według ważności alertu. Kliknij kafelek, aby otworzyć blok **alerty** , a następnie kliknij indywidualny alert, aby wyświetlić dodatkowe szczegóły dotyczące tego alertu, w tym wszystkie zalecane akcje. Możesz również wyczyścić alert, jeśli problem został rozwiązany.

* Kafelek **pojemność** przedstawia magazyn podstawowy, który jest zainicjowany i pozostały w całym urządzeniu wirtualnym względem łącznego magazynu dostępnego dla tego samego. **Przygotowana odnosi się** do ilości miejsca przeznaczonego do użycia i przystosowanego do użytku, **pozostała** odnosi się do pozostałej pojemności, która może zostać zainicjowana na tym urządzeniu. **Pozostała pojemność warstwowa** to dostępna pojemność, która może zostać zainicjowana, w tym w chmurze, a **pozostała** wartość jest równa pozostałej pojemności na dyskach dołączonych do tej macierzy wirtualnej.

* Na wykresie **użycia** można wyświetlić magazyn podstawowy używany w ramach macierzy wirtualnej oraz magazyn w chmurze zużyty w ciągu ostatnich 7 dni — domyślny okres. Użyj opcji **Edytuj** w prawym górnym rogu wykresu, aby wybrać inną skalę czasu.

* Kafelek **udziały** lub **woluminy** zawiera podsumowanie liczby udziałów lub woluminów w urządzeniu pogrupowanych według stanu. Kliknij kafelek, aby otworzyć blok listy **udziały**  lub **woluminy** , a następnie kliknij pojedynczy udział lub wolumin, aby wyświetlić lub zmodyfikować jego właściwości. Aby uzyskać więcej informacji, zobacz jak [zarządzać udziałami](storsimple-virtual-array-manage-shares.md) lub [zarządzać woluminami](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Następne kroki
Instrukcje:
- [Zarządzanie udziałami w macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Zarządzanie woluminami w macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-volumes.md)

