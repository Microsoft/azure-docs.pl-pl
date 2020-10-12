---
title: Opcje zarządzania w konsoli szeregowej platformy Azure | Microsoft Docs
description: Opcje zarządzania MASZYNami wirtualnymi dostępne w konsoli szeregowej platformy Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451199"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opcje napięcia dostępne w konsoli szeregowej platformy Azure

Konsola szeregowa Azure oferuje kilka zaawansowanych narzędzi do zarządzania zużyciem mocy na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. Te opcje zarządzania mocą mogą być mylące dla niektórych, więc jest to przegląd każdego narzędzia i jego zamierzonego użycia.

Funkcja konsoli szeregowej | Opis | Przypadek użycia
:----------------------|:------------|:---------
Uruchom ponownie maszynę wirtualną | Bezpieczne ponowne uruchomienie maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych. Ta operacja jest taka sama jak wywołanie funkcji restart dostępnej na stronie Przegląd. | W większości przypadków ta opcja powinna być pierwszym narzędziem próbującym ponownie uruchomić maszynę wirtualną. Połączenie z konsolą szeregową będzie miało krótkie przerwy i zostanie automatycznie wznowione zaraz po ponownym uruchomieniu maszyny wirtualnej.
Resetowanie maszyny wirtualnej | Wymuszony cykl energetyczny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przez platformę Azure. | Ta opcja służy do natychmiastowego ponownego uruchomienia systemu operacyjnego niezależnie od jego bieżącego stanu. Ponieważ ta operacja nie jest łagodna, istnieje ryzyko utraty lub uszkodzenia danych. Nie ma przerwy w połączeniu z konsolą szeregową, co może być przydatne do wczesnego wysyłania poleceń w czasie rozruchu (na przykład w celu GRUB na maszynie wirtualnej z systemem Linux lub w trybie awaryjnym w maszynie wirtualnej systemu Windows).
SysRq — ponowny rozruch (b) | Żądanie systemowe umożliwiające wymuszenie ponownego uruchomienia gościa. | Ta funkcja ma zastosowanie tylko do systemów operacyjnych Linux i wymaga [włączenia sysrq](./serial-console-nmi-sysrq.md#system-request-sysrq) w systemie operacyjnym. Jeśli system operacyjny został prawidłowo skonfigurowany do SysRq, to polecenie spowoduje ponowne uruchomienie systemu operacyjnego.
NMI (przerwanie z maską) | Polecenie przerwania, które zostanie dostarczone do systemu operacyjnego | Ta operacja jest dostępna zarówno dla maszyn wirtualnych z [systemem Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) , jak i [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) i wymaga włączenia NMI. Wysłanie elementu NMI zazwyczaj spowoduje awarię systemu operacyjnego. Można skonfigurować system operacyjny, aby utworzył plik zrzutu, a następnie uruchomić go ponownie po odebraniu NMI, co może być przydatne w przypadku debugowania niskiego poziomu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md)
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)