---
title: Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z ustawień maszyny wirtualnej
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową pojedynczej maszyny wirtualnej platformy Azure lub wielu maszyn wirtualnych platformy Azure z usługą Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667516"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z ustawień maszyny wirtualnej

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi [Azure Backup](backup-overview.md) . Możesz tworzyć kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu kilku metod:

- Pojedyncza maszyna wirtualna platformy Azure: instrukcje zawarte w tym artykule opisują sposób tworzenia kopii zapasowej maszyny wirtualnej platformy Azure bezpośrednio z ustawień maszyny wirtualnej.
- Wiele maszyn wirtualnych platformy Azure: można skonfigurować magazyn Recovery Services i skonfigurować kopie zapasowe dla wielu maszyn wirtualnych platformy Azure. Postępuj zgodnie z instrukcjami w [tym artykule](backup-azure-arm-vms-prepare.md) w tym scenariuszu.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Dowiedz się](backup-architecture.md#how-does-azure-backup-work) , jak działa kopia zapasowa i [Sprawdź](backup-support-matrix.md#azure-vm-backup-support) wymagania dotyczące pomocy technicznej.
2. [Zapoznaj się z omówieniem](backup-azure-vms-introduction.md) kopii zapasowej maszyny wirtualnej platformy Azure.

### <a name="azure-vm-agent-installation"></a>Instalacja agenta maszyny wirtualnej platformy Azure

Aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure, Azure Backup instaluje rozszerzenie na agencie maszyny wirtualnej uruchomionym na tym komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent zostanie uruchomiony. W niektórych przypadkach, na przykład w przypadku tworzenia niestandardowej maszyny wirtualnej lub migrowania maszyny z lokalnego, może być konieczne ręczne zainstalowanie agenta.

- Jeśli konieczne jest ręczne zainstalowanie agenta maszyny wirtualnej, postępuj zgodnie z instrukcjami dotyczącymi maszyn wirtualnych z [systemem Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) .
- Po zainstalowaniu agenta programu, gdy zostanie włączona kopia zapasowa, Azure Backup instaluje rozszerzenie kopii zapasowej do agenta. Aktualizuje i poprawki rozszerzenia bez interwencji użytkownika.

## <a name="back-up-from-azure-vm-settings"></a>Tworzenie kopii zapasowej z ustawień maszyny wirtualnej platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **wszystkie usługi** i w polu Filtr wpisz polecenie **maszyny wirtualne**, a następnie wybierz pozycję **maszyny wirtualne**.
3. Z listy maszyn wirtualnych wybierz maszynę wirtualną, dla której chcesz utworzyć kopię zapasową.
4. W menu maszyny wirtualnej wybierz pozycję **kopia zapasowa**.
5. W **magazynie Recovery Services**wykonaj następujące czynności:
   - Jeśli masz już magazyn, wybierz pozycję **Wybierz istniejący**i wybierz magazyn.
   - Jeśli nie masz magazynu, wybierz pozycję **Utwórz nowy**. Określ nazwę magazynu. Jest on tworzony w tym samym regionie i grupie zasobów co maszyna wirtualna. Nie można modyfikować tych ustawień po włączeniu kopii zapasowej bezpośrednio z ustawień maszyny wirtualnej.

        ![Kreator włączania kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. W obszarze **Wybieranie zasad tworzenia kopii zapasowej**wykonaj jedną z następujących czynności:

   - Pozostaw zasady domyślne. Spowoduje to utworzenie kopii zapasowej maszyny wirtualnej raz dziennie o określonej godzinie i przechowywanie kopii zapasowych w magazynie przez 30 dni.
   - Wybierz istniejące zasady tworzenia kopii zapasowych, jeśli je masz.
   - Utwórz nowe zasady i Zdefiniuj ustawienia zasad.  

       ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Wybierz pozycję **Włącz wykonywanie kopii zapasowej**. Spowoduje to skojarzenie zasad tworzenia kopii zapasowych z maszyną wirtualną.

    ![Przycisk Włącz wykonywanie kopii zapasowej](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Postęp konfiguracji można śledzić w powiadomieniach portalu.
9. Po zakończeniu zadania w menu maszyna wirtualna wybierz pozycję **kopia zapasowa**. Na stronie jest wyświetlany stan kopii zapasowej maszyny wirtualnej, informacje o punktach odzyskiwania, uruchomionych zadaniach i alertach.

   ![Stan kopii zapasowej](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po włączeniu kopii zapasowej zostanie uruchomione początkowe kopie zapasowe. Początkową kopię zapasową można rozpocząć od razu lub zaczekać, aż zacznie się zgodnie z harmonogramem tworzenia kopii zapasowych.
    - Do momentu ukończenia początkowej kopii zapasowej **stan ostatniej kopii zapasowej** zostanie wyświetlony jako **ostrzeżenie (początkowa kopia zapasowa oczekuje)**.
    - Aby sprawdzić, kiedy zostanie uruchomiona Następna zaplanowana kopia zapasowa, wybierz nazwę zasad kopii zapasowych.

## <a name="run-a-backup-immediately"></a>Natychmiastowe uruchamianie kopii zapasowej

1. Aby natychmiast uruchomić kopię zapasową, w menu maszyny wirtualnej wybierz pozycję **Utwórz**kopię zapasową  >  **teraz**.

    ![Uruchom kopię zapasową](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. W obszarze **kopia zapasowa**użyj kontrolki kalendarz do wybrania, dopóki punkt odzyskiwania zostanie zachowany > i **OK**.

    ![Dzień przechowywania kopii zapasowych](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Powiadomienia portalu informują o tym, że zadanie tworzenia kopii zapasowej zostało wyzwolone. Aby monitorować postęp tworzenia kopii zapasowych, wybierz pozycję **Wyświetl wszystkie zadania**.

## <a name="back-up-from-the-recovery-services-vault"></a>Tworzenie kopii zapasowej z magazynu Recovery Services

Postępuj zgodnie z instrukcjami w [tym artykule](backup-azure-arm-vms-prepare.md) , aby włączyć tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przez skonfigurowanie magazynu Azure Backup Recovery Services i włączenie tworzenia kopii zapasowych w magazynie.

## <a name="next-steps"></a>Następne kroki

- Jeśli masz problemy z dowolnymi procedurami opisanymi w tym artykule, zapoznaj się z [przewodnikiem rozwiązywania problemów](backup-azure-vms-troubleshoot.md).
- [Dowiedz się więcej na temat](backup-azure-manage-vms.md) zarządzania kopiami zapasowymi.
