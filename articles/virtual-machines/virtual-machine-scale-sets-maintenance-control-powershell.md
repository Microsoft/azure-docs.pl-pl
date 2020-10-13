---
title: Kontrola konserwacji obrazów systemu operacyjnego w zestawach skalowania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak sterować automatycznym uaktualnianiem obrazu systemu operacyjnego do zestawów skalowania maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji i programu PowerShell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974842"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Wersja zapoznawcza: kontrola konserwacji obrazów systemu operacyjnego w zestawach skalowania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

Kontrola konserwacji pozwala określić, kiedy mają być stosowane automatyczne uaktualnienia obrazu systemu operacyjnego gościa do zestawów skalowania maszyn wirtualnych. W tym temacie omówiono opcje Azure PowerShell sterowania konserwacją. Aby uzyskać więcej informacji o korzystaniu z funkcji kontroli konserwacji, zobacz [Kontrola konserwacji dla zestawów skalowania maszyn wirtualnych platformy Azure](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Kontrola konserwacji obrazów systemu operacyjnego w zestawach skalowania maszyn wirtualnych platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="enable-the-powershell-module"></a>Włączanie modułu programu PowerShell

Upewnij się `PowerShellGet` , że jest aktualne.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Zainstaluj `Az.Maintenance` moduł programu PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Jeśli instalujesz lokalnie, upewnij się, że otwarto wiersz polecenia programu PowerShell jako administrator.

Może również pojawić się monit o potwierdzenie, że chcesz zainstalować z *niezaufanego repozytorium*. Wpisz `Y` lub wybierz opcję **tak, aby** zainstalować moduł.

## <a name="connect-to-an-azure-account"></a>Nawiązywanie połączenia z kontem platformy Azure

Połącz się z żądanym kontem platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i [Set-AzAccount](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Utworzenie konfiguracji konserwacji

Utwórz grupę zasobów jako kontener dla konfiguracji. W tym przykładzie grupa zasobów o nazwie *myMaintenanceRG* jest tworzona w *eastus2*. Jeśli masz już grupę zasobów, której chcesz użyć, możesz pominąć tę część. Wystarczy zamienić nazwę grupy zasobów na własną w pozostałej części przykładów.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Użyj [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) , aby utworzyć konfigurację konserwacji. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja *config* w zakresie obrazu systemu operacyjnego. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> **Czas trwania** konserwacji musi wynosić *5 godzin* lub dłużej. **Cykl** konserwacji musi być ustawiony na *dzień*.

Użycie `-MaintenanceScope OSImage` gwarantuje, że konfiguracja konserwacji służy do kontrolowania aktualizacji systemu operacyjnego gościa.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, zostanie wyświetlony komunikat o błędzie. Nazwy konfiguracji muszą być unikatowe dla grupy zasobów.

Można wykonać zapytanie o dostępne konfiguracje konserwacji za pomocą polecenia [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Skojarz zestaw skalowania maszyn wirtualnych z konfiguracją konserwacji

Zestaw skalowania maszyn wirtualnych można skojarzyć z dowolną konfiguracją konserwacji niezależnie od regionu i subskrypcji konfiguracji konserwacji. Ustawiając konfigurację konserwacji, nowe aktualizacje obrazu systemu operacyjnego dla zestawu skalowania zostaną automatycznie zaplanowane w następnym dostępnym oknie obsługi.

Użyj funkcji [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) , aby skojarzyć zestaw skalowania maszyn wirtualnych z konfiguracją konserwacji.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Włącz automatyczne uaktualnianie systemu operacyjnego

Można włączyć automatyczne uaktualnienia systemu operacyjnego dla każdego zestawu skalowania maszyn wirtualnych, który będzie używać kontroli konserwacji. Zapoznaj się z dokumentem tworzenie [zestawu skalowania maszyn wirtualnych platformy Azure automatyczne uaktualnienia systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) umożliwiające włączenie automatycznych uaktualnień systemu operacyjnego w zestawie skalowania maszyn wirtualnych. 


## <a name="next-steps"></a>Następne kroki

Informacje o konserwacji i aktualizacjach maszyn wirtualnych działających na platformie Azure.

> [!div class="nextstepaction"]
> [Konserwacja i aktualizacje](maintenance-and-updates.md)