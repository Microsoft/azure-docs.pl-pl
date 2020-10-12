---
title: Tworzenie kopii zapasowych i odzyskiwanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Zawiera opis sposobu tworzenia kopii zapasowych i odzyskiwania maszyn wirtualnych platformy Azure przy użyciu Azure Backup programu PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 87d655652d0207a50f8980f18d18e76fea0b1e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975093"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

W tym artykule wyjaśniono, jak utworzyć kopię zapasową i przywrócić maszynę wirtualną platformy Azure w magazynie [Azure Backup](backup-overview.md) Recovery Services przy użyciu poleceń cmdlet programu PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Utwórz magazyn Recovery Services i ustaw kontekst magazynu.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwól zadanie tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych zanim będzie można utworzyć kopię zapasową (lub chronić) maszyny wirtualnej, musisz spełnić [wymagania wstępne](backup-azure-arm-vms-prepare.md) , aby przygotować środowisko do ochrony maszyn wirtualnych.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach Recovery Services.
* [Zapoznaj](backup-architecture.md#architecture-built-in-azure-vm-backup) się z architekturą kopii zapasowej maszyny wirtualnej platformy Azure, [zapoznaj się](backup-azure-vms-introduction.md) z procesem tworzenia kopii zapasowej oraz [Przejrzyj](backup-support-matrix-iaas.md) pomoc techniczną, ograniczenia i wymagania wstępne.
* Zapoznaj się z hierarchią obiektów programu PowerShell dla Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów Recovery Services

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zapoznaj się z dokumentacją [polecenia cmdlet](/powershell/module/az.recoveryservices/) **AZ. RecoveryServices** w bibliotece platformy Azure.

## <a name="set-up-and-register"></a>Skonfiguruj i zarejestruj

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](/powershell/azure/install-az-ps)

