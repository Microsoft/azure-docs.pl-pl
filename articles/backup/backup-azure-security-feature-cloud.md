---
title: Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze
description: Dowiedz się, jak używać funkcji zabezpieczeń w programie Azure Backup, aby tworzyć kopie zapasowe bardziej bezpieczne.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668738"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze korzystających z Azure Backup

Problemy dotyczące zabezpieczeń, takie jak złośliwe oprogramowanie, programy wymuszającego okup i wtargnięcie, zwiększają się. Te problemy z zabezpieczeniami mogą być kosztowne, w odniesieniu do pieniędzy i danych. Aby ochronić przed takimi atakami, Azure Backup teraz zapewnia funkcje zabezpieczeń, które ułatwiają ochronę danych kopii zapasowej nawet po usunięciu.

Jedną z tych funkcji jest usuwanie nietrwałe. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa kopię zapasową maszyny wirtualnej (lub dane kopii zapasowej są przypadkowo usuwane), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z kosztem dla klienta. Platforma Azure szyfruje także wszystkie dane kopii zapasowej przechowywane przy użyciu [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) , aby dodatkowo zabezpieczyć dane.

Ochrona usuwania nietrwałego dla usługi Azure Virtual Machines jest ogólnie dostępna.

>[!NOTE]
>Nietrwałe usuwanie programu SQL Server na maszynie wirtualnej platformy Azure oraz usuwanie nietrwałe dla SAP HANA w obciążeniach maszyn wirtualnych platformy Azure jest teraz dostępne w wersji zapoznawczej.<br>
>Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas naAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Usuwanie nietrwałe

### <a name="soft-delete-for-vms"></a>Usuwanie nietrwałe dla maszyn wirtualnych

Usuwanie nietrwałe dla maszyn wirtualnych chroni kopie zapasowe maszyn wirtualnych przed niezamierzonym usunięciem. Nawet po usunięciu kopii zapasowych są one zachowywane w stanie nietrwałego usunięcia przez 14 dodatkowych dni.

> [!NOTE]
> Usuwanie nietrwałe chroni tylko usunięte dane kopii zapasowej. Jeśli maszyna wirtualna zostanie usunięta bez kopii zapasowej, funkcja usuwania nietrwałego nie będzie zachować danych. Wszystkie zasoby powinny być chronione za pomocą Azure Backup, aby zapewnić pełną odporność.
>

### <a name="supported-regions"></a>Obsługiwane regiony

Usuwanie nietrwałe jest obecnie obsługiwane w zachodnich środkowe stany USA, Azja Wschodnia, Kanada środkowa, Kanada Wschodnia, Francja środkowa, Francja Południowa, Korea środkowa, Korea Południowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Australia Wschodnia, Australia Południowo-Wschodnia, Południowe stany USA, zachodnie stany USA, Europa Zachodnia, południowe stany USA, Indie Południowe, Indie Zachodnie, Japonia południowo Azja Wschodnia-Wschodnia , Szwajcaria Północna, Szwajcaria Zachodnia, Norwegia Zachodnia, Norwegia Wschodnia i wszystkie regiony narodowe.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu Azure Portal

