---
title: Przywracanie usługi Azure Dyski zarządzane
description: Dowiedz się, jak przywrócić usługę Azure Dyski zarządzane z Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517202"
---
# <a name="restore-azure-managed-disks"></a>Przywracanie usługi Azure Dyski zarządzane

W tym artykule wyjaśniono, jak przywrócić [usługę Azure Dyski zarządzane](../virtual-machines/managed-disks-overview.md) z punktu przywracania utworzonego przez Azure Backup.

Obecnie opcja Original-Location Recovery (OLR) przywracania przez zastąpienie istniejącego dysku źródłowego, z którego zostały wykonane kopie zapasowe, nie jest obsługiwana. Możesz przywrócić z punktu odzyskiwania, aby utworzyć nowy dysk w tej samej grupie zasobów co dysk źródłowy, z którego zostały wykonane kopie zapasowe, lub w dowolnej innej grupie zasobów. Jest to nazywane odzyskiwaniem Alternate-Location (ALR), co pomaga zachować zarówno dysk źródłowy, jak i przywrócony (nowy).

Ten artykuł obejmuje następujące zagadnienia:

- Przywróć, aby utworzyć nowy dysk

- Śledzenie stanu operacji przywracania

## <a name="restore-to-create-a-new-disk"></a>Przywróć, aby utworzyć nowy dysk

Usługa Backup Vault używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby przywrócić z kopii zapasowej, tożsamość zarządzana magazynu usługi Backup wymaga zestawu uprawnień w grupie zasobów, w której ma zostać przywrócony dysk.

Magazyn kopii zapasowych używa przypisanej przez system tożsamości zarządzanej, która jest ograniczona do jednej na zasób i jest powiązana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można przyznać przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. Tożsamość zarządzana jest jednostką usługi specjalnego typu, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych.](../active-directory/managed-identities-azure-resources/overview.md)

Następujące wymagania wstępne są wymagane do wykonania operacji przywracania:

