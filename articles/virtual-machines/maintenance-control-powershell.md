---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji i programu PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: b1c72c2f606ab653d7e3f1d81f7278571e8e4978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136536"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Kontroluj aktualizacje przy użyciu sterowania konserwacją i Azure PowerShell

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure. W tym temacie omówiono opcje Azure PowerShell sterowania konserwacją. Aby uzyskać więcej informacji na temat korzyści z używania kontroli konserwacji, jej ograniczeń i innych opcji zarządzania, zobacz [Zarządzanie aktualizacjami platformy przy użyciu kontroli konserwacji](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>Włączanie modułu programu PowerShell

Upewnij się `PowerShellGet` , że jest aktualne.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Jeśli instalujesz lokalnie, upewnij się, że otwarto wiersz polecenia programu PowerShell jako administrator.

Może również pojawić się monit o potwierdzenie, że chcesz zainstalować z *niezaufanego repozytorium*. Wpisz `Y` lub wybierz opcję **tak, aby** zainstalować moduł.


## <a name="create-a-maintenance-configuration"></a>Utwórz konfigurację konserwacji

Utwórz grupę zasobów jako kontener dla konfiguracji. W tym przykładzie grupa zasobów o nazwie *myMaintenanceRG* jest tworzona w *wschodnim regionie*. Jeśli masz już grupę zasobów, której chcesz użyć, możesz pominąć tę część i zastąpić nazwę grupy zasobów własnymi w pozostałej części przykładów.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Użyj [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) , aby utworzyć konfigurację konserwacji. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja *config* objęta zakresem hosta. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Użycie `-MaintenanceScope host` gwarantuje, że konfiguracja konserwacji jest używana do kontrolowania aktualizacji hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, zostanie wyświetlony komunikat o błędzie. Nazwy konfiguracji muszą być unikatowe dla Twojej subskrypcji.

Można wykonać zapytanie o dostępne konfiguracje konserwacji za pomocą polecenia [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Przypisz konfigurację

Użyj [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) , aby przypisać konfigurację do izolowanej maszyny wirtualnej lub dedykowanego hosta platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny wirtualnej przy użyciu identyfikatora konfiguracji. Określ `-ResourceType VirtualMachines` i podaj nazwę maszyny wirtualnej dla `-ResourceName`programu oraz grupę zasobów maszyny wirtualnej dla. `-ResourceGroupName` 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedykowany host

Aby zastosować konfigurację do dedykowanego hosta, należy również dołączyć `-ResourceType hosts` `-ResourceParentName` z nazwą grupy hostów i. `-ResourceParentType hostGroups` 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Wyszukaj oczekujące aktualizacje

Użyj [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) , aby sprawdzić, czy istnieją oczekujące aktualizacje. Użyj `-subscription` , aby określić subskrypcję platformy Azure dla maszyny wirtualnej, jeśli jest inna niż ta, która jest zarejestrowana.

Jeśli nie ma żadnych aktualizacji do wyświetlenia, to polecenie zwróci wartość Nothing. W przeciwnym razie zwróci obiekt PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy istnieją oczekujące aktualizacje dla izolowanej maszyny wirtualnej. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności. Zamień wartości dla zasobów własnymi.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Stosowanie aktualizacji

Użyj opcji [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) , aby zastosować oczekujące aktualizacje.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie zastosowania aktualizacji dla izolowanej maszyny wirtualnej.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Po powodzeniu to polecenie zwróci `PSApplyUpdate` obiekt. Aby sprawdzić stan aktualizacji, można użyć atrybutu `Get-AzApplyUpdate` Name w poleceniu. Zobacz [Sprawdzanie stanu aktualizacji](#check-update-status).

### <a name="dedicated-host"></a>Dedykowany host

Zastosuj aktualizacje do dedykowanego hosta.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Sprawdź stan aktualizacji
Aby sprawdzić stan aktualizacji, użyj [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) . Poniższe polecenia pokazują stan najnowszej aktualizacji przy użyciu `default` `-ApplyUpdateName` parametru. Można zastąpić nazwę aktualizacji (zwracaną przez polecenie [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) ), aby uzyskać stan konkretnej aktualizacji.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime będzie czas, w którym aktualizacja została ukończona, inicjowana przez użytkownika lub przez platformę w przypadku nieużycia okna samoobsługowego. Jeśli aktualizacja nie została zastosowana przez kontrolę konserwacji, zostanie wyświetlona wartość domyślna.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy są aktualizacje określonej maszyny wirtualnej.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedykowany host

Sprawdź dostępność aktualizacji na dedykowanym hoście.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Usuń konfigurację konserwacji

Aby usunąć konfigurację konserwacji, należy użyć [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) .

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).
