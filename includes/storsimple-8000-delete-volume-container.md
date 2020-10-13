---
title: plik dołączany
description: plik dołączany
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 30bbd06e36ed1e03caa391165a8abc275f1899a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102867"
---
Aby usunąć kontener woluminów, należy
 - Usuń woluminy w kontenerze woluminów. Jeśli kontener woluminów zawiera skojarzone woluminy, należy najpierw przełączyć te woluminy w tryb offline. Wykonaj kroki opisane w sekcji [Zrób wolumin w trybie offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Gdy woluminy są w trybie offline, można je usunąć. 
 - Usuń skojarzone zasady tworzenia kopii zapasowych i migawki w chmurze. Sprawdź, czy kontener woluminów ma skojarzone zasady tworzenia kopii zapasowych i migawki w chmurze. Jeśli tak, [Usuń zasady tworzenia kopii zapasowej](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Spowoduje to również usunięcie migawek chmurowych. 
 
Gdy kontener woluminów nie ma skojarzonych woluminów, zasad kopii zapasowych i migawek w chmurze, można go usunąć. Aby usunąć kontener woluminów, wykonaj poniższą procedurę.

#### <a name="to-delete-a-volume-container"></a>Aby usunąć kontener woluminów
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Wybierz i kliknij urządzenie, a następnie przejdź do pozycji **ustawienia > Zarządzanie kontenerami woluminów >**.

    ![Blok kontenerów woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Z listy tabelarycznej kontenerów woluminów wybierz kontener woluminów do usunięcia, kliknij prawym przyciskiem myszy pozycję.. **.** , a następnie wybierz polecenie **Usuń**.

    ![Usuń kontener woluminów](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Jeśli kontener woluminów nie ma skojarzonych woluminów, zasad kopii zapasowych i migawek w chmurze, można go usunąć. Po wyświetleniu monitu o potwierdzenie Sprawdź i zaznacz pole wyboru wskazujące wpływ usunięcia kontenera woluminów. Kliknij przycisk **Usuń** , aby usunąć kontener woluminów.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Lista kontenerów woluminów jest aktualizowana w celu odzwierciedlenia usuniętego kontenera woluminów.

![Zrzut ekranu strony kontenera woluminów. Tabelaryczna lista kontenerów woluminów nie zawiera już usuniętego kontenera.](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


