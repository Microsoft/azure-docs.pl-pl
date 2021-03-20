---
title: Zarządzanie wykazem kopii zapasowych StorSimple | Microsoft Docs
description: Wyjaśnia, w jaki sposób używać strony wykazu kopii zapasowych StorSimple Device Manager Service, aby wyświetlać, wybierać i usuwać zestawy kopii zapasowych.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017376"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Zarządzanie wykazem kopii zapasowych przy użyciu usługi StorSimple Device Manager
## <a name="overview"></a>Omówienie
Blok **wykazu kopii zapasowych** usługi StorSimple Device Manager wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas wykonywania ręcznych lub zaplanowanych kopii zapasowych. Ta strona umożliwia wyświetlenie listy wszystkich kopii zapasowych zasad tworzenia kopii zapasowych lub woluminu, wybranie lub usunięcie kopii zapasowej lub użycie kopii zapasowej do przywrócenia lub klonowania woluminu.

W tym samouczku wyjaśniono, jak wyświetlać, wybierać i usuwać zestaw kopii zapasowych. Aby dowiedzieć się, jak przywrócić urządzenie z kopii zapasowej, przejdź do obszaru [przywracanie urządzenia z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md). Aby dowiedzieć się, jak sklonować wolumin, przejdź do [klonowania woluminu StorSimple](storsimple-8000-clone-volume-u2.md).

![Wykaz kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Blok **wykazu kopii zapasowych** zawiera zapytanie umożliwiające zawężenie wybranego zestawu kopii zapasowych. Zestawy kopii zapasowych, które są pobierane, można filtrować na podstawie następujących parametrów:

* **Urządzenie** — urządzenie, na którym został utworzony zestaw kopii zapasowych.
* **Zasady lub wolumin kopii zapasowej** — zasady kopii zapasowej lub woluminy skojarzone z tym zestawem kopii zapasowych.
* **Od i do** – zakres dat i godzin tworzenia zestawu kopii zapasowych.

Filtrowane zestawy kopii zapasowych są następnie wyświetlane na podstawie następujących atrybutów:

* **Name** — Nazwa zasad lub woluminu kopii zapasowej skojarzonych z zestawem kopii zapasowych.
* **Size** — rzeczywisty rozmiar zestawu kopii zapasowych.
* Data i **godzina utworzenia kopii** zapasowych. 
* **Typ** — zestawy kopii zapasowych mogą być migawkami lokalnymi lub migawkami w chmurze. Lokalna migawka to kopia zapasowa wszystkich danych woluminu przechowywanych lokalnie na urządzeniu, natomiast migawka w chmurze odwołuje się do kopii zapasowej danych woluminu znajdujących się w chmurze. Migawki lokalne zapewniają szybszy dostęp, podczas gdy migawki chmur są wybrane do odporności danych.
* **Zainicjowane przez** — kopie zapasowe mogą być inicjowane automatycznie przez harmonogram lub ręcznie przez użytkownika. Aby zaplanować wykonywanie kopii zapasowych, można użyć zasad tworzenia kopii zapasowych. Alternatywnie możesz użyć opcji **Wykonaj kopię zapasową** , aby ręcznie wykonać kopię zapasową.

## <a name="list-backup-sets-for-a-backup-policy"></a>Utwórz listę zestawów kopii zapasowych dla zasad tworzenia kopii zapasowych
Wykonaj następujące kroki, aby wyświetlić listę wszystkich kopii zapasowych zasad tworzenia kopii zapasowych.

#### <a name="to-list-backup-sets"></a>Aby wyświetlić listę zestawów kopii zapasowych
1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **wykaz kopii zapasowych**.

2. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Filtruj według **zasad tworzenia kopii zapasowych** , aby wyświetlić odpowiednie kopie zapasowe.
   3. Z listy rozwijanej zasady tworzenia kopii zapasowej wybierz pozycję **wszystkie** , aby wyświetlić wszystkie kopie zapasowe na wybranym urządzeniu.
   4. Kliknij przycisk **Zastosuj** , aby wykonać to zapytanie.
      
      Kopie zapasowe skojarzone z wybranymi zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Wybierz zestaw kopii zapasowych
Wykonaj następujące kroki, aby wybrać zestaw kopii zapasowych dla woluminu lub zasad tworzenia kopii zapasowych.

#### <a name="to-select-a-backup-set"></a>Aby wybrać zestaw kopii zapasowych
1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **wykaz kopii zapasowych**.
2. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według woluminów lub zasad tworzenia kopii zapasowej, które chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** , aby wykonać to zapytanie.
      
      Kopie zapasowe skojarzone z wybranym woluminem lub zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.

      ![Przejdź do wykazu kopii zapasowych 2](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wybierz i rozwiń zestaw kopii zapasowych. Teraz można zobaczyć zestawy kopii zapasowych podzielone na woluminy, które zawiera. Opcje **przywracania** i **usuwania** są dostępne za pośrednictwem menu kontekstowego (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Każdą z tych akcji można wykonać w wybranym zestawie kopii zapasowych.

    ![Przejdź do wykazu kopii zapasowych 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Usuwanie zestawu kopii zapasowych
Usuń kopię zapasową, gdy nie chcesz już zachować skojarzonych z nią danych. Wykonaj następujące kroki, aby usunąć zestaw kopii zapasowych.

#### <a name="to-delete-a-backup-set"></a>Aby usunąć zestaw kopii zapasowych
 Przejdź do usługi StorSimple Device Manager i kliknij pozycję **wykaz kopii zapasowych**.
1. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według woluminów lub zasad tworzenia kopii zapasowej, które chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** , aby wykonać to zapytanie.
      
      Kopie zapasowe skojarzone z wybranym woluminem lub zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.

      ![Przejdź do wykazu kopii zapasowych 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Wybierz i rozwiń zestaw kopii zapasowych. Teraz można zobaczyć zestawy kopii zapasowych podzielone na woluminy, które zawiera. Opcje **przywracania** i **usuwania** są dostępne za pośrednictwem menu kontekstowego (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Kliknij prawym przyciskiem myszy wybrany zestaw kopii zapasowych i z menu kontekstowego wybierz polecenie **Usuń**.

    ![Przejdź do katalogu kopii zapasowych 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po wyświetleniu monitu o potwierdzenie Przejrzyj wyświetlone informacje, a następnie kliknij przycisk **Usuń**. Wybrana kopia zapasowa zostanie trwale usunięta.

    ![Przejdź do wykazu kopii zapasowych 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Otrzymasz powiadomienie, gdy usuwanie jest w toku, a po pomyślnym zakończeniu. Po zakończeniu usuwania Odśwież zapytanie na tej stronie. Usunięty zestaw kopii zapasowych nie będzie już wyświetlany na liście zestawów kopii zapasowych.

    ![Przejdź do wykazu kopii zapasowych 7](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak przywrócić urządzenie z zestawu kopii zapasowych przy użyciu wykazu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

