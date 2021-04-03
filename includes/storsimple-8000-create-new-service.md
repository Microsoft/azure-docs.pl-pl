---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 746198f87e23cd7aca2a3177c23974917cb4b12a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027561"
---
#### <a name="to-create-a-new-service"></a>Aby utworzyć nową usługę

1. Użyj poświadczeń konta Microsoft, aby zalogować się do witryny [Azure Portal](https://portal.azure.com/).

2. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**, a następnie w witrynie Marketplace kliknij pozycję **Zobacz wszystko**.

    ![Tworzenie usługi Menedżer urządzeń StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Wyszukaj pozycję _Urządzenie fizyczne StorSimple_. Wybierz i kliknij pozycję **Seria urządzeń fizycznych StorSimple**, a następnie kliknij pozycję **Utwórz**. Alternatywnie, w Azure Portal kliknij pozycję, **+** a następnie w obszarze **Magazyn** kliknij pozycję **StorSimple Seria urządzeń fizycznych**.

    ![Tworzenie StorSimple Device Manager 2](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. W bloku **Menedżer urządzeń StorSimple** wykonaj następujące kroki:

   1. Podaj **unikatową nazwę zasobu** dla swojej usługi. Jest to przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 2 do 50 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

   2. Wybierz opcję **Subskrypcja** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole nie jest widoczne, jeśli istnieje tylko jedna subskrypcja.

   3. W obszarze **Grupa zasobów** wybierz opcję **użycia istniejącej** grupy lub **utworzenia nowej**. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Wypełnij pole **Lokalizacja** dla usługi. Ogólnie rzecz biorąc, wybierz lokalizację najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Weź także pod uwagę następujące kwestie:

      * Jeśli masz istniejące obciążenia na platformie Azure, które również zamierzasz wdrożyć na urządzeniu StorSimple, użyj tego centrum danych.
      * Usługi Menedżer urządzeń StorSimple i Azure Storage mogą działać w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie. Aby utworzyć konto usługi Azure Storage, przejdź do usługi Azure Storage w witrynie Azure Portal i postępuj zgodnie z instrukcjami w temacie [Tworzenie konta usługi Azure Storage](../articles/storage/common/storage-account-create.md). Po utworzeniu konta dodaj je do usługi Menedżer urządzeń StorSimple, wykonując czynności w temacie [Configure a new storage account for the service](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service) (Konfigurowanie nowego konta magazynu dla usługi).

   5. Wybierz opcję **Utwórz nowe konto magazynu**, aby automatycznie utworzyć konto magazynu w usłudze. Podaj nazwę tego konta magazynu. Jeśli potrzebujesz danych w innej lokalizacji, usuń zaznaczenie tego pola.

   6. Zaznacz pole **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz szybko łączyć się z tą usługą na pulpicie nawigacyjnym.

   7. Kliknij pozycję **Utwórz**, aby utworzyć usługę Menedżer urządzeń StorSimple.

       ![Utwórz StorSimple Device Manager 3](./media/storsimple-8000-create-new-service/createssdevman2.png)

Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi zostanie wyświetlone powiadomienie i zostanie otwarty blok nowej usługi.

![Tworzenie StorSimple Device Manager 4](./media/storsimple-8000-create-new-service/createssdevman5.png)