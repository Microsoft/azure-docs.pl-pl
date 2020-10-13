---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure
description: W tym artykule opisano typowe zadania związane z zarządzaniem udziałami plików platformy Azure i ich monitorowaniem, które są tworzone przez Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88757629"
---
# <a name="manage-azure-file-share-backups"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure

W tym artykule opisano typowe zadania związane z zarządzaniem udziałami plików platformy Azure i ich monitorowaniem, które są tworzone przez [Azure Backup](./backup-overview.md). Dowiesz się, jak wykonywać zadania zarządzania w magazynie Recovery Services.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Gdy Wyzwalasz operację tworzenia kopii zapasowej lub przywracania, usługa Backup tworzy zadanie śledzenia. Postęp wszystkich zadań możesz monitorować na stronie **Zadania tworzenia kopii zapasowej**.

Aby otworzyć stronę **Zadania tworzenia kopii zapasowej**:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej dla udziałów plików. W okienku **Przegląd** wybierz pozycję **zadania tworzenia kopii zapasowej** w sekcji **monitorowanie** .

   ![Zadania tworzenia kopii zapasowej w sekcji monitorowanie](./media/manage-afs-backup/backup-jobs.png)

1. Po wybraniu **przycisku OK**w okienku **zadania tworzenia kopii zapasowej** będzie wyświetlany stan wszystkich zadań. Wybierz nazwę obciążenia odpowiadającą udziałowi plików, który chcesz monitorować.

   ![Nazwa obciążenia](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Tworzenie nowych zasad

Można utworzyć nowe zasady tworzenia kopii zapasowych udziałów plików platformy Azure z sekcji **zasady tworzenia kopii zapasowych** w magazynie Recovery Services. Wszystkie zasady utworzone podczas konfigurowania kopii zapasowej dla udziałów plików są wyświetlane z **typem zasad** jako **udział plików platformy Azure**.

Aby wyświetlić istniejące zasady tworzenia kopii zapasowych:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików. W menu magazyn Recovery Services wybierz pozycję **zasady tworzenia kopii zapasowych** w sekcji **Zarządzanie** . Zostaną wyświetlone wszystkie zasady tworzenia kopii zapasowej skonfigurowane w magazynie.

   ![Wszystkie zasady tworzenia kopii zapasowych](./media/manage-afs-backup/all-backup-policies.png)

1. Aby wyświetlić zasady dotyczące **udziału plików platformy Azure**, wybierz pozycję **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu.

   ![Wybierz udział plików platformy Azure](./media/manage-afs-backup/azure-file-share.png)

Aby utworzyć nowe zasady tworzenia kopii zapasowych:

1. W okienku **zasady tworzenia kopii zapasowych** wybierz pozycję **+ Dodaj**.

   ![Nowe zasady tworzenia kopii zapasowych](./media/manage-afs-backup/new-backup-policy.png)

1. W okienku **Dodawanie** wybierz pozycję **udział plików platformy Azure** jako **Typ zasad**. Zostanie otwarte okienko **zasady tworzenia kopii zapasowej** dla **udziału plików platformy Azure** . Określ nazwę zasad, częstotliwość tworzenia kopii zapasowych i zakres przechowywania dla punktów odzyskiwania. Po zdefiniowaniu zasad wybierz **przycisk OK**.

   ![Definiowanie zasad tworzenia kopii zapasowych](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modyfikuj zasady

Możesz zmodyfikować zasady tworzenia kopii zapasowej, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania.

Aby zmodyfikować zasady:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików. W menu magazyn Recovery Services wybierz pozycję **zasady tworzenia kopii zapasowych** w sekcji **Zarządzanie** . Zostaną wyświetlone wszystkie zasady tworzenia kopii zapasowej skonfigurowane w magazynie.

   ![Wszystkie zasady tworzenia kopii zapasowych w magazynie](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Aby wyświetlić zasady dotyczące udziału plików platformy Azure, wybierz pozycję **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu. Wybierz zasady tworzenia kopii zapasowej, które chcesz zmodyfikować.

   ![Udział plików platformy Azure do zmodyfikowania](./media/manage-afs-backup/azure-file-share-modify.png)

1. Zostanie otwarte okienko **harmonogram** . Zmodyfikuj **Harmonogram kopii zapasowych** i **Zakres przechowywania** zgodnie z wymaganiami, a następnie wybierz pozycję **Zapisz**. W okienku zostanie wyświetlony komunikat "Aktualizacja w toku". Po pomyślnym zaktualizowaniu zasad zostanie wyświetlony komunikat "pomyślnie Zaktualizowano zasady tworzenia kopii zapasowej".

   ![Zapisz zmodyfikowane zasady](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i *Usuń wszystkie punkty odzyskiwania*.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej, pozostawiając *punkty odzyskiwania*.

Może być kosztem związanym z opuszczeniem punktów odzyskiwania w magazynie, ponieważ źródłowe migawki utworzone przez Azure Backup zostaną zachowane. Korzyścią opuszczenia punktów odzyskiwania jest możliwość późniejszego przywrócenia udziału plików. Aby uzyskać informacje o kosztu opuszczenia punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/backup/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików platformy Azure:

1. Otwórz magazyn Recovery Services zawierający punkty odzyskiwania udziału plików. Wybierz pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** . Zostanie wyświetlona lista typów elementów kopii zapasowej.

   ![Elementy kopii zapasowej](./media/manage-afs-backup/backup-items.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Zostanie wyświetlona lista **elementy kopii zapasowej (Azure Files))** .

   ![Wybierz usługę Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Z listy **elementy kopii zapasowej (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz zatrzymać ochronę.

1. Wybierz opcję **Zatrzymaj tworzenie kopii zapasowej** .

   ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/manage-afs-backup/stop-backup.png)

1. W okienku **Zatrzymaj tworzenie kopii** zapasowej wybierz opcję **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

    ![Wybierz opcję Zachowaj dane kopii zapasowej lub Usuń dane kopii zapasowej](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli opcja **Zachowaj dane kopii zapasowej** została wybrana, gdy ochrona udziału plików została zatrzymana, można wznowić ochronę. Jeśli wybrano opcję **Usuń dane kopii zapasowej** , nie można wznowić ochrony udziału plików.

Aby wznowić ochronę udziału plików platformy Azure:

1. Otwórz magazyn Recovery Services zawierający punkty odzyskiwania udziału plików. Wybierz pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** . Zostanie wyświetlona lista typów elementów kopii zapasowej.

   ![Elementy kopii zapasowej do wznowienia](./media/manage-afs-backup/backup-items-resume.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Zostanie wyświetlona lista **elementy kopii zapasowej (Azure Files))** .

   ![Lista usługi Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Z listy **elementy kopii zapasowej (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz wznowić ochronę.

1. Wybierz opcję **Wznów tworzenie kopii zapasowej** .

   ![Wybierz pozycję Wznów wykonywanie kopii zapasowej](./media/manage-afs-backup/resume-backup.png)

1. Zostanie otwarte okienko **zasady tworzenia kopii zapasowej** . Wybierz wybrane zasady, aby wznowić tworzenie kopii zapasowej.

1. Po wybraniu zasad tworzenia kopii zapasowych wybierz pozycję **Zapisz**. W portalu zostanie wyświetlony komunikat "Aktualizacja w toku". Po pomyślnym wznowieniu tworzenia kopii zapasowej zostanie wyświetlony komunikat "pomyślnie Zaktualizowano zasady tworzenia kopii zapasowej chronionego udziału plików platformy Azure".

   ![Pomyślnie Zaktualizowano zasady tworzenia kopii zapasowych](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Kopię zapasową udziału plików można usunąć podczas zadania **zatrzymania tworzenia kopii zapasowej** lub w dowolnym momencie po zatrzymaniu ochrony. Przed usunięciem punktów odzyskiwania może być korzystne zaczekanie dni lub nawet tygodni. Po usunięciu danych kopii zapasowej nie można wybrać określonych punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowej, wszystkie punkty odzyskiwania skojarzone z udziałem plików są usuwane.

W poniższej procedurze przyjęto założenie, że ochrona udziału plików została zatrzymana.

Aby usunąć dane kopii zapasowej udziału plików platformy Azure:

1. Gdy zadanie tworzenia kopii zapasowej zostanie zatrzymane, opcje **Wznów wykonywanie kopii zapasowej** i **Usuń kopię zapasową** są dostępne na pulpicie nawigacyjnym **elementu kopii zapasowej** . Wybierz opcję **Usuń dane kopii zapasowej** .

   ![Usuwanie danych kopii zapasowej](./media/manage-afs-backup/delete-backup-data.png)

1. Zostanie otwarte okienko **Usuń dane kopii zapasowej** . Wprowadź nazwę udziału plików, aby potwierdzić usunięcie. Opcjonalnie podaj więcej informacji w polach **Przyczyna** lub **Komentarze** . Po upewnieniu się, że usuwasz dane kopii zapasowej, wybierz pozycję **Usuń**.

   ![Potwierdź usunięcie danych](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Wyrejestrowywanie konta magazynu

Aby chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu Recovery Services, należy najpierw [zatrzymać ochronę wszystkich udziałów plików](#stop-protection-on-a-file-share) na tym koncie magazynu. Następnie Wyrejestruj konto z bieżącego magazynu Recovery Services używanego do ochrony.

W poniższej procedurze przyjęto założenie, że ochrona została zatrzymana dla wszystkich udziałów plików na koncie magazynu, które chcesz wyrejestrować.

Aby wyrejestrować konto magazynu:

1. Otwórz magazyn Recovery Services, w którym zarejestrowano konto magazynu.
1. W okienku **Przegląd** wybierz opcję **infrastruktura zapasowa** w sekcji **Zarządzanie** .

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/manage-afs-backup/backup-infrastructure.png)

1. Zostanie otwarte okienko **infrastruktura tworzenia kopii zapasowych** . Wybierz pozycję **konta magazynu** w sekcji **konta usługi Azure Storage** .

   ![Wybieranie kont magazynu](./media/manage-afs-backup/storage-accounts.png)

1. Po wybraniu **konta magazynu**zostanie wyświetlona lista kont magazynu zarejestrowanych w magazynie.
1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz wyrejestrować, a następnie wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję Wyrejestruj](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z kopiami zapasowymi udziałów plików platformy Azure](./troubleshoot-azure-files.md).
