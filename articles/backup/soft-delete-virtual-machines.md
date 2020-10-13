---
title: Usuwanie nietrwałe maszyn wirtualnych
description: Dowiedz się, jak usuwanie nietrwałe dla maszyn wirtualnych sprawia, że kopie zapasowe są bezpieczniejsze.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022245"
---
# <a name="soft-delete-for-virtual-machines"></a>Usuwanie nietrwałe maszyn wirtualnych

Usuwanie nietrwałe dla maszyn wirtualnych chroni kopie zapasowe maszyn wirtualnych przed niezamierzonym usunięciem. Nawet po usunięciu kopii zapasowych są one zachowywane w stanie nietrwałego usunięcia przez 14 dodatkowych dni.

> [!NOTE]
> Usuwanie nietrwałe chroni tylko usunięte dane kopii zapasowej. Jeśli maszyna wirtualna zostanie usunięta bez kopii zapasowej, funkcja usuwania nietrwałego nie będzie zachować danych. Wszystkie zasoby powinny być chronione za pomocą Azure Backup, aby zapewnić pełną odporność.
>

## <a name="supported-regions"></a>Obsługiwane regiony

Usuwanie nietrwałe jest obecnie obsługiwane w zachodnich środkowe stany USA, Azja Wschodnia, Kanada środkowa, Kanada Wschodnia, Francja środkowa, Francja Południowa, Korea środkowa, Korea Południowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Australia Wschodnia, Australia Południowo-Wschodnia, Południowe stany USA, zachodnie stany USA, Europa Zachodnia, południowe stany USA, Indie Południowe, Indie Zachodnie, Japonia południowo Azja Wschodnia-Wschodnia , Szwajcaria Północna, Szwajcaria Zachodnia, Norwegia Zachodnia, Norwegia Wschodnia i wszystkie regiony narodowe.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu Azure Portal

