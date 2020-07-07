---
title: Przenoszenie maszyn wirtualnych platformy Azure do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść maszyny wirtualne do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744635"
---
# <a name="move-guidance-for-virtual-machines"></a>Wskazówki dotyczące przenoszenia maszyn wirtualnych

W tym artykule opisano scenariusze, które nie są obecnie obsługiwane, oraz kroki umożliwiające przeniesienie maszyn wirtualnych przy użyciu kopii zapasowej.

## <a name="scenarios-not-supported"></a>Scenariusze nie są obsługiwane

Następujące scenariusze nie są jeszcze obsługiwane:

* Nie można przenieść Managed Disks w Strefy dostępności do innej subskrypcji.
* Nie można przenieść Virtual Machine Scale Sets ze standardową jednostką SKU Load Balancer lub z publicznym adresem IP standardowej jednostki SKU.
* Nie można przenosić maszyn wirtualnych utworzonych z zasobów witryny Marketplace z dołączonymi planami między grupami zasobów lub subskrypcjami. Usuń obsługę administracyjną maszyny wirtualnej w bieżącej subskrypcji i Wdróż ją ponownie w nowej subskrypcji.
* Nie można przenieść maszyn wirtualnych w istniejącej sieci wirtualnej do nowej subskrypcji, jeśli nie przeniesiesz wszystkich zasobów w sieci wirtualnej.
* Nie można przenosić maszyn wirtualnych o niskim priorytecie i zestawów skalowania maszyn wirtualnych o niskim priorytecie między grupami zasobów lub subskrypcjami.
* Maszyn wirtualnych w zestawie dostępności nie można przenieść pojedynczo.

## <a name="virtual-machines-with-azure-backup"></a>Maszyny wirtualne z Azure Backup

Aby przenieść maszyny wirtualne skonfigurowane z Azure Backup, należy usunąć z magazynu punkty przywracania.

Jeśli [usuwanie nietrwałe](../../../backup/backup-azure-security-feature-cloud.md) jest włączone dla maszyny wirtualnej, nie można przenieść maszyny wirtualnej, dopóki te punkty przywracania nie są przechowywane. [Wyłącz usuwanie nietrwałe](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) lub poczekaj 14 dni po usunięciu punktów przywracania.

### <a name="portal"></a>Portal

1. Tymczasowe zatrzymywanie tworzenia kopii zapasowej i zachowywanie danych kopii zapasowej.
2. Aby przenieść maszyny wirtualne skonfigurowane przy użyciu Azure Backup, wykonaj następujące czynności:

   1. Znajdź lokalizację maszyny wirtualnej.
   2. Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` . Na przykład *AzureBackupRG_westus2_1*
   3. W Azure Portal zaznacz opcję **Pokaż ukryte typy**.
   4. Znajdź zasób z typem **Microsoft. COMPUTE/restorePointCollections** , który ma wzorzec nazewnictwa `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.
   6. Po zakończeniu operacji usuwania można przenieść maszynę wirtualną.

3. Przenieś maszynę wirtualną do docelowej grupy zasobów.
4. Wznów tworzenie kopii zapasowej.

### <a name="powershell"></a>Program PowerShell

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` na przykład AzureBackupRG_westus2_1
* Jeśli w programie PowerShell, użyj `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` polecenia cmdlet
* Znajdź zasób z typem `Microsoft.Compute/restorePointCollections` , który ma wzorzec nazewnictwa`AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

* Znajdź lokalizację maszyny wirtualnej.
* Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` na przykład AzureBackupRG_westus2_1
* Jeśli w interfejsie wiersza polecenia, użyj polecenia`az resource list -g AzureBackupRG_<location of your VM>_1`
* Znajdź zasób z typem `Microsoft.Compute/restorePointCollections` , który ma wzorzec nazewnictwa`AzureBackup_<name of your VM that you're trying to move>_###########`
* Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).

* Informacje o przenoszeniu magazynów usługi Recovery Service dla kopii zapasowej znajdują się w temacie [Recovery Services ograniczenia](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