1. Aby usunąć dane kopii zapasowej maszyny wirtualnej, należy zatrzymać wykonywanie kopii zapasowej. W Azure Portal przejdź do magazynu usługi Recovery Services, kliknij prawym przyciskiem myszy element kopii zapasowej, a następnie wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej**.

   ![Zrzut ekranu przedstawiający elementy Azure Portal kopii zapasowej](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. W poniższym oknie zostanie wybrana opcja usuwania lub zachowywania danych kopii zapasowej. W przypadku wybrania opcji **Usuń dane kopii zapasowej** , a następnie **zatrzymania wykonywania**kopii zapasowej kopia zapasowa maszyny wirtualnej nie zostanie trwale usunięta. Zamiast tego dane kopii zapasowej będą przechowywane przez 14 dni w stanie nietrwałego usunięcia. W przypadku wybrania opcji **Usuń dane kopii zapasowej** do skonfigurowanego identyfikatora poczty e-mail zostanie wysłany alert z informacją o tym, że 14 dni pozostaną Rozszerzone przechowywanie danych kopii zapasowej. Ponadto w 12-dniowym dniu jest wysyłany alert e-mail z informacją o tym, że pozostały jeszcze dwa dni, aby przywracania aktywności usunięte dane. Usuwanie zostanie odroczone do 15-dniowego dnia, po upływie którego nastąpi trwałe usunięcie i zostanie wysłany końcowy alert e-mail z informacją o trwałym usunięciu danych.

   ![Zrzut ekranu przedstawiający ekran Azure Portal, Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. W ciągu 14 dni w magazynie Recovery Services nietrwałe usunięte maszyny wirtualne będą wyświetlane z czerwoną ikoną "miękki-Delete".

   ![Zrzut ekranu przedstawiający Azure Portal, maszynę wirtualną w stanie usuwania nietrwałego](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Jeśli w magazynie znajdują się jakiekolwiek nietrwałe usunięte elementy kopii zapasowej, nie można w tym momencie usunąć magazynu. Spróbuj usunąć magazyn po usunięciu trwałych elementów kopii zapasowej. w magazynie nie ma żadnego elementu usuniętego nietrwałego stanu.

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

Ten wykres przepływu przedstawia różne kroki i Stany elementu kopii zapasowej po włączeniu usuwania nietrwałego:

![Cykl życia nietrwałego usuniętego elementu kopii zapasowej](./media/backup-azure-security-feature-cloud/lifecycle.png)

Aby uzyskać więcej informacji, zobacz sekcję [często zadawane pytania](backup-azure-security-feature-cloud.md#frequently-asked-questions) poniżej.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu Azure PowerShell

> [!IMPORTANT]
> Wersja AZ. RecoveryServices wymagana do użycia nietrwałego usuwania przy użyciu narzędzia Azure PS to minimalna 2.2.0. Użyj ```Install-Module -Name Az.RecoveryServices -Force``` , aby uzyskać najnowszą wersję.

Jak opisano powyżej dla Azure Portal, sekwencja kroków jest taka sama, ale również przy użyciu Azure PowerShell.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Usuń element kopii zapasowej za pomocą Azure PowerShell

Usuń element kopii zapasowej za pomocą polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

"DeleteState" elementu kopii zapasowej zmieni się z "NotDeleted" na "ToBeDeleted". Dane kopii zapasowej będą przechowywane przez 14 dni. Jeśli chcesz przywrócić operację usuwania, należy wykonać Cofnięcie usunięcia.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Cofanie operacji usuwania przy użyciu Azure PowerShell

Najpierw Pobierz odpowiedni element kopii zapasowej, który jest w stanie usuwania nietrwałego (to oznacza, że zostanie usunięty).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Następnie wykonaj operację cofania usuwania przy użyciu polecenia cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

"DeleteState" elementu kopii zapasowej zostanie przywrócony do "NotDeleted". Jednak ochrona jest nadal zatrzymana. [Wznów tworzenie kopii zapasowej,](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) aby ponownie włączyć ochronę.

### <a name="soft-delete-for-vms-using-rest-api"></a>Usuwanie nietrwałe dla maszyn wirtualnych przy użyciu interfejsu API REST

- Usuń kopie zapasowe przy użyciu interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Jeśli użytkownik chce cofnąć te operacje usuwania, należy zapoznać się z krokami opisanymi [tutaj](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Wyłączanie usuwania nietrwałego

Usuwanie nietrwałe jest domyślnie włączone dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem.  Wyłączenie tej funkcji nie jest zalecane. Jedyną okolicznością, w której należy rozważyć wyłączenie usuwania nietrwałego, jest to, że planujesz przeniesienie chronionych elementów do nowego magazynu i nie będzie można odczekać 14 dni przed usunięciem i ponownym włączeniem ochrony (na przykład w środowisku testowym). Tylko właściciel magazynu może wyłączyć tę funkcję. Jeśli wyłączysz tę funkcję, wszystkie przyszłe usunięcia chronionych elementów spowodują natychmiastowe usunięcie, bez możliwości przywrócenia. Dane kopii zapasowej, które istnieją w stanie nietrwałego usunięcia przed wyłączeniem tej funkcji, pozostaną w stanie nietrwałego usunięcia przez 14 dni. Jeśli chcesz trwale usunąć te elementy natychmiast, musisz cofnąć ich usunięcie i usunąć je ponownie, aby trwale usunąć.

### <a name="disabling-soft-delete-using-azure-portal"></a>Wyłączanie usuwania nietrwałego przy użyciu Azure Portal

Aby wyłączyć usuwanie nietrwałe, wykonaj następujące kroki:

1. W Azure Portal przejdź do magazynu, a następnie przejdź do pozycji **Ustawienia** -> **Właściwości**.
2. W okienku właściwości wybierz pozycję Aktualizacja **ustawień** -> **Update**zabezpieczeń.  
3. W okienku ustawienia zabezpieczeń w obszarze **usuwanie nietrwałe**wybierz pozycję **Wyłącz**.

![Wyłącz usuwanie nietrwałe](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Wyłączanie usuwania nietrwałego przy użyciu Azure PowerShell

> [!IMPORTANT]
> Wersja AZ. RecoveryServices wymagana do użycia nietrwałego usuwania przy użyciu narzędzia Azure PS to minimalna 2.2.0. Użyj ```Install-Module -Name Az.RecoveryServices -Force``` , aby uzyskać najnowszą wersję.

Aby wyłączyć, użyj polecenia cmdlet [Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

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

1. Postępuj zgodnie z instrukcjami, aby [wyłączyć usuwanie nietrwałe](#disabling-soft-delete).
2. W Azure Portal przejdź do swojego magazynu, przejdź do **pozycji elementy kopii zapasowej**, a następnie wybierz nietrwałą maszynę wirtualną.

   ![Wybierz nietrwałe usunięte maszyny wirtualne](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Wybierz opcję **Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Zostanie wyświetlone okno. Wybierz pozycję **Cofnij usunięcie**.

   ![Wybierz pozycję Cofnij usunięcie](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Wybierz pozycję **Usuń dane kopii zapasowej** , aby trwale usunąć dane kopii zapasowej.

   ![Wybierz pozycję Usuń dane kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

   ![Wpisz nazwę elementu kopii zapasowej](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

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

1. Najpierw Cofnij operacje usuwania, wykonując kroki opisane [tutaj](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Następnie wyłącz funkcje usuwania nietrwałego za pomocą interfejsu API REST, wykonując kroki opisane [tutaj](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Następnie usuń kopie zapasowe przy użyciu interfejsu API REST, jak wspomniano [tutaj](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Szyfrowanie

Wszystkie dane kopii zapasowej są automatycznie szyfrowane, gdy są przechowywane w chmurze przy użyciu szyfrowania usługi Azure Storage, co pomaga sprostać zobowiązaniom dotyczącym zabezpieczeń i zgodności. Te dane przechowywane są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2.

Oprócz szyfrowania przechowywane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Zapoznaj się z [Azure Backup często zadawanych](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) pytań, aby odpowiedzieć na pytania dotyczące szyfrowania.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Nie musisz podejmować żadnych działań jawnych, aby włączyć to szyfrowanie i ma zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Podczas tworzenia kopii zapasowej Virtual Machines platformy Azure można teraz szyfrować dane przy użyciu kluczy należących do Ciebie i zarządzanych przez użytkownika. Azure Backup umożliwia korzystanie z kluczy RSA przechowywanych w Azure Key Vault na potrzeby szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256, który jest z kolei chroniony przy użyciu kluczy. Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, wymagane jest przyznanie magazynowi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie konieczności można wyłączyć klucz lub odwołać dostęp. Należy jednak włączyć szyfrowanie przy użyciu kluczy przed podjęciem próby włączenia ochrony wszystkich elementów do magazynu.

>[!NOTE]
>Ta funkcja jest obecnie w ograniczonej dostępności. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) i Wyślij wiadomość e-mail na AskAzureBackupTeam@microsoft.com adres, jeśli chcesz zaszyfrować dane kopii zapasowej przy użyciu kluczy zarządzanych przez klienta. Należy zauważyć, że możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Kopia zapasowa maszyn wirtualnych z dyskami zarządzanymi szyfrowanymi przy użyciu kluczy zarządzanych przez klienta

Azure Backup umożliwia również tworzenie kopii zapasowych maszyn wirtualnych platformy Azure, które używają klucza do szyfrowania po stronie serwera. Klucz używany do szyfrowania dysków jest przechowywany w Azure Key Vault i zarządzane przez Ciebie. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta różni się od Azure Disk Encryption, ponieważ w programie ADE są używane funkcje BitLocker (dla systemu Windows) i DM-Crypt (w systemie Linux) w celu przeprowadzenia szyfrowania w trybie gościnnym, funkcja SSE szyfruje dane w usłudze Storage, umożliwiając korzystanie z dowolnego systemu operacyjnego lub obrazów dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków zarządzanych przy użyciu kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

### <a name="backup-of-vms-encrypted-using-ade"></a>Tworzenie kopii zapasowych maszyn wirtualnych zaszyfrowanych przy użyciu systemu ADE

Za pomocą Azure Backup można także tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które mają zaszyfrowany system operacyjny lub dyski danych przy użyciu Azure Disk Encryption. ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows i programu DM-Crypt for Linux, aby można było przeprowadzić szyfrowanie w gościu. Aby uzyskać szczegółowe informacje, zobacz [wykonywanie kopii zapasowych i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Inne funkcje zabezpieczeń

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrona punktów odzyskiwania Azure Backup

Konta magazynu używane przez magazyny usługi Recovery Services są izolowane i nie są dostępne dla użytkowników w przypadku jakichkolwiek złośliwych celów. Dostęp jest dozwolony tylko za pomocą Azure Backup operacji zarządzania, takich jak przywracanie. Te operacje zarządzania są kontrolowane za pośrednictwem Access Control opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [używanie Access Control opartej na rolach w celu zarządzania Azure Backup punktów odzyskiwania](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="for-soft-delete"></a>W przypadku usuwania nietrwałego

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Czy muszę włączyć funkcję usuwania nietrwałego dla każdego magazynu?

Nie. jest ona domyślnie skompilowana i włączona dla wszystkich magazynów usługi Recovery Services.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Czy można skonfigurować liczbę dni, przez jaką dane będą przechowywane w stanie nieusuniętym, po zakończeniu operacji usuwania?

Nie, po operacji usuwania zostanie ustalone po 14 dniach dodatkowego okresu przechowywania.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Czy muszę płacić za to dodatkowe 14 dni?

Nie, to 14-dniowe dodatkowe przechowywanie jest bezpłatne w ramach funkcji usuwania nietrwałego.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Czy można wykonać operację przywracania, gdy moje dane są w stanie usuwania nietrwałego?

Nie, Usuń nietrwały zasób nietrwałego w celu przywrócenia. Operacja cofnięcia usunięcia spowoduje przywrócenie zasobu z powrotem do **stanu Zatrzymaj ochronę przy zachowaniu danych** , w którym można przywrócić wszystkie punkty w czasie. Moduł wyrzucania elementów bezużytecznych pozostaje wstrzymany w tym stanie.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Czy moje migawki będą zgodne z tym samym cyklem życia jak moje punkty odzyskiwania w magazynie?

Tak.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak można ponownie wyzwolić zaplanowane kopie zapasowe dla nietrwałego zasobu?

Cofnięcie usunięcia po wykonaniu operacji Wznów spowoduje ponowne włączenie ochrony zasobu. Operacja Wznów kojarzy zasady tworzenia kopii zapasowych, aby wyzwolić zaplanowane kopie zapasowe z wybranym okresem przechowywania. Ponadto Moduł wyrzucania elementów bezużytecznych jest uruchamiany zaraz po zakończeniu operacji wznawiania. Jeśli chcesz wykonać przywracanie z punktu odzyskiwania, który przekroczył datę wygaśnięcia, zaleca się wykonanie tej czynności przed wyzwoleniem operacji wznowienia.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Czy mogę usunąć swój magazyn, jeśli w magazynie istnieją nietrwałe elementy usunięte?

Nie można usunąć magazynu Recovery Services, jeśli w magazynie istnieją elementy kopii zapasowej w stanie nieusuniętym. Usunięte elementy nietrwałe są usuwane przez 14 dni po operacji usuwania. Jeśli nie możesz zaczekać 14 dni, [Wyłącz usuwanie nietrwałe](#disabling-soft-delete), Cofnij usunięcie nieusuniętych elementów i usuń je ponownie, aby trwale usunąć. Po upewnieniu się, że nie ma żadnych elementów chronionych i nie usunięto żadnych nietrwałych elementów, można usunąć magazyn.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Czy mogę usunąć dane przed usunięciem z okresu usuwania nietrwałego z 14 dni?

Nie. Nie można wymusić usunięcia nieusuniętych elementów, które są automatycznie usuwane po 14 dniach. Ta funkcja zabezpieczeń umożliwia ochronę danych kopii zapasowych przed przypadkowym lub złośliwym usunięciem.  Przed wykonaniem jakiejkolwiek innej akcji na maszynie wirtualnej należy poczekać 14 dni.  Wykorzystano nietrwałe elementy usunięte ".  Jeśli potrzebujesz ponownie chronić maszyny wirtualne oznaczone do usunięcia nietrwałego w ciągu 14 dni do nowego magazynu, skontaktuj się z pomocą techniczną firmy Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Czy można wykonywać operacje usuwania nietrwałego w programie PowerShell lub interfejsie wiersza polecenia?

Operacje usuwania nietrwałego można wykonać przy użyciu [programu PowerShell](#soft-delete-for-vms-using-azure-powershell). Obecnie interfejs wiersza polecenia nie jest obsługiwany.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Czy usuwanie nietrwałe jest obsługiwane dla innych obciążeń w chmurze, takich jak SQL Server na maszynach wirtualnych platformy Azure i SAP HANA na maszynach wirtualnych platformy Azure?

Nie. Obecnie usuwanie nietrwałe jest obsługiwane tylko dla maszyn wirtualnych platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [kontrolkach zabezpieczeń dla Azure Backup](backup-security-controls.md).
