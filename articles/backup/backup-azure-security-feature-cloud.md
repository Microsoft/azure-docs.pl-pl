---
title: Usuwanie nietrwałe dla Azure Backup
description: Dowiedz się, jak używać funkcji zabezpieczeń w programie Azure Backup, aby tworzyć kopie zapasowe bardziej bezpieczne.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95254053"
---
# <a name="soft-delete-for-azure-backup"></a>Usuwanie nietrwałe dla Azure Backup

Problemy dotyczące zabezpieczeń, takie jak złośliwe oprogramowanie, programy wymuszającego okup i wtargnięcie, zwiększają się. Te problemy z zabezpieczeniami mogą być kosztowne, w odniesieniu do pieniędzy i danych. Aby ochronić przed takimi atakami, Azure Backup teraz zapewnia funkcje zabezpieczeń, które ułatwiają ochronę danych kopii zapasowej nawet po usunięciu.

Jedną z tych funkcji jest usuwanie nietrwałe. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa kopię zapasową (lub przypadkowo usunięto dane kopii zapasowej), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, umożliwiając odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z pozostałymi kosztami.

Dla tych usług jest dostępna ochrona usuwania nietrwałego:

- [Usuwanie nietrwałe dla maszyn wirtualnych platformy Azure](soft-delete-virtual-machines.md)
- [Usuwanie nietrwałe dla programu SQL Server na maszynie wirtualnej platformy Azure oraz usuwanie nietrwałe dla SAP HANA w obciążeniach maszyn wirtualnych platformy Azure](soft-delete-sql-saphana-in-azure-vm.md)

Ten wykres przepływu przedstawia różne kroki i Stany elementu kopii zapasowej po włączeniu usuwania nietrwałego:

