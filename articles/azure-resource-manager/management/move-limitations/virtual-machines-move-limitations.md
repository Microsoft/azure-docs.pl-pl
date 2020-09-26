---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 219a8b438d2715f6e97085a527b386e51759ec2c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317110"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane, oraz kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Nie można przenieść Virtual Machine Scale Sets ze standardową jednostką SKU Load Balancer lub z publicznym adresem IP standardowej jednostki SKU.
* Nie można przenosić maszyn wirtualnych utworzonych z zasobów witryny Marketplace z dołączonymi planami między subskrypcjami. Usuń obsługę administracyjną maszyny wirtualnej w bieżącej subskrypcji i Wdróż ją ponownie w nowej subskrypcji.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przeniesiesz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych o niskim priorytecie i zestawów skalowania maszyn wirtualnych o niskim priorytecie między grupami zasobów lub subskrypcjami.
* Maszyn wirtualnych w zestawie dostępności nie można przenieść pojedynczo.

## <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Nie można przenieść maszyny wirtualnej zintegrowanej z magazynem kluczy, aby zaimplementować [Azure Disk Encryption dla maszyn wirtualnych](../../../virtual-machines/linux/disk-encryption-overview.md) z systemem Linux lub [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Aby przenieść maszynę wirtualną, należy wyłączyć szyfrowanie.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane z Azure Backup, należy usunąć z magazynu punkty przywracania.

Jeśli [usuwanie nietrwałe](../../../backup/backup-azure-security-feature-cloud.md) jest włączone dla maszyny wirtualnej, nie można przenieść maszyny wirtualnej, dopóki te punkty przywracania nie są przechowywane. [Wyłącz usuwanie nietrwałe](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) lub poczekaj 14 dni po usunięciu punktów przywracania.

### <a name="portal"></a>Portal

1. Tymczasowe zatrzymywanie tworzenia kopii zapasowej i zachowywanie danych kopii zapasowej.
2. Aby przenieść maszyny wirtualne skonfigurowane przy użyciu Azure Backup, wykonaj następujące czynności:

   1. Znajdź lokalizację maszyny wirtualnej.
   2. Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<VM location>_1` . Na przykład nazwa jest w formacie *AzureBackupRG_westus2_1*.
   3. W Azure Portal zaznacz opcję **Pokaż ukryte typy**.
   4. Znajdź zasób z typem **Microsoft. COMPUTE/restorePointCollections** , który ma wzorzec nazewnictwa `AzureBackup_<VM name>_###########` .
   5. Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.
   6. Po zakończeniu operacji usuwania można przenieść maszynę wirtualną.

3. Przenieś maszynę wirtualną do docelowej grupy zasobów.
4. Wznów tworzenie kopii zapasowej.

### <a name="powershell"></a>PowerShell

1. Znajdź lokalizację maszyny wirtualnej.

1. Znajdź grupę zasobów ze wzorcem nazewnictwa — `AzureBackupRG_<VM location>_1` . Na przykład może to być nazwa `AzureBackupRG_westus2_1` .

1. Jeśli przenosisz tylko jedną maszynę wirtualną, Pobierz kolekcję punktów przywracania dla tej maszyny wirtualnej.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Jeśli przenosisz wszystkie maszyny wirtualne z funkcją Backer do tyłu w tej lokalizacji, Pobierz kolekcje punktów przywracania dla tych maszyn wirtualnych.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Usuń każdy zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Znajdź lokalizację maszyny wirtualnej.

1. Znajdź grupę zasobów ze wzorcem nazewnictwa — `AzureBackupRG_<VM location>_1` . Na przykład może to być nazwa `AzureBackupRG_westus2_1` .

1. Jeśli przenosisz tylko jedną maszynę wirtualną, Pobierz kolekcję punktów przywracania dla tej maszyny wirtualnej.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Jeśli przenosisz wszystkie maszyny wirtualne z funkcją Backer do tyłu w tej lokalizacji, Pobierz kolekcje punktów przywracania dla tych maszyn wirtualnych.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Usuń każdy zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).

* Informacje o przenoszeniu magazynów usługi Recovery Service dla kopii zapasowej znajdują się w temacie [Recovery Services ograniczenia](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
