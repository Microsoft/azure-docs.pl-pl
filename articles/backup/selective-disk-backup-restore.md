---
title: Selektywne tworzenie kopii zapasowych i przywracanie dysków dla maszyn wirtualnych platformy Azure
description: W tym artykule poznasz informacje o kopii zapasowej i przywracaniu na dysku selektywnym przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 95104f231e7b4d4d2135ac3c5dde27512d465775
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746984"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Selektywne tworzenie kopii zapasowych i przywracanie dysków dla maszyn wirtualnych platformy Azure

Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) w maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyn wirtualnych. Teraz korzystając z funkcji tworzenia kopii zapasowych i przywracania dysków selektywnych, można utworzyć kopię zapasową podzbioru dysków danych w maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera tylko te dyski, które są uwzględnione w operacji tworzenia kopii zapasowej. Pozwala to na dalsze przywrócenie podzestawu dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to zarówno przywracania z migawek, jak i magazynu.

## <a name="scenarios"></a>Scenariusze

To rozwiązanie jest szczególnie przydatne w następujących scenariuszach:

1. Jeśli masz krytyczne dane, których kopię zapasową chcesz utworzyć tylko w jednym dysku, lub podzbiorze dysków i nie chcesz tworzyć kopii zapasowych reszty dysków podłączonych do maszyny wirtualnej, aby zminimalizować koszty magazynu kopii zapasowych.
2. Jeśli masz inne rozwiązania do tworzenia kopii zapasowych w ramach maszyny wirtualnej lub danych. Na przykład jeśli utworzysz kopię zapasową baz danych lub danych przy użyciu innego rozwiązania do tworzenia kopii zapasowych, a chcesz użyć kopii zapasowej na poziomie maszyny wirtualnej platformy Azure do pozostałej części danych lub dysków, aby utworzyć wydajny i niezawodny system przy użyciu najlepszych dostępnych funkcji.

Korzystając z programu PowerShell lub interfejsu wiersza polecenia platformy Azure, można skonfigurować selektywną kopię zapasową dysku maszyny wirtualnej platformy Azure.  Za pomocą skryptu można dołączać lub wykluczać dyski danych przy użyciu numerów LUN.  Obecnie możliwość konfigurowania kopii zapasowych dysków selektywnych za pomocą Azure Portal jest ograniczona do opcji **tylko dysk systemu operacyjnego kopii zapasowej** . Pozwala to na skonfigurowanie kopii zapasowej maszyny wirtualnej platformy Azure z dyskiem systemu operacyjnego i wykluczenie wszystkich dołączonych dysków danych.

>[!NOTE]
> Dysk systemu operacyjnego jest domyślnie dodawany do kopii zapasowej maszyny wirtualnej i nie można go wykluczyć.

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Upewnij się, że używasz polecenia AZ CLI w wersji 2.0.80 lub nowszej. Wersję interfejsu wiersza polecenia można uzyskać za pomocą tego polecenie:

```azurecli
az --version
```

Zaloguj się do identyfikatora subskrypcji, w którym znajduje się magazyn Recovery Services i maszyna wirtualna:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>W każdym poleceniu poniżej jest wymagana tylko nazwa obiektu **Resource** (nie obiekt) odpowiadająca magazynowi.

### <a name="configure-backup-with-azure-cli"></a>Konfigurowanie kopii zapasowej za pomocą interfejsu wiersza polecenia platformy Azure

Podczas operacji konfigurowania ochrony należy określić ustawienie listy dysków z parametrem wykluczenia **dołączenia**  /  **exclusion** , podając numery LUN dysków, które mają być dołączone lub wykluczone w kopii zapasowej.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Jeśli maszyna wirtualna nie znajduje się w tej samej grupie zasobów, co magazyn **, a następnie** Grupa zasobów odwołuje się do niej, w której utworzono magazyn. Zamiast nazwy maszyny wirtualnej podaj identyfikator maszyny wirtualnej, jak pokazano poniżej.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Modyfikowanie ochrony dla już utworzonych kopii zapasowych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Tworzenie kopii zapasowej tylko dysku systemu operacyjnego podczas konfigurowania kopii zapasowej za pomocą interfejsu wiersza polecenia

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Tworzenie kopii zapasowej tylko dysku systemu operacyjnego podczas modyfikacji ochrony przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Przywracanie dysków za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Przywróć tylko dysk systemu operacyjnego za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Pobierz chroniony element, aby uzyskać szczegóły wykluczenia dysku za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Do chronionego elementu Dodano dodatkowy parametr **diskExclusionProperties** , jak pokazano poniżej:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Pobieranie zadania tworzenia kopii zapasowej za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

