---
title: Nietrwałe usuwanie dla programu SQL Server na maszynie wirtualnej platformy Azure i SAP HANA w obciążeniach maszyn wirtualnych platformy Azure
description: Dowiedz się, jak usuwanie nietrwałe dla programu SQL Server na maszynie wirtualnej platformy Azure i SAP HANA w obciążeniach maszyn wirtualnych platformy Azure zwiększa bezpieczeństwo kopii zapasowych.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254299"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Nietrwałe usuwanie dla programu SQL Server na maszynie wirtualnej platformy Azure i SAP HANA w obciążeniach maszyn wirtualnych platformy Azure

Azure Backup teraz zapewnia nietrwałe usuwanie programu SQL Server na maszynie wirtualnej platformy Azure i SAP HANA w obciążeniach maszyn wirtualnych platformy Azure. Jest to dodatek do już obsługiwanego [scenariusza usuwania nietrwałego maszyny wirtualnej platformy Azure](soft-delete-virtual-machines.md).

[Usuwanie nietrwałe](backup-azure-security-feature-cloud.md) to funkcja zabezpieczeń, która pomaga chronić dane kopii zapasowej nawet po usunięciu. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa kopię zapasową bazy danych (lub dane kopii zapasowej są przypadkowo usuwane), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni. Pozwala to na odzyskanie tego elementu kopii zapasowej bez utraty danych. To dodatkowe przechowywanie danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiąże się z żadnym kosztem dla klienta.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Usuwanie nietrwałe dla programu SQL Server na maszynie wirtualnej platformy Azure przy użyciu Azure Portal

>[!NOTE]
>Te instrukcje dotyczą również SAP HANA na maszynie wirtualnej platformy Azure.

1. Aby usunąć dane kopii zapasowej bazy danych programu SQL Server, należy zatrzymać wykonywanie kopii zapasowej. W Azure Portal przejdź do magazynu Recovery Services, przejdź do elementu kopii zapasowej, a następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

   ![Zatrzymaj kopię zapasową](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. W poniższym oknie zostanie wybrana opcja usuwania lub zachowywania danych kopii zapasowej. W przypadku wybrania opcji **Usuń dane kopii zapasowej** kopia zapasowa bazy danych nie zostanie trwale usunięta. Zamiast tego dane kopii zapasowej będą przechowywane przez 14 dni w stanie nietrwałego usunięcia. Usuwanie jest odroczone do piętnastego dnia ze zwykłymi wiadomościami e-mail z alertami w dniu pierwszego, dwunastu i piętnastego dnia informującego o stanie kopii zapasowej bazy danych dla użytkownika.

   ![Usuwanie danych kopii zapasowej](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. W ciągu 14 dni w magazynie Recovery Services niewygładzony element usunięty zostanie wyświetlony z czerwoną ikoną "Usuwanie miękkie".

   ![Niewygładzone elementy usunięte](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Aby przywrócić nietrwałe usunięcie bazy danych, należy najpierw usunąć jej usunięcie. Aby cofnąć usunięcie, wybierz nietrwałe usunięcie bazy danych, a następnie wybierz opcję **Cofnij usunięcie**.

   ![Cofanie usunięcia bazy danych](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Zostanie wyświetlone okno z ostrzeżeniem, że w przypadku wybrania opcji Cofnij usuwanie wszystkie punkty przywracania dla bazy danych zostaną cofnięte i udostępnione do wykonania operacji przywracania. Element kopii zapasowej zostanie zachowany w stanie "Wstrzymaj ochronę przy zachowaniu danych" z wstrzymanymi kopiami zapasowymi, a dane kopii zapasowej są zachowywane w niepełnym stopniu bez skutecznego działania

   ![Usuń ostrzeżenie](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. W tym momencie można również przywrócić dane, wybierając opcję **Przywróć** dla wybranego nietrwałego elementu kopii zapasowej.

   ![Przywracanie maszyny wirtualnej](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Po ukończeniu procesu cofania usuwania stan zwróci wartość "Zatrzymaj tworzenie kopii zapasowej z zachowaniem danych", a następnie wybierz pozycję **Wznów tworzenie kopii zapasowej**. Operacja **Wznów tworzenie kopii zapasowej** powoduje przywrócenie elementu kopii zapasowej w stanie aktywnym, skojarzony z zasadami tworzenia kopii zapasowych wybranym przez użytkownika w celu zdefiniowania harmonogramów tworzenia kopii zapasowych i przechowywania.

   ![Wznów tworzenie kopii zapasowej](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Usuwanie nietrwałe dla programu SQL Server na maszynie wirtualnej przy użyciu Azure PowerShell

>[!NOTE]
>Wersja AZ. RecoveryServices wymagana do użycia nietrwałego usuwania przy użyciu Azure PowerShell jest minimalna 2.2.0. Użyj, `Install-Module -Name Az.RecoveryServices -Force` Aby uzyskać najnowszą wersję.

Sekwencja kroków do użycia Azure PowerShell jest taka sama jak w przypadku Azure Portal, pokreślonych powyżej.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Usuń element kopii zapasowej za pomocą Azure PowerShell

Usuń element kopii zapasowej za pomocą polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) programu PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

**DeleteState** elementu kopii zapasowej zmieni się z **NotDeleted** na **ToBeDeleted**. Dane kopii zapasowej będą przechowywane przez 14 dni. Jeśli chcesz przywrócić operację usuwania, należy wykonać Cofnięcie usunięcia.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Cofanie operacji usuwania przy użyciu Azure PowerShell

Najpierw Pobierz odpowiedni element kopii zapasowej, który jest w stanie usuwania nietrwałego (to wszystko, aby usunąć).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Następnie wykonaj operację cofania usuwania przy użyciu polecenia cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) programu PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

**DeleteState** elementu kopii zapasowej zostanie przywrócony do **NotDeleted**. Jednak ochrona jest nadal zatrzymana. Wznów tworzenie kopii zapasowej, aby ponownie włączyć ochronę.

## <a name="how-to-disable-soft-delete"></a>Jak wyłączyć usuwanie nietrwałe

Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest to, że planujesz przeniesienie chronionych elementów do nowego magazynu i nie będzie można odczekać 14 dni przed usunięciem i ponownym włączeniem ochrony (na przykład w środowisku testowym). Aby uzyskać instrukcje dotyczące sposobu wyłączania usuwania nietrwałego, zobacz [Włączanie i wyłączanie usuwania nietrwałego](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [często zadawane pytania](backup-azure-security-feature-cloud.md#frequently-asked-questions) dotyczące usuwania nietrwałego
- Przeczytaj o wszystkich [funkcjach zabezpieczeń w programie Azure Backup](security-overview.md)
