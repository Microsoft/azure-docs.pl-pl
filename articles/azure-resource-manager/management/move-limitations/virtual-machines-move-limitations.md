---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104913"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane, oraz kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Nie można przenieść Virtual Machine Scale Sets ze standardową jednostką SKU Load Balancer lub z publicznym adresem IP standardowej jednostki SKU.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przeniesiesz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych utworzonych z zasobów witryny Marketplace z dołączonymi planami między subskrypcjami. Aby zapoznać się z potencjalnym obejściem, zobacz [maszyny wirtualne z planami witryny Marketplace](#virtual-machines-with-marketplace-plans).
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

## <a name="virtual-machines-with-marketplace-plans"></a>Maszyny wirtualne z planami Marketplace

Nie można przenosić maszyn wirtualnych utworzonych z zasobów witryny Marketplace z dołączonymi planami między subskrypcjami. Aby obejść to ograniczenie, można wyłączyć obsługę administracyjną maszyny wirtualnej w bieżącej subskrypcji i wdrożyć ją ponownie w nowej subskrypcji. Poniższe kroki ułatwiają ponowne utworzenie maszyny wirtualnej w nowej subskrypcji. Jednak mogą nie współpracować ze wszystkimi scenariuszami. Jeśli plan nie jest już dostępny w portalu Marketplace, te kroki nie będą działać.

1. Kopiuj informacje o planie.

1. Sklonuj dysk systemu operacyjnego w ramach subskrypcji docelowej lub Przenieś oryginalny dysk po usunięciu maszyny wirtualnej z subskrypcji źródłowej.

1. W subskrypcji docelowej Zaakceptuj warunki witryny Marketplace dotyczące Twojego planu. Możesz zaakceptować warunki, uruchamiając następujące polecenie programu PowerShell:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Można też utworzyć nowe wystąpienie maszyny wirtualnej z planem za pomocą portalu. Możesz usunąć maszynę wirtualną po zaakceptowaniu warunków w nowej subskrypcji.

1. W subskrypcji docelowej Utwórz ponownie maszynę wirtualną ze sklonowanego dysku systemu operacyjnego przy użyciu programu PowerShell, interfejsu wiersza polecenia lub szablonu Azure Resource Manager. Uwzględnij plan witryny Marketplace, który jest dołączony do dysku. Informacje o planie powinny być zgodne z planem zakupionym w ramach nowej subskrypcji.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane z Azure Backup, należy usunąć z magazynu punkty przywracania.

Jeśli [usuwanie nietrwałe](../../../backup/soft-delete-virtual-machines.md) jest włączone dla maszyny wirtualnej, nie można przenieść maszyny wirtualnej, dopóki te punkty przywracania nie są przechowywane. [Wyłącz usuwanie nietrwałe](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) lub poczekaj 14 dni po usunięciu punktów przywracania.

### <a name="portal"></a>Portal

1. Tymczasowe zatrzymanie tworzenia kopii zapasowej i przechowywanie danych kopii zapasowej.
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