To polecenie umożliwia uzyskanie szczegółowych informacji o dyskach kopii zapasowej i wykluczonych dyskach, jak pokazano poniżej:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Wyświetlanie listy punktów odzyskiwania przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Zapewnia to informacje o liczbie dołączonych dysków i kopii zapasowej na maszynie wirtualnej.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Pobierz punkt odzyskiwania przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Każdy punkt odzyskiwania ma informacje o dyskach dołączonych i wykluczonych:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Usuwanie ustawień wykluczania dysku i pobieranie chronionego elementu przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Po wykonaniu tych poleceń zobaczysz `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

Upewnij się, że używasz Azure PowerShell w wersji 3.7.0 lub nowszej.

### <a name="enable-backup-with-powershell"></a>Włączanie tworzenia kopii zapasowych za pomocą programu PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Kopia zapasowa tylko dysku systemu operacyjnego podczas konfigurowania kopii zapasowej za pomocą programu PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Pobierz obiekt elementu kopii zapasowej do przekazanie w ramach modyfikacji ochrony przy użyciu programu PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Należy przekazać powyższy uzyskany obiekt **$Item** do parametru **– Item** w następujących poleceniach cmdlet.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Modyfikowanie ochrony dla już utworzonych kopii zapasowych maszyn wirtualnych przy użyciu programu PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Tworzenie kopii zapasowej tylko dysku systemu operacyjnego podczas modyfikacji ochrony przy użyciu programu PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Resetowanie ustawienia wykluczania dysku przy użyciu programu PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Przywracanie selektywnych dysków przy użyciu programu PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Przywróć tylko dysk systemu operacyjnego przy użyciu programu PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Za pomocą witryny Azure Portal

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Za pomocą Azure Portal można wyświetlić dołączone i wykluczone dyski z okienka szczegółów kopii zapasowej maszyny wirtualnej i okienka Szczegóły zadania tworzenia kopii zapasowej.  Podczas przywracania, po wybraniu punktu odzyskiwania do przywrócenia, można wyświetlić kopię zapasową dysków w tym punkcie odzyskiwania.

W tym miejscu można wyświetlić dołączone i wykluczone dyski maszyny wirtualnej w portalu z okienka szczegółów kopii zapasowej maszyny wirtualnej:

![Wyświetlanie dołączonych i wykluczonych dysków z okienka szczegółów kopii zapasowej](./media/selective-disk-backup-restore/backup-details.png)

W tym miejscu można wyświetlić dyski dołączone i wykluczone w obszarze kopia zapasowa z okienka Szczegóły zadania:

![Wyświetlanie dołączonych i wykluczonych dysków z okienka szczegółów zadania](./media/selective-disk-backup-restore/job-details.png)

W tym miejscu można wyświetlić kopię zapasową dysków podczas przywracania, wybierając punkt odzyskiwania do przywrócenia:

![Wyświetl dyski z kopią zapasową podczas przywracania](./media/selective-disk-backup-restore/during-restore.png)

Skonfigurowanie środowiska tworzenia kopii zapasowych dysków selektywnych dla maszyny wirtualnej za pomocą Azure Portal jest ograniczone do opcji **tylko dysk systemu operacyjnego kopii zapasowej** . Aby korzystać z kopii zapasowych dysków selektywnych na maszynach wirtualnych z kopią zapasową lub w celu zaawansowania dołączania lub wykluczania określonych dysków danych maszyny wirtualnej, należy użyć programu PowerShell lub interfejsu wiersza polecenia

>[!NOTE]
>Jeśli dane obejmują między dyskami, upewnij się, że wszystkie dyski zależne są zawarte w kopii zapasowej. Jeśli nie utworzysz kopii zapasowej wszystkich dysków zależnych w woluminie, podczas przywracania nie zostanie utworzony wolumin składający się z niektórych dysków bez kopii zapasowej.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Wykonaj kopię zapasową dysku systemu operacyjnego tylko w Azure Portal

Po włączeniu tworzenia kopii zapasowej przy użyciu Azure Portal można wybrać opcję **tworzenia kopii zapasowej tylko dysku systemu operacyjnego** . Pozwala to na skonfigurowanie kopii zapasowej maszyny wirtualnej platformy Azure z dyskiem systemu operacyjnego i wykluczenie wszystkich podłączonych do niej dysków danych.

![Konfiguruj kopię zapasową tylko dla dysku systemu operacyjnego](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Korzystanie z interfejsu API REST platformy Azure

Kopię zapasową maszyny wirtualnej platformy Azure można skonfigurować za pomocą kilku wybranych dysków lub zmodyfikować ochronę istniejącej maszyny wirtualnej w celu uwzględnienia/wykluczenia kilku dysków zgodnie z opisem w [tym miejscu](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup).

## <a name="selective-disk-restore"></a>Selektywne przywracanie dysków

Funkcja przywracania dysków selektywnych jest dodatkową funkcją pobieraną po włączeniu funkcji tworzenia kopii zapasowych na dyskach selektywnych. Korzystając z tej funkcji, można przywrócić dyski selektywne ze wszystkich dysków, których kopia zapasowa jest wykonywana w punkcie odzyskiwania. Jest to wydajniejsze i pomaga zaoszczędzić czas w scenariuszach, w których wiesz, które dyski muszą zostać przywrócone.

- Dysk systemu operacyjnego jest domyślnie uwzględniany w kopii zapasowej maszyny wirtualnej i przywracania oraz nie może być wykluczony.
- Selektywne przywracanie dysków jest obsługiwane tylko dla punktów odzyskiwania utworzonych po włączeniu możliwości wykluczenia dysku.
- Kopie zapasowe z ustawieniem wykluczania dysku **obsługują tylko opcję** **przywracania dysku** . **Przywracanie maszyny wirtualnej** lub **zastępowanie istniejących** opcji przywracania nie jest obsługiwane w tym przypadku.

![Opcja przywrócenia maszyny wirtualnej i zastępowania istniejących nie jest dostępna podczas operacji przywracania](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Ograniczenia

Funkcja tworzenia kopii zapasowych dysków selektywnych nie jest obsługiwana w przypadku klasycznych maszyn wirtualnych i szyfrowanych maszyn wirtualnych. Tak więc maszyny wirtualne platformy Azure, które są zaszyfrowane za pomocą narzędzia Azure Disk Encryption (ADE) przy użyciu funkcji BitLocker do szyfrowania maszyn wirtualnych z systemem Windows, a funkcja dm-crypt dla maszyn wirtualnych systemu Linux nie jest obsługiwana

Opcje przywracania służące do **tworzenia nowej maszyny wirtualnej** i **zastępowania istniejących** nie są obsługiwane dla maszyny wirtualnej, dla której włączono funkcję tworzenia kopii zapasowych na dyskach selektywnych.

Obecnie kopia zapasowa maszyny wirtualnej platformy Azure nie obsługuje maszyn wirtualnych z dyskami o najwyższej lub podłączonym dysku udostępnionym. W takich przypadkach nie można użyć selektywnej kopii zapasowej dysku, który wyklucza dysk i kopię zapasową maszyny wirtualnej.

## <a name="billing"></a>Rozliczenia

Kopia zapasowa maszyny wirtualnej platformy Azure jest zgodna z istniejącym modelem cen, szczegółowo wyjaśnioną [tutaj](https://azure.microsoft.com/pricing/details/backup/).

**Koszt chronionego wystąpienia (PI)** jest obliczany dla dysku systemu operacyjnego tylko w przypadku, gdy użytkownik zdecyduje się utworzyć kopię zapasową przy użyciu opcji **tylko dysk systemu operacyjnego** .  W przypadku skonfigurowania kopii zapasowej i wybrania co najmniej jednego dysku z danymi koszt PI zostanie obliczony dla wszystkich dysków dołączonych do maszyny wirtualnej. **Koszt magazynu kopii zapasowych** jest obliczany na podstawie tylko dołączonych dysków, dzięki czemu można zaoszczędzić koszt magazynu. **Koszt migawki** jest zawsze obliczany dla wszystkich dysków w maszynie wirtualnej (dysków dołączonych i wykluczonych).

Jeśli wybrano funkcję przywracania między regionami (CRR), [Cennik CRR](https://azure.microsoft.com/pricing/details/backup/) dotyczy kosztów magazynu kopii zapasowych po wykluczeniu dysku.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Jak koszt wystąpienia chronionego (PI) jest obliczany tylko dla kopii zapasowej dysku systemu operacyjnego w systemach Windows i Linux?

Koszt PI jest obliczany na podstawie rzeczywistego (używanego) rozmiaru maszyny wirtualnej.

- W przypadku systemu Windows: użycie obliczenia miejsca jest oparte na dysku, na którym jest przechowywany system operacyjny (zazwyczaj jest to C:).
- W przypadku systemu Linux: użycie obliczenia miejsca jest oparte na urządzeniu, na którym jest zainstalowany główny system plików (/).

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Mam skonfigurowaną kopię zapasową dysku systemu operacyjnego, dlaczego migawka jest taka sama dla wszystkich dysków?

Funkcje tworzenia kopii zapasowych na dysku selektywnym pozwalają zaoszczędzić koszt magazynowania magazynu kopii zapasowych przez ograniczenie funkcjonalności dołączonych dysków, które są częścią kopii zapasowej. Jednak migawka jest wykonywana dla wszystkich dysków dołączonych do maszyny wirtualnej. W związku z tym koszt migawki jest zawsze obliczany dla wszystkich dysków w maszynie wirtualnej (dysków dołączonych i wykluczonych). Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Nie mogę skonfigurować tworzenia kopii zapasowej maszyny wirtualnej platformy Azure, wykluczając dysk Ultra lub udostępnione dyski dołączone do maszyny wirtualnej

Funkcja tworzenia kopii zapasowych na dysku selektywnym jest funkcją oferowaną w ramach rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure. Obecnie kopia zapasowa maszyny wirtualnej platformy Azure nie obsługuje maszyn wirtualnych z dołączonym dyskiem o wysokiej lub dysku udostępnionym.

## <a name="next-steps"></a>Następne kroki

- [Tabela obsługi kopii zapasowych maszyn wirtualnych platformy Azure](backup-support-matrix-iaas.md)
- [Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vm-backup-faq.md)