![Cykl życia nietrwałego usuniętego elementu kopii zapasowej](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Włączanie i wyłączanie usuwania nietrwałego

Usuwanie nietrwałe jest domyślnie włączone dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem.  Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest to, że planujesz przeniesienie chronionych elementów do nowego magazynu i nie będzie można odczekać 14 dni przed usunięciem i ponownym włączeniem ochrony (na przykład w środowisku testowym). Tylko właściciel magazynu może wyłączyć tę funkcję. Jeśli wyłączysz tę funkcję, wszystkie przyszłe usunięcia chronionych elementów spowodują natychmiastowe usunięcie, bez możliwości przywrócenia. Dane kopii zapasowej, które istnieją w stanie nietrwałego usunięcia przed wyłączeniem tej funkcji, pozostaną w stanie nietrwałego usunięcia przez 14 dni. Jeśli chcesz trwale usunąć te elementy natychmiast, musisz cofnąć ich usunięcie i usunąć je ponownie, aby trwale usunąć.

Należy pamiętać, że po wyłączeniu usuwania nietrwałego funkcja ta jest wyłączona dla wszystkich typów obciążeń. Na przykład nie można wyłączyć usuwania nietrwałego tylko dla programu SQL Server lub SAP HANA baz danych, gdy jest on włączony dla maszyn wirtualnych w tym samym magazynie. Można utworzyć oddzielne magazyny dla szczegółowej kontroli.

### <a name="disabling-soft-delete-using-azure-portal"></a>Wyłączanie usuwania nietrwałego przy użyciu Azure Portal

Aby wyłączyć usuwanie nietrwałe, wykonaj następujące kroki:

1. W Azure Portal przejdź do magazynu, a następnie przejdź do pozycji **Ustawienia**  ->  **Właściwości**.
2. W okienku właściwości wybierz pozycję Aktualizacja **ustawień zabezpieczeń**  ->  .  
3. W okienku ustawienia zabezpieczeń w obszarze **usuwanie nietrwałe** wybierz pozycję **Wyłącz**.

![Wyłącz usuwanie nietrwałe](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Wyłączanie usuwania nietrwałego przy użyciu Azure PowerShell

> [!IMPORTANT]
> Wersja AZ. RecoveryServices wymagana do użycia nietrwałego usuwania przy użyciu Azure PowerShell jest minimalna 2.2.0. Użyj, ```Install-Module -Name Az.RecoveryServices -Force``` Aby uzyskać najnowszą wersję.

Aby wyłączyć, użyj polecenia cmdlet [Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) programu PowerShell.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Wyłączanie usuwania nietrwałego za pomocą interfejsu API REST

Aby wyłączyć funkcję usuwania nietrwałego przy użyciu interfejsu API REST, zapoznaj się z krokami opisanymi [tutaj](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Trwałe usuwanie nietrwałych usuniętych elementów kopii zapasowej

Dane kopii zapasowej w stanie nietrwałego usunięcia przed wyłączeniem tej funkcji pozostaną w stanie nietrwałego usunięcia. Jeśli chcesz trwale usunąć te elementy natychmiast, usuń je i Usuń ponownie, aby uzyskać trwałe usunięcie.

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Wykonaj następujące kroki:

1. Postępuj zgodnie z instrukcjami, aby [wyłączyć usuwanie nietrwałe](#enabling-and-disabling-soft-delete).

2. W Azure Portal przejdź do swojego magazynu, przejdź do **pozycji elementy kopii zapasowej** i wybierz niewygładzony element usunięty.

   ![Wybierz niewygładzony element usunięty](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Wybierz opcję **Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Zostanie wyświetlone okno. Wybierz pozycję **Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Wybierz pozycję **Usuń dane kopii zapasowej** , aby trwale usunąć dane kopii zapasowej.

   ![Wybierz pozycję Usuń dane kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

   ![Wpisz nazwę elementu kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Aby usunąć dane kopii zapasowej dla elementu, wybierz pozycję **Usuń**. Komunikat z powiadomieniem informuje o tym, że dane kopii zapasowej zostały usunięte.

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Jeśli elementy zostały usunięte przed wyłączeniem usuwania nietrwałego, zostaną one w stanie nieusuniętym. Aby natychmiast je usunąć, operacja usuwania musi zostać cofnięta, a następnie wykonana ponownie.

Zidentyfikuj elementy, które są w stanie nieusuniętym.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Następnie należy wycofać operację usuwania wykonywaną po włączeniu usuwania nietrwałego.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Ponieważ usuwanie nietrwałe jest teraz wyłączone, operacja usuwania spowoduje natychmiastowe usunięcie danych kopii zapasowej.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Korzystanie z interfejsu API REST

Jeśli elementy zostały usunięte przed wyłączeniem usuwania nietrwałego, zostaną one w stanie nieusuniętym. Aby natychmiast je usunąć, operacja usuwania musi zostać cofnięta, a następnie wykonana ponownie.

1. Najpierw Cofnij operacje usuwania, wykonując kroki opisane [tutaj](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).
2. Następnie wyłącz funkcje usuwania nietrwałego za pomocą interfejsu API REST, wykonując kroki opisane [tutaj](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Następnie usuń kopie zapasowe przy użyciu interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Czy muszę włączyć funkcję usuwania nietrwałego dla każdego magazynu?

Nie. jest ona domyślnie wbudowana i włączona dla wszystkich magazynów Recovery Services.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Czy można skonfigurować liczbę dni, przez jaką dane będą przechowywane w stanie nieusuniętym, po zakończeniu operacji usuwania?

Nie, po operacji usuwania zostanie ustalone po 14 dniach dodatkowego okresu przechowywania.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Czy muszę płacić za to dodatkowe 14 dni?

Nie, to 14-dniowe dodatkowe przechowywanie jest bezpłatne w ramach funkcji usuwania nietrwałego.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Czy można wykonać operację przywracania, gdy moje dane są w stanie usuwania nietrwałego?

Nie, Usuń nietrwały zasób nietrwałego w celu przywrócenia. Operacja cofnięcia usunięcia spowoduje przywrócenie zasobu z powrotem do **stanu Zatrzymaj ochronę przy zachowaniu danych** , w którym można przywrócić wszystkie punkty w czasie. Moduł wyrzucania elementów bezużytecznych pozostaje wstrzymany w tym stanie.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Czy moje migawki będą zgodne z tym samym cyklem życia jak moje punkty odzyskiwania w magazynie?

Tak.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak można ponownie wyzwolić zaplanowane kopie zapasowe dla nietrwałego zasobu?

Cofnięcie usunięcia po wykonaniu operacji wznawiania spowoduje ponowne włączenie ochrony zasobu. Operacja Resume kojarzy zasady tworzenia kopii zapasowych, aby wyzwolić zaplanowane kopie zapasowe z wybranym okresem przechowywania. Ponadto Moduł wyrzucania elementów bezużytecznych jest uruchamiany zaraz po zakończeniu operacji wznawiania. Jeśli chcesz wykonać przywracanie z punktu odzyskiwania, który przypada poza datą wygaśnięcia, zaleca się wykonanie tej czynności przed wyzwoleniem operacji wznowienia.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Czy mogę usunąć swój magazyn, jeśli w magazynie istnieją nietrwałe elementy usunięte?

Nie można usunąć magazynu Recovery Services, jeśli w magazynie istnieją elementy kopii zapasowej w stanie nieusuniętym. Usunięte elementy nietrwałe są usuwane przez 14 dni po operacji usuwania. Jeśli nie możesz zaczekać 14 dni, [Wyłącz usuwanie nietrwałe](#enabling-and-disabling-soft-delete), Cofnij usunięcie nieusuniętych elementów i usuń je ponownie, aby trwale usunąć. Po upewnieniu się, że nie ma żadnych elementów chronionych i nie usunięto żadnych nietrwałych elementów, można usunąć magazyn.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Czy mogę usunąć dane przed usunięciem z okresu usuwania nietrwałego z 14 dni?

Nie. Nie można wymusić usunięcia nieusuniętych elementów. Są one automatycznie usuwane po 14 dniach. Ta funkcja zabezpieczeń umożliwia ochronę danych kopii zapasowych przed przypadkowym lub złośliwym usunięciem.  Należy poczekać 14 dni przed wykonaniem jakiejkolwiek innej akcji dotyczącej elementu.  Elementy usunięte nietrwale nie będą obciążane opłatami.  Jeśli musisz ponownie włączyć ochronę elementów oznaczonych do usunięcia nietrwałego w ciągu 14 dni w nowym magazynie, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Czy można wykonywać operacje usuwania nietrwałego w programie PowerShell lub interfejsie wiersza polecenia?

Operacje usuwania nietrwałego można wykonać przy użyciu programu PowerShell. Obecnie interfejs wiersza polecenia nie jest obsługiwany.

## <a name="next-steps"></a>Następne kroki

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](security-overview.md)
