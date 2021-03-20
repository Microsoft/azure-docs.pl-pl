---
title: Zasady tworzenia kopii zapasowych StorSimple Snapshot Manager | Microsoft Docs
description: Zawiera opis sposobu korzystania z przystawki StorSimple Snapshot Manager MMC do tworzenia zasad tworzenia kopii zapasowych, które kontrolują zaplanowanymi zapasami i zarządzania nimi.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054946"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi za pomocą StorSimple Snapshot Manager
## <a name="overview"></a>Omówienie
Zasady tworzenia kopii zapasowych tworzą harmonogram tworzenia kopii zapasowych danych woluminu lokalnie lub w chmurze. Podczas tworzenia zasad tworzenia kopii zapasowych można także określić zasady przechowywania. (Można zachować maksymalnie 64 migawek). Aby uzyskać więcej informacji na temat zasad tworzenia kopii zapasowych, zobacz [typy kopii zapasowych](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) w [serii StorSimple 8000: rozwiązanie w chmurze hybrydowej](storsimple-overview.md).

W tym samouczku wyjaśniono:

* Tworzenie zasad kopii zapasowych
* Edytowanie zasad kopii zapasowych
* Usuwanie zasad kopii zapasowych

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych
Aby utworzyć nowe zasady tworzenia kopii zapasowych, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-create-a-backup-policy"></a>Aby utworzyć zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij prawym przyciskiem myszy pozycję **zasady tworzenia kopii zapasowych**, a następnie kliknij pozycję **Utwórz zasady tworzenia kopii zapasowych**.

    ![Tworzenie zasad kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Zostanie wyświetlone okno dialogowe **Tworzenie zasad** .

    ![Tworzenie zasad — karta Ogólne](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na karcie **Ogólne** wykonaj następujące informacje:

   1. W polu tekstowym **Nazwa** wpisz nazwę zasady.
   2. W polu tekstowym **Grupa woluminów** wpisz nazwę grupy woluminów skojarzonej z zasadami.
   3. Wybierz **migawkę lokalną** lub **migawkę w chmurze**.
   4. Wybierz liczbę migawek do zachowania. W przypadku wybrania opcji **wszystkie** migawki 64 będą zachowywane (wartość maksymalna).
4. Kliknij kartę **Harmonogram** .

    ![Tworzenie karty harmonogram zasad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na karcie **harmonogram** wykonaj następujące informacje:

   1. Kliknij pole wyboru **Włącz** , aby zaplanować następną kopię zapasową.
   2. W obszarze **Ustawienia** wybierz **jeden raz**, **codziennie**, **co tydzień** lub **co miesiąc**.
   3. W polu tekstowym **Start** kliknij ikonę kalendarza i wybierz datę rozpoczęcia.
   4. W obszarze **Ustawienia zaawansowane** można ustawić opcjonalne harmonogramy powtarzania i datę końcową.
   5. Kliknij przycisk **OK**.

Po utworzeniu zasad tworzenia kopii zapasowych w okienku **wyników** zostaną wyświetlone następujące informacje:

* **Nazwa** — Nazwa zasad tworzenia kopii zapasowych.
* **Typ** — migawka lokalna lub migawka w chmurze.
* **Grupa woluminów** — Grupa woluminów skojarzona z zasadami.
* **Przechowywanie** — Liczba zachowanych migawek; wartość maksymalna to 64.
* **Utworzone** — Data utworzenia tych zasad.
* **Włączone** — określa, czy zasady są obecnie aktywne: **wartość true** wskazuje, że jest ona aktywna. **Wartość false** wskazuje, że nie jest ona aktywna.

## <a name="edit-a-backup-policy"></a>Edytowanie zasad kopii zapasowych
Aby edytować istniejące zasady tworzenia kopii zapasowych, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-edit-a-backup-policy"></a>Aby edytować zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij węzeł **zasady tworzenia kopii zapasowych** . Wszystkie zasady tworzenia kopii zapasowych są wyświetlane w okienku **wyników** .
3. Kliknij prawym przyciskiem myszy zasady, które chcesz edytować, a następnie kliknij przycisk **edytować**.

    ![Edytowanie zasad kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Po wyświetleniu okna **Utwórz zasady** wprowadź zmiany, a następnie kliknij przycisk **OK**.

## <a name="delete-a-backup-policy"></a>Usuwanie zasad kopii zapasowych
Aby usunąć zasady tworzenia kopii zapasowych, wykonaj czynności opisane w poniższej procedurze.

#### <a name="to-delete-a-backup-policy"></a>Aby usunąć zasady tworzenia kopii zapasowych
1. Kliknij ikonę pulpitu, aby rozpocząć StorSimple Snapshot Manager.
2. W okienku **zakres** kliknij węzeł **zasady tworzenia kopii zapasowych** . Wszystkie zasady tworzenia kopii zapasowych są wyświetlane w okienku **wyników** .
3. Kliknij prawym przyciskiem myszy zasady tworzenia kopii zapasowych, które chcesz usunąć, a następnie kliknij przycisk **Usuń**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij przycisk **tak**.

    ![Potwierdzenie usunięcia zasad kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [używać Snapshot Manager StorSimple do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [używać Snapshot Manager StorSimple do wyświetlania zadań tworzenia kopii zapasowych i zarządzania nimi](storsimple-snapshot-manager-manage-backup-jobs.md).
