---
title: Tworzenie kopii zapasowej maszyny wirtualnej SQL Server z okienka maszyny wirtualnej
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową SQL Server baz danych w usłudze Azure Virtual Machines z okienka maszyny wirtualnej.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891661"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Tworzenie kopii zapasowej SQL Server z okienka maszyny wirtualnej

W tym artykule opisano sposób tworzenia kopii zapasowych SQL Server uruchomionych na maszynach wirtualnych platformy Azure przy użyciu usługi [Azure Backup](backup-overview.md) . Można tworzyć kopie zapasowe maszyn wirtualnych SQL Server przy użyciu dwóch metod:

- Jedna SQL Server maszyny wirtualnej platformy Azure: instrukcje przedstawione w tym artykule opisują sposób tworzenia kopii zapasowej SQL Server maszyny wirtualnej bezpośrednio z widoku maszyny wirtualnej.
- Wiele SQL Server maszyn wirtualnych platformy Azure: można skonfigurować magazyn Recovery Services i skonfigurować kopię zapasową dla wielu maszyn wirtualnych. Postępuj zgodnie z instrukcjami w [tym artykule](backup-sql-server-database-azure-vms.md) w tym scenariuszu.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. Sprawdź środowisko przy użyciu [macierzy obsługi](sql-support-matrix.md).
2. Zapoznaj się z [omówieniem](backup-azure-sql-database.md) Azure Backup dla SQL Server maszyny wirtualnej.
3. Sprawdź, czy maszyna wirtualna ma [łączność sieciową](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>Skonfiguruj kopię zapasową na SQL Server

Tworzenie kopii zapasowych na maszynie wirtualnej SQL Server można włączyć z poziomu okienka **kopia zapasowa** maszyny wirtualnej. Ta metoda wykonuje dwie czynności:

- Rejestruje maszynę wirtualną SQL z usługą Azure Backup, aby zapewnić dostęp do niej.
- Program chroni wszystkie wystąpienia SQL Server uruchomione wewnątrz maszyny wirtualnej. Oznacza to, że zasady tworzenia kopii zapasowych są stosowane do wszystkich istniejących baz danych, a także do baz danych, które zostaną dodane do tych wystąpień w przyszłości.

1. Wybierz transparent w górnej części strony, aby otworzyć widok kopia zapasowa SQL Server.

    ![SQL Server widok kopii zapasowej](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Nie widzisz transparentu? Transparent jest wyświetlany tylko dla tych SQL Server maszyn wirtualnych, które są tworzone za pomocą obrazów w portalu Azure Marketplace. Jest ona również wyświetlana w przypadku maszyn wirtualnych chronionych przy użyciu kopii zapasowej maszyny wirtualnej platformy Azure. W przypadku innych obrazów można skonfigurować tworzenie kopii zapasowych zgodnie z opisem w [tym miejscu](backup-sql-server-database-azure-vms.md).

2. Wprowadź nazwę magazynu Recovery Services. Magazyn jest jednostką logiczną do przechowywania wszystkich kopii zapasowych i zarządzania nimi. W przypadku tworzenia nowego magazynu:

    - Zostanie ona utworzona w tej samej subskrypcji i regionie co SQL Serverą maszynę wirtualną, która jest używana do ochrony.
    - Zostanie on utworzony przy użyciu ustawienia magazynu geograficznie nadmiarowego (GRS) dla wszystkich kopii zapasowych. Jeśli chcesz zmienić typ nadmiarowości, należy to zrobić przed włączeniem ochrony maszyny wirtualnej. Więcej informacji znajduje się w [tym artykule](backup-create-rs-vault.md#set-storage-redundancy).

3. Wybierz **zasady tworzenia kopii zapasowych**. Można wybrać jedną z domyślnych zasad lub innych istniejących zasad utworzonych w magazynie. Jeśli chcesz utworzyć nowe zasady, możesz zapoznać się z [tym artykułem](backup-sql-server-database-azure-vms.md#create-a-backup-policy) , aby zapoznać się z przewodnikiem krok po kroku.

    ![Wybieranie zasad kopii zapasowych](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Wybierz pozycję **Włącz kopię zapasową**. Wykonanie operacji może potrwać kilka minut.

    ![Wybierz pozycję Włącz kopię zapasową](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Po zakończeniu operacji zobaczysz **nazwę magazynu** na banerze.

    ![Nazwa magazynu pojawia się na transparencie](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Wybierz transparent, aby przejść do widoku magazynu, w którym można zobaczyć wszystkie zarejestrowane maszyny wirtualne i ich stan ochrony.

    ![Widok magazynu z zarejestrowanymi maszynami wirtualnymi](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. W przypadku obrazów spoza witryny Marketplace Rejestracja może się powieść, ale **skonfigurowanie kopii zapasowej** może nie być wyzwalane, dopóki Azure Backup rozszerzenie nie zostanie przyznane na SQL Server. W takich przypadkach kolumna **gotowość do tworzenia kopii zapasowych** **nie jest gotowa**. Należy ręcznie [przypisać odpowiednie uprawnienia](backup-azure-sql-database.md#set-vm-permissions) dla obrazów spoza witryny Marketplace, aby można było skonfigurować tworzenie kopii zapasowej.

    ![Gotowość do tworzenia kopii zapasowych nie jest gotowa](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Aby uzyskać więcej operacji lub monitorowania, które należy wykonać w ramach kopii zapasowej SQL Server maszynie wirtualnej, przejdź do odpowiedniego magazynu Recovery Services. Przejdź do **pozycji elementy kopii zapasowej** , aby wyświetlić wszystkie bazy danych, których kopię zapasową utworzono w tym magazynie, i Wyzwól operacje, takie jak tworzenie kopii zapasowych na żądanie i przywracanie. Analogicznie przejdź do **zadania tworzenia kopii zapasowej** , aby [monitorować](manage-monitor-sql-database-backup.md) zadania odpowiadające operacjom, takim jak Konfigurowanie ochrony, kopii zapasowej i przywracania.

    ![Wyświetlanie kopii zapasowych baz danych w elementach kopii zapasowej](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>Kopia zapasowa nie jest konfigurowana automatycznie na żadnym z nowych wystąpień SQL Server, które mogą zostać później dodane do chronionej maszyny wirtualnej. Aby skonfigurować kopię zapasową na nowo dodanych wystąpieniach, należy przejść do magazynu, w którym zarejestrowano maszynę wirtualną, i wykonać kroki opisane w [tym miejscu](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Następne kroki

Instrukcje:

- [Przywracanie kopii zapasowych SQL Server baz danych](restore-sql-database-azure-vm.md)
- [Zarządzanie kopiami zapasowymi baz danych SQL Server](manage-monitor-sql-database-backup.md)