1. Aby usunąć dane kopii zapasowej maszyny wirtualnej, należy zatrzymać wykonywanie kopii zapasowej. W Azure Portal przejdź do magazynu Recovery Services, kliknij prawym przyciskiem myszy element kopii zapasowej, a następnie wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej**.

   ![Zrzut ekranu przedstawiający elementy Azure Portal kopii zapasowej](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. W poniższym oknie zostanie wybrana opcja usuwania lub zachowywania danych kopii zapasowej. W przypadku wybrania opcji **Usuń dane kopii zapasowej** , a następnie **zatrzymania wykonywania**kopii zapasowej kopia zapasowa maszyny wirtualnej nie zostanie trwale usunięta. Zamiast tego dane kopii zapasowej będą przechowywane przez 14 dni w stanie nietrwałego usunięcia. W przypadku wybrania opcji **Usuń dane kopii zapasowej** do skonfigurowanego identyfikatora poczty e-mail zostanie wysłany alert z informacją o tym, że 14 dni pozostaną Rozszerzone przechowywanie danych kopii zapasowej. Ponadto w 12-dniowym dniu jest wysyłany alert e-mail z informacją o tym, że pozostały jeszcze dwa dni, aby przywracania aktywności usunięte dane. Usuwanie zostanie odroczone do 15-dniowego dnia, po upływie którego nastąpi trwałe usunięcie i zostanie wysłany końcowy alert e-mail z informacją o trwałym usunięciu danych.

   ![Zrzut ekranu przedstawiający ekran Azure Portal, Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. W ciągu 14 dni w magazynie Recovery Services nietrwałe usunięte maszyny wirtualne będą wyświetlane z czerwoną ikoną "miękki-Delete".

   ![Zrzut ekranu przedstawiający Azure Portal, maszynę wirtualną w stanie usuwania nietrwałego](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Jeśli w magazynie znajdują się jakiekolwiek nietrwałe usunięte elementy kopii zapasowej, nie można w tym momencie usunąć magazynu. Spróbuj usunąć magazyn po usunięciu trwałych elementów kopii zapasowej, a w magazynie nie ma żadnych elementów w stanie usunięte nietrwałe.

4. Aby przywrócić nietrwałą maszynę wirtualną, należy najpierw ją usunąć. Aby cofnąć usunięcie, wybierz maszynę wirtualną, która została usunięta, a następnie wybierz opcję **Cofnij usunięcie**.

   ![Zrzut ekranu przedstawiający Azure Portal, cofanie usunięcia maszyny wirtualnej](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zostanie wyświetlone okno z ostrzeżeniem, że w przypadku wybrania opcji Cofnij usuwanie wszystkie punkty przywracania dla maszyny wirtualnej będą cofnięte i dostępne do wykonania operacji przywracania. Maszyna wirtualna zostanie zachowana w stanie "Zatrzymaj ochronę przy zachowaniu danych" z wstrzymanymi kopiami zapasowymi, a dane kopii zapasowej są zachowywane w nieskończoność bez względu na to, czy zasady tworzenia

   ![Zrzut ekranu przedstawiający Azure Portal, potwierdź Cofnięcie usunięcia maszyny wirtualnej](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   W tym momencie można również przywrócić maszynę wirtualną, wybierając pozycję **Przywróć maszynę wirtualną** z wybranego punktu przywracania.  

   ![Zrzut ekranu przedstawiający Azure Portal, opcja Przywróć maszynę wirtualną](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Moduł wyrzucania elementów bezużytecznych będzie uruchamiał i czyścił wygasłe punkty odzyskiwania dopiero po wykonaniu operacji **wznawiania kopii zapasowej** .

5. Po ukończeniu procesu cofania usuwania stan zwróci wartość "Zatrzymaj tworzenie kopii zapasowej z zachowaniem danych", a następnie wybierz pozycję **Wznów tworzenie kopii zapasowej**. Operacja **Wznów tworzenie kopii zapasowej** powoduje przywrócenie elementu kopii zapasowej w stanie aktywnym, skojarzony z zasadami tworzenia kopii zapasowych wybranym przez użytkownika w celu zdefiniowania harmonogramów tworzenia kopii zapasowych i przechowywania.

   ![Zrzut ekranu przedstawiający Azure Portal, Wznów opcję tworzenia kopii zapasowej](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu Azure PowerShell

> [!IMPORTANT]
> Wersja AZ. RecoveryServices wymagana do użycia nietrwałego usuwania przy użyciu Azure PowerShell jest minimalna 2.2.0. Użyj, ```Install-Module -Name Az.RecoveryServices -Force``` Aby uzyskać najnowszą wersję.

Jak opisano powyżej dla Azure Portal, sekwencja kroków jest taka sama, ale również przy użyciu Azure PowerShell.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Usuń element kopii zapasowej za pomocą Azure PowerShell

Usuń element kopii zapasowej za pomocą polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) programu PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

"DeleteState" elementu kopii zapasowej zmieni się z "NotDeleted" na "ToBeDeleted". Dane kopii zapasowej będą przechowywane przez 14 dni. Jeśli chcesz przywrócić operację usuwania, należy wykonać Cofnięcie usunięcia.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Cofanie operacji usuwania przy użyciu Azure PowerShell

Najpierw Pobierz odpowiedni element kopii zapasowej, który jest w stanie usuwania nietrwałego (to wszystko, aby usunąć).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Następnie wykonaj operację cofania usuwania przy użyciu polecenia cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) programu PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

"DeleteState" elementu kopii zapasowej zostanie przywrócony do "NotDeleted". Jednak ochrona jest nadal zatrzymana. [Wznów tworzenie kopii zapasowej,](./backup-azure-vms-automation.md#change-policy-for-backup-items) aby ponownie włączyć ochronę.

## <a name="soft-delete-for-vms-using-rest-api"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu interfejsu API REST

- Usuń kopie zapasowe przy użyciu interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Jeśli chcesz cofnąć te operacje usuwania, zapoznaj się z krokami opisanymi [tutaj](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).

## <a name="how-to-disable-soft-delete"></a>Jak wyłączyć usuwanie nietrwałe

Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest to, że planujesz przeniesienie chronionych elementów do nowego magazynu i nie będzie można odczekać 14 dni przed usunięciem i ponownym włączeniem ochrony (na przykład w środowisku testowym). Aby uzyskać instrukcje dotyczące sposobu wyłączania usuwania nietrwałego, zobacz [Włączanie i wyłączanie usuwania nietrwałego](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [często zadawane pytania](backup-azure-security-feature-cloud.md#frequently-asked-questions) dotyczące usuwania nietrwałego
- Przeczytaj o wszystkich [funkcjach zabezpieczeń w programie Azure Backup](security-overview.md)