2. Znajdź Azure Backup polecenia cmdlet programu PowerShell, wpisując następujące polecenie:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Zostaną wyświetlone aliasy i polecenia cmdlet dla Azure Backup, Azure Site Recovery i magazynu Recovery Services. Na poniższej ilustracji przedstawiono przykład tego, co widzisz. Nie jest to kompletna lista poleceń cmdlet.

    ![Lista Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Zaloguj się do konta platformy Azure przy użyciu polecenia **Connect-AzAccount**. To polecenie cmdlet wyświetla stronę sieci Web z prośbą o poświadczenia konta:

    * Alternatywnie można uwzględnić poświadczenia konta jako parametr w poleceniu cmdlet **Connect-AzAccount** przy użyciu parametru **-Credential** .
    * Jeśli jesteś partnerem programu CSP działającym w imieniu dzierżawy, określ klienta jako dzierżawcę przy użyciu nazwy domeny głównej tenantID lub dzierżawcy. Na przykład: **Connect-AzAccount-dzierżawca "fabrikam.com"**

4. Skojarz subskrypcję, której chcesz używać z kontem, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz Azure Backup po raz pierwszy, musisz użyć polecenia cmdlet **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** , aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Można sprawdzić, czy dostawcy zarejestrowali się pomyślnie, przy użyciu następujących poleceń:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    W danych wyjściowych polecenia **RegistrationState** należy zmienić na **zarejestrowane**. W przeciwnym razie po prostu uruchom ponownie polecenie cmdlet **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** .

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki umożliwiają utworzenie magazynu Recovery Services. Magazyn Recovery Services jest inny niż magazyn kopii zapasowych.

1. Magazyn Recovery Services jest zasobem Menedżer zasobów, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć grupę zasobów za pomocą polecenia cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** . Podczas tworzenia grupy zasobów Określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) , aby utworzyć magazyn Recovery Services. Należy określić tę samą lokalizację dla magazynu, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości magazynu, który ma być używany. Można używać magazynu [lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)lub [magazynu Strefowo nadmiarowego (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). W poniższym przykładzie pokazano opcję **-BackupStorageRedundancy** dla *testvault* z ustawioną **geomiarowo**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodnie jest przechowywać obiekt magazynu Recovery Services kopii zapasowej w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w subskrypcji, użyj polecenie [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do poniższego przykładu, Zauważ, że skojarzone ResourceGroupName i lokalizacja są podane.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Użyj magazynu Recovery Services, aby chronić maszyny wirtualne. Przed zastosowaniem ochrony ustaw kontekst magazynu (typ danych chronionych w magazynie) i Sprawdź zasady ochrony. Zasady ochrony to harmonogram, w którym uruchamiane są zadania tworzenia kopii zapasowej, oraz czas zachowywania każdej migawki kopii zapasowej.

### <a name="set-vault-context"></a>Ustaw kontekst magazynu

Przed włączeniem ochrony na maszynie wirtualnej Użyj polecenie [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) , aby ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. Poniższy przykład ustawia kontekst magazynu dla magazynu, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobierz identyfikator magazynu

Planujemy zaniechanie ustawienia kontekstu magazynu zgodnie z zaleceniami Azure PowerShell. Zamiast tego można zapisać lub pobrać identyfikator magazynu i przekazać go do odpowiednich poleceń. Jeśli więc nie ustawisz kontekstu magazynu lub chcesz określić polecenie do uruchomienia dla pewnego magazynu, Przekaż identyfikator magazynu jako "-vaultID" do wszystkich odpowiednich poleceń w następujący sposób:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Lub

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modyfikowanie ustawień replikacji magazynu

Użyj polecenia [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) , aby ustawić konfigurację replikacji magazynu dla magazynu na LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Nadmiarowość magazynu można modyfikować tylko wtedy, gdy nie ma żadnych elementów kopii zapasowych chronionych do magazynu.

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony

Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Możesz użyć domyślnych zasad, aby szybko chronić maszynę wirtualną i edytować zasady później z innymi szczegółami.

Użyj **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** , aby wyświetlić zasady ochrony dostępne w magazynie. Tego polecenia cmdlet można użyć w celu uzyskania określonych zasad lub wyświetlenia zasad skojarzonych z typem obciążenia. Poniższy przykład pobiera zasady dla typu obciążenia, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Strefa czasowa pola nieprzerwanego działania w programie PowerShell jest UTC. Jeśli jednak w Azure Portal zostanie wyświetlony czas tworzenia kopii zapasowej, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

Zasady ochrony kopii zapasowej są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określają czas przechowywania punktu odzyskiwania przed jego usunięciem.

* Użyj [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) , aby wyświetlić domyślne zasady przechowywania.
* Podobnie można użyć [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) , aby uzyskać domyślne zasady harmonogramu.
* Polecenie cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) tworzy obiekt programu PowerShell, który zawiera informacje o zasadach kopii zapasowych.
* Obiekty zasad harmonogramu i przechowywania są używane jako dane wejściowe polecenia cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Domyślnie w obiekcie zasad harmonogramu jest zdefiniowany czas rozpoczęcia. Użyj poniższego przykładu, aby zmienić godzinę rozpoczęcia na żądaną godzinę rozpoczęcia. Wymagana godzina rozpoczęcia powinna być również w formacie UTC. W poniższym przykładzie przyjęto założenie, że żądany czas rozpoczęcia to 01:00 czasu UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać godzinę rozpoczęcia tylko w ciągu 30 minut. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Godzina rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie są przechowywane zasady harmonogramu i zasady przechowywania w zmiennych. W przykładzie zastosowano te zmienne do definiowania parametrów podczas tworzenia zasad ochrony, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony nadal należy włączyć zasady dla elementu. Aby włączyć ochronę, użyj [enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) . Włączenie ochrony wymaga dwóch obiektów — elementu i zasad. Po skojarzeniu zasad z magazynem przepływ pracy tworzenia kopii zapasowej jest wyzwalany w czasie zdefiniowanym w harmonogramie zasad.

> [!IMPORTANT]
> Podczas korzystania z programu PowerShell w celu jednoczesnego włączenia tworzenia kopii zapasowych wielu maszyn wirtualnych upewnij się, że z jednymi zasadami nie są skojarzone więcej niż 100 maszyn wirtualnych. Jest to [zalecane najlepsze rozwiązanie](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy). Obecnie klient programu PowerShell nie jest jawnie blokowany, jeśli istnieje więcej niż 100 maszyn wirtualnych, ale w przyszłości sprawdzanie jest planowane.

W poniższych przykładach włączono ochronę dla elementu, V2VM, przy użyciu zasad, NewPolicy. Przykłady różnią się w zależności od tego, czy maszyna wirtualna jest zaszyfrowana, oraz jakiego typu szyfrowanie.

Aby włączyć ochronę na **niezaszyfrowanych maszynach wirtualnych Menedżer zasobów**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Aby włączyć ochronę zaszyfrowanych maszyn wirtualnych (zaszyfrowanych przy użyciu klucz szyfrowania bloków i KEK), należy przyznać usłudze Azure Backup uprawnienia do odczytu kluczy i wpisów tajnych z magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Aby włączyć ochronę **szyfrowanych maszyn wirtualnych (szyfrowanych tylko przy użyciu programu klucz szyfrowania bloków)**, należy nadać usłudze Azure Backup uprawnienia do odczytywania wpisów tajnych z magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Jeśli używasz chmury Azure Government, użyj wartości `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` parametru **ServicePrincipalName** w poleceniu cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Można monitorować długotrwałe operacje, takie jak zadania tworzenia kopii zapasowej, bez użycia Azure Portal. Aby uzyskać stan zadania w toku, należy użyć polecenia cmdlet [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . To polecenie cmdlet pobiera zadania tworzenia kopii zapasowej dla określonego magazynu i ten magazyn jest określony w kontekście magazynu. Poniższy przykład pobiera stan zadania w toku jako tablicę i zapisuje stan w zmiennej $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania tych zadań do zakończenia — nie jest to zbędny dodatkowy kod, użyj polecenia cmdlet [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . To polecenie cmdlet wstrzymuje wykonywanie do momentu zakończenia zadania lub osiągnięcia określonej wartości limitu czasu.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

### <a name="modify-a-protection-policy"></a>Modyfikowanie zasad ochrony

Aby zmodyfikować zasady ochrony, użyj polecenie [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) w celu zmodyfikowania obiektów SchedulePolicy lub RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modyfikowanie zaplanowanego czasu

Podczas tworzenia zasad ochrony domyślnie przypisywany jest czas rozpoczęcia. W poniższych przykładach pokazano, jak zmodyfikować godzinę rozpoczęcia zasad ochrony.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modyfikowanie przechowywania

Poniższy przykład zmienia czas przechowywania punktu odzyskiwania na 365 dni.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurowanie przechowywania migawek natychmiastowego przywracania

> [!NOTE]
> W programie Azure PowerShell w wersji 1.6.0 lub nowszej można zaktualizować okres przechowywania migawki przywracania natychmiastowego w zasadach przy użyciu programu PowerShell.

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Wartość domyślna to 2. Można ustawić wartość minimalną 1 i maksymalnie 5. W przypadku tygodniowych zasad tworzenia kopii zapasowych okres jest ustawiany na 5 i nie można go zmienić.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Tworzenie Azure Backup grupy zasobów podczas przechowywania migawek

> [!NOTE]
> Z Azure PowerShell wersji 3.7.0 lub nowszej można utworzyć i edytować grupę zasobów utworzoną na potrzeby przechowywania natychmiastowych migawek.

Aby dowiedzieć się więcej o regułach tworzenia grupy zasobów i innych istotnych szczegółach, zapoznaj się z dokumentacją [Virtual Machines Azure Backup grupy zasobów](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) .

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="trigger-a-backup"></a>Wyzwalanie kopii zapasowej

Aby wyzwolić zadanie tworzenia kopii zapasowej, użyj [Narzędzia Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . Jeśli jest to początkowa kopia zapasowa, jest to pełna kopia zapasowa. Kolejne kopie zapasowe pobierają przyrostową kopię. W poniższym przykładzie kopia zapasowa maszyny wirtualnej ma być przechowywana przez 60 dni.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Strefa czasowa pól StartTime i EndTime w programie PowerShell jest UTC. Jeśli jednak czas jest pokazywany w Azure Portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

### <a name="change-policy-for-backup-items"></a>Zmień zasady dla elementów kopii zapasowej

Możesz zmodyfikować istniejące zasady lub zmienić zasady kopii zapasowej elementu z Policy1 na Policy2. Aby przełączyć zasady dla elementu kopii zapasowej, należy pobrać odpowiednie zasady i wykonać kopię zapasową, a następnie użyć polecenia [enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) z elementem kopii zapasowej jako parametru.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Polecenie czeka na zakończenie konfigurowania kopii zapasowej i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zatrzymywanie ochrony

#### <a name="retain-data"></a>Zachowywanie danych

Jeśli chcesz zatrzymać ochronę, możesz użyć polecenia cmdlet [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) programu PowerShell. Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane, których kopia zapasowa została utworzona, dopóki nie będzie teraz zachowywane.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywane dane kopii zapasowej w magazynie, należy dodać flagę "-RemoveRecoveryPoints"/przełącznik do [polecenia "Disable" ochrony](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny wirtualnej platformy Azure

Istnieje ważna różnica między przywracaniem maszyny wirtualnej przy użyciu Azure Portal i przywracaniem maszyny wirtualnej przy użyciu programu PowerShell. W programie PowerShell operacja przywracania została zakończona po utworzeniu informacji o dyskach i konfiguracji z punktu odzyskiwania. Operacja przywracania nie tworzy maszyny wirtualnej. Aby utworzyć maszynę wirtualną z dysku, zapoznaj się z sekcją [Tworzenie maszyny wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Jeśli nie chcesz przywrócić całej maszyny wirtualnej, ale chcesz przywrócić lub odzyskać kilka plików z kopii zapasowej maszyny wirtualnej platformy Azure, zapoznaj się z [sekcją odzyskiwanie plików](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operacja przywracania nie tworzy maszyny wirtualnej.
>
>

Poniższa ilustracja przedstawia hierarchię obiektów od RecoveryServicesVault do BackupRecoveryPoint.

![Hierarchia obiektów Recovery Services wyświetlana BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, zidentyfikuj element kopii zapasowej i punkt odzyskiwania, który zawiera dane punktu w czasie. Aby przywrócić dane z magazynu do konta, użyj [instrukcji RESTORE-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) .

Podstawowe kroki przywracania maszyny wirtualnej platformy Azure:

* Wybierz maszynę wirtualną.
* Wybierz punkt odzyskiwania.
* Przywróć dyski.
* Utwórz maszynę wirtualną z przechowywanych dysków.

### <a name="select-the-vm-when-restoring-files"></a>Wybierz maszynę wirtualną (podczas przywracania plików)

Aby uzyskać obiekt programu PowerShell, który identyfikuje właściwy element kopii zapasowej, Rozpocznij od kontenera w magazynie i pracuj w sposób określony w hierarchii obiektów. Aby wybrać kontener reprezentujący maszynę wirtualną, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) i potoku do polecenia cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Wybierz punkt odzyskiwania (podczas przywracania plików)

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) , aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli nie masz pewności, który punkt odzyskiwania jest używany, dobrym sposobem jest wybranie najnowszego punktu RecoveryPointType = AppConsistent z listy.

W poniższym skrypcie zmienna **$RP**jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej w ciągu ostatnich siedmiu dni. Tablica jest posortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania pod indeksem 0. Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W przykładzie $rp [0] wybiera najnowszy punkt odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Przywracanie dysków

Za pomocą polecenia cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) Przywróć dane i konfigurację elementu kopii zapasowej w punkcie odzyskiwania. Po zidentyfikowaniu punktu odzyskiwania Użyj go jako wartości parametru **-RecoveryPoint** . W powyższym przykładzie **$RP [0]** był punktem odzyskiwania do użycia. W poniższym przykładowym kodzie **$RP [0]** jest punktem odzyskiwania używanym do przywracania dysku.

Aby przywrócić dyski i informacje o konfiguracji:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Przywracanie dysków zarządzanych

> [!NOTE]
> Jeśli maszyna wirtualna z kopią zapasową ma dyski zarządzane i chcesz je przywrócić jako dyski zarządzane, dodaliśmy możliwość usługi Azure PowerShell RM module 6.7.0. lub nowszy.
>
>

Podaj dodatkowy parametr **TargetResourceGroupName** , aby określić RG, do którego zostaną przywrócone dyski zarządzane.

> [!IMPORTANT]
> Zdecydowanie zaleca się użycie **TargetResourceGroupName** parametru do przywracania dysków zarządzanych, ponieważ powoduje to znaczne zwiększenie wydajności. Jeśli ten parametr nie jest określony, nie można korzystać z funkcji natychmiastowego przywracania, a operacja przywracania będzie wolniejsza w porównaniu z wynikami. Jeśli celem jest przywrócenie dysków zarządzanych jako dysków niezarządzanych, nie należy podawać tego parametru i uczynić zamierzonym zamiarem, podając `-RestoreAsUnmanagedDisks` parametr. `-RestoreAsUnmanagedDisks`Parametr jest dostępny w Azure PowerShell 3.7.0 lub nowszym. W przyszłych wersjach będzie wymagane podanie dowolnego z tych parametrów dla odpowiedniego środowiska przywracania.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**VMConfig.JS** pliku zostanie przywrócony do konta magazynu, a dyski zarządzane zostaną przywrócone do określonego elementu docelowego RG.

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Za pomocą polecenia cmdlet [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Zaczekaj na zakończenie zadania przywracania.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po zakończeniu zadania przywracania należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) , aby uzyskać szczegółowe informacje o operacji przywracania. Właściwość JobDetails ma informacje konieczne do odbudowania maszyny wirtualnej.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Po przywróceniu dysków przejdź do następnej sekcji, aby utworzyć maszynę wirtualną.

## <a name="replace-disks-in-azure-vm"></a>Zastąp dyski na maszynie wirtualnej platformy Azure

Aby zastąpić dyski i informacje o konfiguracji, wykonaj następujące czynności:

* Krok 1. [przywracanie dysków](backup-azure-vms-automation.md#restore-the-disks)
* Krok 2. [Odłączanie dysku danych przy użyciu programu PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Krok 3: [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Tworzenie maszyny wirtualnej na podstawie przywróconych dysków

Po przywróceniu dysków wykonaj następujące kroki, aby utworzyć i skonfigurować maszynę wirtualną z dysku.

> [!NOTE]
>
> 1. Wymagany jest moduł AzureAz w wersji 3.0.0 lub nowszej. <br>
> 2. Aby można było tworzyć zaszyfrowane maszyny wirtualne na przywróconych dyskach, rola platformy Azure musi mieć uprawnienia do wykonywania akcji, usługi **Microsoft./magazynów/magazynu/wdrażania/działania**. Jeśli rola nie ma tego uprawnienia, Utwórz rolę niestandardową przy użyciu tej akcji. Aby uzyskać więcej informacji, zobacz [role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md). <br>
> 3. Po przywróceniu dysków można teraz uzyskać szablon wdrożenia, za pomocą którego można bezpośrednio utworzyć nową maszynę wirtualną. Nie potrzebujesz innych poleceń cmdlet programu PowerShell do tworzenia zarządzanych/niezarządzanych maszyn wirtualnych, które są szyfrowane/nieszyfrowane.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu wdrożenia

Szczegóły zadania wynikowego zawierają identyfikator URI szablonu, który można zbadać i wdrożyć.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Szablon nie jest dostępny bezpośrednio, ponieważ znajduje się na koncie magazynu klienta i podanym kontenerze. Do uzyskania dostępu do tego szablonu potrzebny jest pełny adres URL (wraz z tymczasowym tokenem SAS).

1. Najpierw Wyodrębnij nazwę szablonu z templateBlobURI. Ten format jest wymieniony poniżej. Możesz użyć operacji Split w programie PowerShell, aby wyodrębnić nazwę szablonu końcowego z tego adresu URL.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Następnie można wygenerować pełny adres URL, jak wyjaśniono [tutaj](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment).

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Wdróż szablon, aby utworzyć nową maszynę wirtualną, jak wyjaśniono [tutaj](../azure-resource-manager/templates/deploy-powershell.md).

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>Tworzenie maszyny wirtualnej przy użyciu pliku konfiguracji

W poniższej sekcji przedstawiono kroki niezbędne do utworzenia maszyny wirtualnej przy użyciu pliku "VMConfig".

> [!NOTE]
> Zdecydowanie zaleca się użycie szablonu wdrożenia szczegółowego powyżej, aby utworzyć maszynę wirtualną. Ta sekcja (punkty 1-6) zostanie wkrótce wycofana.

1. Wykonaj zapytanie dotyczące przywróconych właściwości dysku w celu uzyskania szczegółowych informacji o zadaniu.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Ustaw kontekst usługi Azure Storage i Przywróć plik konfiguracji JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Użyj pliku konfiguracji JSON, aby utworzyć konfigurację maszyny wirtualnej.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Dołącz dysk systemu operacyjnego i dyski z danymi. Ten krok zawiera przykłady dla różnych konfiguracji zarządzanych i szyfrowanych maszyn wirtualnych. Użyj przykładu pasującego do konfiguracji maszyny wirtualnej.

    * **Niezarządzane i nieszyfrowane maszyny wirtualne** — Użyj poniższego przykładu dla niezarządzanych, nieszyfrowanych maszyn wirtualnych.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niezarządzane i zaszyfrowane maszyny wirtualne z usługą Azure AD (tylko klucz szyfrowania bloków)** — dla niezarządzanych, szyfrowanych maszyn wirtualnych z usługą Azure AD (zaszyfrowane tylko przy użyciu klucz szyfrowania bloków) przed dołączeniem dysków należy przywrócić klucz tajny do magazynu kluczy. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanej maszyny wirtualnej z Azure Backup punktu odzyskiwania](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych. Podczas ustawiania dysku systemu operacyjnego upewnij się, że wspominasz o odpowiednim typie systemu operacyjnego.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niezarządzane i zaszyfrowane maszyny wirtualne z usługą Azure AD (klucz szyfrowania bloków i KEK)** — w przypadku niezarządzanych, szyfrowanych maszyn wirtualnych z usługą Azure AD (zaszyfrowane przy użyciu klucz szyfrowania bloków i KEK) przed dołączeniem dysków Przywróć klucz i klucz tajny magazynu kluczy. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanej maszyny wirtualnej z punktu odzyskiwania Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niezarządzane i zaszyfrowane maszyny wirtualne bez usługi Azure AD (tylko klucz szyfrowania bloków)** — dla niezarządzanych, szyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych tylko za pomocą klucz szyfrowania bloków), Jeśli źródłowy **Magazyn kluczy/klucz tajny nie są dostępne** Przywróć klucze tajne do magazynu klucza przy użyciu procedury [przywracania nieszyfrowanej maszyny wirtualnej z punktu odzyskiwania Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania dla przywróconego obiektu BLOB systemu operacyjnego (ten krok nie jest wymagany dla obiektu BLOB danych). $Dekurl można pobrać z przywróconego magazynu kluczy.

    Następujący skrypt należy wykonać tylko wtedy, gdy źródłowy Magazyn kluczy/wpis tajny jest niedostępny.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Po **udostępnieniu wpisów tajnych** i utworzeniu szczegółów szyfrowania w obiekcie blob systemu operacyjnego Podłącz dyski przy użyciu skryptu podanego poniżej.

    Jeśli źródłowy Magazyn kluczy/wpisy tajne są już dostępne, ten skrypt nie musi być wykonywany.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niezarządzane i zaszyfrowane maszyny wirtualne bez usługi Azure AD (klucz szyfrowania bloków i KEK)** — w przypadku niezarządzanych, szyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych przy użyciu klucz szyfrowania bloków & KEK), Jeśli źródłowy **Magazyn kluczy/klucz/wpis tajny nie są dostępne** Przywróć klucz i klucze tajne do magazynu klucza przy użyciu procedury [przywracania nieszyfrowanej maszyny wirtualnej z Azure Backup punktu odzyskiwania](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania dla przywróconego obiektu BLOB systemu operacyjnego (ten krok nie jest wymagany dla obiektu BLOB danych). $Dekurl i $kekurl można pobrać z przywróconego magazynu kluczy.

    Poniższy skrypt należy wykonać tylko wtedy, gdy źródłowy Magazyn kluczy/klucz/wpis tajny jest niedostępny.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Po **udostępnieniu klucza/wpisów tajnych** i określeniu szczegółów szyfrowania w obiekcie blob systemu operacyjnego Podłącz dyski przy użyciu skryptu podanego poniżej.

    Jeśli źródłowy Magazyn kluczy/klucz tajny jest dostępny, nie trzeba wykonywać powyższego skryptu.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Zarządzane i nieszyfrowane maszyny wirtualne** — w przypadku zarządzanych nieszyfrowanych maszyn wirtualnych Podłącz przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Zarządzane i zaszyfrowane maszyny wirtualne za pomocą usługi Azure AD (tylko klucz szyfrowania bloków)** — w przypadku zarządzanych szyfrowanych maszyn wirtualnych z usługą Azure AD (zaszyfrowane tylko przy użyciu klucz szyfrowania bloków) dołączaj przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Zarządzane i zaszyfrowane maszyny wirtualne za pomocą usługi Azure AD (klucz szyfrowania bloków i KEK)** — w przypadku zarządzanych szyfrowanych maszyn wirtualnych z usługą Azure AD (zaszyfrowane przy użyciu klucz szyfrowania bloków i KEK) Dołącz przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Zarządzane i zaszyfrowane maszyny wirtualne bez usługi Azure AD (tylko klucz szyfrowania bloków)** — w przypadku zarządzanych, szyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych tylko za pomocą klucz szyfrowania bloków), Jeśli źródłowy **Magazyn kluczy/klucz tajny nie są dostępne** Przywróć klucze tajne do magazynu klucza przy użyciu procedury [przywracania nieszyfrowanej maszyny wirtualnej z punktu odzyskiwania Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym dysku systemu operacyjnego (ten krok nie jest wymagany w przypadku dysku danych). $Dekurl można pobrać z przywróconego magazynu kluczy.

    Skrypt poniżej należy wykonać tylko wtedy, gdy źródłowy Magazyn kluczy/wpis tajny jest niedostępny.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Po udostępnieniu wpisów tajnych i wybraniu szczegółowych informacji o szyfrowaniu na dysku systemu operacyjnego w celu dołączenia przywróconych dysków zarządzanych zapoznaj się z tematem [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **Zarządzane i zaszyfrowane maszyny wirtualne bez usługi Azure AD (klucz szyfrowania bloków i KEK)** — w przypadku zarządzanych, szyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych przy użyciu klucz szyfrowania bloków & KEK), Jeśli źródłowy **Magazyn kluczy/klucz/wpis tajny nie są dostępne** Przywróć klucz i wpisy tajne do magazynu klucza przy użyciu procedury [przywracania nieszyfrowanej maszyny wirtualnej z Azure Backup punktu odzyskiwania](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym dysku systemu operacyjnego (ten krok nie jest wymagany dla dysków danych). $Dekurl i $kekurl można pobrać z przywróconego magazynu kluczy.

    Następujący skrypt należy wykonać tylko wtedy, gdy źródłowy Magazyn kluczy/klucz/wpis tajny jest niedostępny.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Po udostępnieniu klucza lub wpisów tajnych na dysku systemu operacyjnego, aby dołączyć przywrócone dyski zarządzane, zobacz [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Ustaw ustawienia sieci.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Utwórz maszynę wirtualną.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Rozszerzenie.
   Jeśli rozszerzenia ADE nie są wypychane, dyski danych zostaną oznaczone jako niezaszyfrowane, więc jest to wymagane w celu wykonania poniższych czynności:

   * **W przypadku maszyny wirtualnej z usługą Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków danych  

     **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **KLUCZ szyfrowania bloków i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **W przypadku maszyny wirtualnej bez usługi Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków danych.

     Jeśli podczas wykonywania polecenia prosi o AADClientID, należy zaktualizować Azure PowerShell.

     **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **KLUCZ szyfrowania bloków i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Pamiętaj o ręcznym usunięciu plików JASON utworzonych w ramach procesu odzyskiwania zaszyfrowanej maszyny wirtualnej.

## <a name="restore-files-from-an-azure-vm-backup"></a>Przywracanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Oprócz przywracania dysków można także przywrócić pojedyncze pliki z kopii zapasowej maszyny wirtualnej platformy Azure. Funkcja przywracania plików zapewnia dostęp do wszystkich plików w punkcie odzyskiwania. Zarządzaj plikami za pomocą Eksploratora plików, tak jak w przypadku zwykłych plików.

Podstawowe kroki przywracania pliku z kopii zapasowej maszyny wirtualnej platformy Azure:

* Wybierz maszynę wirtualną
* Wybierz punkt odzyskiwania
* Instalowanie dysków punktu odzyskiwania
* Kopiuj wymagane pliki
* Odinstalowywanie dysku

### <a name="select-the-vm-when-restoring-the-vm"></a>Wybierz maszynę wirtualną (podczas przywracania maszyny wirtualnej)

Aby uzyskać obiekt programu PowerShell, który identyfikuje właściwy element kopii zapasowej, Rozpocznij od kontenera w magazynie i pracuj w sposób określony w hierarchii obiektów. Aby wybrać kontener reprezentujący maszynę wirtualną, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) i potoku do polecenia cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Wybierz punkt odzyskiwania (podczas przywracania maszyny wirtualnej)

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) , aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli nie masz pewności, który punkt odzyskiwania jest używany, dobrym sposobem jest wybranie najnowszego punktu RecoveryPointType = AppConsistent z listy.

W poniższym skrypcie zmienna **$RP**jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej w ciągu ostatnich siedmiu dni. Tablica jest posortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania pod indeksem 0. Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W przykładzie $rp [0] wybiera najnowszy punkt odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Instalowanie dysków punktu odzyskiwania

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) , aby pobrać skrypt instalujący wszystkie dyski punktu odzyskiwania.

> [!NOTE]
> Dyski są instalowane jako dołączone dyski iSCSI na komputerze, na którym skrypt jest uruchamiany. Instalowanie odbywa się natychmiast, a opłaty nie są naliczane.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Uruchom skrypt na komputerze, na którym chcesz odzyskać pliki. Aby wykonać skrypt, należy wprowadzić podane hasło. Po dołączeniu dysków Użyj Eksploratora plików systemu Windows, aby przeglądać nowe woluminy i pliki. Aby uzyskać więcej informacji, zobacz artykuł Tworzenie kopii zapasowej, [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odinstalowywanie dysków

Po skopiowaniu wymaganych plików Użyj polecenie [disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) , aby odinstalować dyski. Pamiętaj o odinstalowaniu dysków, aby uzyskać dostęp do plików z punktu odzyskiwania.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz używać programu PowerShell do korzystania z zasobów platformy Azure, zapoznaj się z artykułem dotyczącym programu PowerShell, [Wdróż i Zarządzaj kopią zapasową dla systemu Windows Server](backup-client-automation.md). W przypadku zarządzania kopiami zapasowymi programu DPM zapoznaj się z artykułem [wdrażanie kopii zapasowej programu DPM i zarządzanie nią](backup-dpm-automation.md).
