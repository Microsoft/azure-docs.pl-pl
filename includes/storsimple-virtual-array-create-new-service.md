---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 1be6a654962b513cfcf755d45e562b86067e7b25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995022"
---
#### <a name="to-create-a-new-service"></a>Aby utworzyć nową usługę

1.  Korzystając z poświadczeń konto Microsoft, zaloguj się do Azure Portal pod tym adresem URL: <https://portal.azure.com/> . W przypadku wdrażania urządzenia w portalu dla instytucji rządowych Zaloguj się pod adresem: <https://portal.azure.us/>

2.  W Azure Portal kliknij pozycję **+ Utwórz** &gt; **Magazyn** zasobów &gt; **StorSimple serii wirtualnej**.

    ![Utwórz nową usługę](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  W otwartym bloku **StorSimple Menedżer urządzeń** wykonaj następujące czynności:

    1.  Podaj **unikatową nazwę zasobu** dla swojej usługi. Nazwa zasobu jest przyjazną nazwą, która może służyć do identyfikowania usługi. Nazwa może zawierać od 2 do 50 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

    2.  Wybierz opcję **Subskrypcja** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole nie jest widoczne, jeśli istnieje tylko jedna subskrypcja.

    3.  W obszarze **Grupa zasobów** wybierz istniejącą lub Utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

    4.  Wypełnij pole **Lokalizacja** dla usługi. Zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services) , aby uzyskać więcej informacji na temat usług dostępnych w tym regionie. Ogólnie rzecz biorąc, wybierz **lokalizację** najbliżej regionu geograficznego, w którym chcesz wdrożyć urządzenie. Możesz także użyć następujących składników:

        -   Jeśli masz istniejące obciążenia na platformie Azure, które zamierzasz wdrożyć na urządzeniu z systemem StorSimple, zalecamy korzystanie z tego centrum danych.

        -   StorSimple Menedżer urządzeń i magazyn platformy Azure mogą znajdować się w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie. Aby utworzyć konto usługi Azure Storage, przejdź do usługi Azure Storage w Azure Portal i wykonaj kroki opisane w temacie [Tworzenie konta magazynu](../articles/storage/common/storage-account-create.md). Po utworzeniu konta dodaj je do usługi Menedżer urządzeń StorSimple, wykonując czynności w temacie [Configure a new storage account for the service](../articles/storsimple/storsimple-virtual-array-manage-storage-accounts.md#add-a-storage-account-credential) (Konfigurowanie nowego konta magazynu dla usługi).

        -   W przypadku wdrażania urządzenia wirtualnego w portalu dla instytucji rządowych Usługa StorSimple Menedżer urządzeń jest dostępna w lokalizacjach US Iowa i US Wirginia.

    5.  Wybierz pozycję **Utwórz nowe konto usługi Azure Storage** , aby automatycznie utworzyć konto magazynu przy użyciu usługi. Określ **nazwę konta magazynu**. Jeśli potrzebujesz danych w innej lokalizacji, usuń zaznaczenie tego pola.

    6.  Zaznacz pole **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz szybko łączyć się z tą usługą na pulpicie nawigacyjnym.

    7.  Kliknij pozycję **Utwórz**, aby utworzyć usługę Menedżer urządzeń StorSimple.

        ![Utwórz nową usługę 2](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Nastąpi przekierowanie do strony docelowej **usługi** . Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi użytkownik zostanie odpowiednio powiadomiony i stan usługi zmieni się na **Aktywny**.