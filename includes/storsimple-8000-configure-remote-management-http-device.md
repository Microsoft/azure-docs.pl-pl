---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183292"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Aby skonfigurować zdalne zarządzanie w urządzeniu w chmurze

1. W usłudze Menedżer urządzeń StorSimple kliknij pozycję **Urządzenia**. Wybierz i kliknij urządzenie w chmurze na liście urządzeń połączonych z usługą.
    ![Wybieranie urządzenia StorSimple w chmurze](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Przejdź kolejno do pozycji **Ustawienia > Zabezpieczenia**, aby otworzyć blok **Ustawienia zabezpieczeń**.

     ![Ustawienia zabezpieczeń urządzenia StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Przejdź do sekcji **Zdalne zarządzanie**. Kliknij pole **Zdalne zarządzanie**.
     ![Zdalne zarządzanie urządzeniem StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. W bloku **Zdalne zarządzanie**:

    1. Upewnij się, że pole **Włącz administrację zdalną** zostało zaznaczone.
    2. Ustawieniem domyślnym jest łączenie za pośrednictwem protokołu HTTPS. Możesz wybrać opcję połączenia przy użyciu protokołu HTTP. Łączenie za pośrednictwem protokołu HTTP jest dopuszczalne tylko w sieciach zaufanych. Upewnij się, że protokół HTTP został włączony.
    3. Na pasku poleceń w górnej części bloku kliknij pozycję **... Więcej** , a następnie kliknij pozycję **Pobierz certyfikat** , aby pobrać certyfikat zdalnego zarządzania. Możesz określić lokalizację, w której chcesz zapisać plik. Certyfikat trzeba zainstalować na kliencie lub maszynie hosta, która zostanie użyta do nawiązania połączenia z urządzeniem w chmurze.

        ![Blok Zdalne zarządzanie](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Kliknij przycisk **Zapisz** i po wyświetleniu monitu potwierdź zmiany.