1. Przypisz **rolę Operator przywracania** dysków do tożsamości zarządzanej magazynu kopii zapasowych w grupie zasobów, w której dysk zostanie przywrócony przez usługę Azure Backup kopii zapasowych.

    >[!NOTE]
    > Możesz wybrać tę samą grupę zasobów co dysk źródłowy, z którego są podejmowane kopie zapasowe, lub do dowolnej innej grupy zasobów w ramach tej samej lub innej subskrypcji.

    1. Przejdź do grupy zasobów, do której ma zostać przywrócony dysk. Na przykład grupa zasobów to *TargetRG*.

    1. Przejdź do **opcji Kontrola dostępu (IAM)** i wybierz **pozycję Dodaj przypisania ról**

    1. W okienku kontekstowym po prawej stronie wybierz pozycję **Operator przywracania dysku** z **listy** rozwijanej Rola. Wybierz tożsamość zarządzaną magazynu kopii zapasowych i pozycję **Zapisz.**

        >[!TIP]
        >Wpisz nazwę magazynu kopii zapasowych, aby wybrać tożsamość zarządzaną magazynu.

        ![Wybieranie roli operatora przywracania dysku](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Sprawdź, czy tożsamość zarządzana magazynu kopii zapasowych ma odpowiedni zestaw przypisań ról w grupie zasobów, w której zostanie przywrócony dysk.

    1. Przejdź do magazynu **usługi Backup — > Tożsamości i** wybierz pozycję Przypisania ról platformy **Azure**

        ![Wybieranie przypisań ról platformy Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Sprawdź, czy rola, nazwa zasobu i typ zasobu są wyświetlane poprawnie.

        ![Weryfikowanie roli, nazwy zasobu i typu zasobu](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Mimo że przypisania ról są poprawnie odzwierciedlane w portalu, zastosowanie uprawnień do tożsamości zarządzanej magazynu kopii zapasowych może potrwać około 15 minut.
    >
    >Podczas tworzenia zaplanowanych kopii zapasowych lub operacji tworzenia kopii zapasowej na żądanie program Azure Backup migawki przyrostowe dysku w grupie zasobów migawki podanej podczas konfigurowania kopii zapasowej dysku. Azure Backup używa tych migawek przyrostowych podczas operacji przywracania. Jeśli migawki zostaną usunięte lub przeniesione z grupy zasobów migawki lub przypisania roli magazynu usługi Backup zostaną odwołane w grupie zasobów migawki, operacja przywracania nie powiedzie się.

1. Jeśli dysk do przywrócenia jest szyfrowany przy użyciu kluczy zarządzanych przez klienta [lub](../virtual-machines/disks-enable-customer-managed-keys-portal.md) podwójnego szyfrowania przy użyciu  kluczy zarządzanych przez platformę i kluczy zarządzanych przez [klienta,](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)przypisz uprawnienie Roli czytelnika do tożsamości zarządzanej usługi Backup Vault w zasobie **zestawu** szyfrowania dysków.

Po spełnianiu wymagań wstępnych wykonaj następujące kroki, aby wykonać operację przywracania.

1. Na stronie [Azure Portal](https://portal.azure.com/)przejdź do Centrum **kopii zapasowych.** Wybierz **pozycję Wystąpienia kopii zapasowych** w **sekcji** Zarządzanie. Z listy wystąpień kopii zapasowych wybierz wystąpienie kopii zapasowej dysku, dla którego chcesz wykonać operację przywracania.

    ![Lista wystąpień kopii zapasowych](./media/restore-managed-disks/backup-instances.png)

    Alternatywnie tę operację można wykonać z magazynu usługi Backup użytego do skonfigurowania kopii zapasowej dysku.

1. Na **ekranie Wystąpienie kopii** zapasowej wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz pozycję **Przywróć.**

    ![Wybieranie punktu przywracania](./media/restore-managed-disks/select-restore-point.png)

1. W **przepływie pracy Przywracanie** przejrzyj informacje na karcie **Podstawowe i** Wybierz **punkt** odzyskiwania, a następnie wybierz pozycję **Dalej: Przywróć parametry.**

    ![Przejrzyj podstawowe informacje i wybierz informacje o punkcie odzyskiwania](./media/restore-managed-disks/review-information.png)

1. Na karcie **Parametry przywracania** wybierz  **subskrypcję docelową** i docelową grupę zasobów, do której chcesz przywrócić kopię zapasową. Podaj nazwę dysku do przywrócenia. Wybierz **pozycję Dalej: Przeglądanie i przywracanie.**

    ![Przywracanie parametrów](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Kopię zapasową dysków, dla których kopie zapasowe są Azure Backup za pomocą rozwiązania do tworzenia kopii zapasowych dysków, można również tworzyć przy użyciu Azure Backup kopii zapasowej maszyny wirtualnej platformy Azure z magazynem usługi Recovery Services. Jeśli skonfigurowano ochronę maszyny wirtualnej platformy Azure, do której jest dołączony ten dysk, możesz również użyć operacji przywracania maszyny wirtualnej platformy Azure. Możesz przywrócić maszynę wirtualną lub dyski i pliki lub foldery z punktu odzyskiwania odpowiedniego wystąpienia kopii zapasowej maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz Kopia [zapasowa maszyny wirtualnej platformy Azure.](./about-azure-vm-restore.md)

1. Po pomyślnym zakończeniu walidacji wybierz pozycję **Przywróć,** aby rozpocząć operację przywracania.

    ![Inicjowanie operacji przywracania](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Walidacja może potrwać kilka minut, zanim będzie można wyzwolić operację przywracania. Walidacja może się nie powieść, jeśli:
    >
    > - dysk o tej samej nazwie podanej w **przywróceniu** nazwy dysku już istnieje w **docelowej grupie zasobów**
    > - Tożsamość zarządzana magazynu usługi Backup nie ma prawidłowych przypisań ról w docelowej **grupie zasobów**
    > - przypisania roli tożsamości zarządzanej magazynu kopii zapasowych  są odwoływały się w grupie zasobów migawki, w której są przechowywane migawki przyrostowe
    > - Jeśli migawki przyrostowe są usuwane lub przenoszone z grupy zasobów migawki

Przywracanie spowoduje utworzenie nowego dysku z wybranego punktu odzyskiwania w docelowej grupie zasobów, która została zapewniona podczas operacji przywracania. Aby użyć przywróconego dysku na istniejącej maszynie wirtualnej, należy wykonać więcej czynności:

- Jeśli przywrócony dysk jest dyskiem danych, możesz dołączyć istniejący dysk do maszyny wirtualnej. Jeśli przywrócony dysk jest dyskiem systemu operacyjnego, możesz zamienić dysk systemu operacyjnego maszyny  wirtualnej z witryny Azure Portal w **okienku** Maszyna wirtualna — dyski > w sekcji **Ustawienia.**

    ![Wymiana dysków systemu operacyjnego](./media/restore-managed-disks/swap-os-disks.png)

- W przypadku maszyn wirtualnych z systemem Windows, jeśli przywrócony dysk jest dyskiem danych, postępuj zgodnie z instrukcjami, aby odłączyć oryginalny dysk danych [od](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) maszyny wirtualnej. Następnie [dołącz przywrócony dysk do](../virtual-machines/windows/attach-managed-disk-portal.md) maszyny wirtualnej. Postępuj zgodnie z [instrukcjami, aby zamienić dysk systemu operacyjnego](../virtual-machines/windows/os-disk-swap.md) maszyny wirtualnej na przywrócony dysk.

- W przypadku maszyn wirtualnych z systemem Linux, jeśli przywrócony dysk jest dyskiem danych, postępuj zgodnie z instrukcjami, aby odłączyć oryginalny dysk danych [od](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) maszyny wirtualnej. Następnie [dołącz przywrócony dysk do](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) maszyny wirtualnej. Postępuj zgodnie z [instrukcjami, aby zamienić dysk systemu operacyjnego](../virtual-machines/linux/os-disk-swap.md) maszyny wirtualnej na przywrócony dysk.

Zaleca się odwołanie przypisania roli **Operator** przywracania dysków z tożsamości zarządzanej magazynu usługi Backup w docelowej grupie zasobów po pomyślnym zakończeniu operacji przywracania. 

## <a name="track-a-restore-operation"></a>Śledzenie operacji przywracania

Po wyzwoleniu operacji przywracania usługa tworzenia kopii zapasowej tworzy zadanie do śledzenia. Azure Backup wyświetla powiadomienia o zadaniu w portalu. Aby wyświetlić postęp zadania przywracania:

1. Przejdź do ekranu **Wystąpienie kopii zapasowej.** Przedstawia pulpit nawigacyjny zadań z operacją i stanem z ostatnich siedmiu dni.

    ![Pulpit nawigacyjny zadań](./media/restore-managed-disks/jobs-dashboard.png)

1. Aby wyświetlić stan operacji przywracania, wybierz **pozycję** Wyświetl wszystko, aby wyświetlić bieżące i wcześniejsze zadania tego wystąpienia kopii zapasowej.

    ![Wybierz Wyświetl wszystko](./media/restore-managed-disks/view-all.png)

1. Przejrzyj listę zadań tworzenia i przywracania kopii zapasowych oraz ich stan. Wybierz zadanie z listy zadań, aby wyświetlić szczegóły zadania.

    ![Lista zadań](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Następne kroki

- [Azure Disk Backup — często zadawane pytania](disk-backup-faq.yml)