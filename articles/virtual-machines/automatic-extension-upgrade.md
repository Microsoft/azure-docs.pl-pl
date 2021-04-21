---
title: Automatyczne uaktualnianie rozszerzeń dla maszyn wirtualnych i zestawów skalowania na platformie Azure
description: Dowiedz się, jak włączyć automatyczne uaktualnianie rozszerzeń dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762609"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Wersja zapoznawcza: automatyczne uaktualnianie rozszerzeń dla maszyn wirtualnych i zestawów skalowania na platformie Azure

Automatyczne uaktualnianie rozszerzeń jest dostępne w wersji zapoznawczej dla maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Gdy automatyczne uaktualnianie rozszerzeń jest włączone na maszynie wirtualnej lub w zestawie skalowania, rozszerzenie jest uaktualniane automatycznie za każdym razem, gdy wydawca rozszerzenia wyda nową wersję dla tego rozszerzenia.

 Automatyczne uaktualnianie rozszerzeń ma następujące funkcje:
- Obsługiwane w przypadku maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets nie są obecnie obsługiwane.
- Uaktualnienia są stosowane w modelu wdrażania po pierwszej dostępności (szczegóły poniżej).
- W przypadku zestawu skalowania maszyn wirtualnych nie więcej niż 20% maszyn wirtualnych zestawu skalowania zostanie uaktualnionych w jednej partii. Minimalny rozmiar partii to jedna maszyna wirtualna.
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz rozszerzeń systemów Windows i Linux.
- W dowolnym momencie możesz zrezygnować z automatycznych uaktualnień.
- Automatyczne uaktualnianie rozszerzeń można włączyć na Virtual Machine Scale Sets o dowolnym rozmiarze.
- Każde obsługiwane rozszerzenie jest rejestrowane indywidualnie i można wybrać rozszerzenia do automatycznego uaktualnienia.
- Obsługiwane we wszystkich regionach chmury publicznej.


> [!IMPORTANT]
> Automatyczne uaktualnianie rozszerzeń jest obecnie dostępne w publicznej wersji zapoznawczej. Procedura zgody jest potrzebna do korzystania z funkcji publicznej wersji zapoznawczej opisanej poniżej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Jak działa automatyczne uaktualnianie rozszerzeń?
Proces uaktualniania rozszerzenia zastępuje istniejącą wersję rozszerzenia na maszynie wirtualnej nową wersją tego samego rozszerzenia po opublikowaniu przez wydawcę rozszerzenia. Kondycja maszyny wirtualnej jest monitorowana po zainstalowaniu nowego rozszerzenia. Jeśli maszyna wirtualna nie jest w dobrej kondycji w ciągu 5 minut od ukończenia uaktualnienia, wersja rozszerzenia zostanie wycofana do poprzedniej wersji.

Nieudana aktualizacja rozszerzenia zostanie automatycznie ponowiona ponownie. Ponawianie próby jest ponawiane co kilka dni automatycznie bez interwencji użytkownika.

### <a name="availability-first-updates"></a>Aktualizacje typu Availability-first
Model "availability-first" dla aktualizacji aranżowanych na platformie zapewni, że konfiguracje dostępności na platformie Azure będą przestrzegane na wielu poziomach dostępności.

W przypadku grupy maszyn wirtualnych w trakcie aktualizacji platforma Azure będzie orkiestrować aktualizacje:

**W różnych regionach:**
- Aktualizacja będzie przechodzić na platformę Azure globalnie w sposób etapowy, aby zapobiec niepowodzeniu wdrażania na całej platformie Azure.
- "Faza" może mieć co najmniej jeden region, a aktualizacja przechodzi między fazami tylko wtedy, gdy pomyślnie zaktualizuje się kwalifikujące się maszyny wirtualne w poprzedniej fazie.
- Regiony sparowane geograficznie nie będą aktualizowane współbieżnie i nie mogą być w tej samej fazie regionalnej.
- Powodzenie aktualizacji jest mierzone przez śledzenie kondycji maszyny wirtualnej po aktualizacji. Kondycja maszyny wirtualnej jest śledzona za pomocą wskaźników kondycji platformy dla maszyny wirtualnej. W Virtual Machine Scale Sets kondycja maszyny wirtualnej jest śledzona za pośrednictwem sond kondycji aplikacji lub rozszerzenia kondycji aplikacji, jeśli jest stosowana do zestawu skalowania.

**W obrębie regionu:**
- Maszyny wirtualne w różnych Strefy dostępności nie są aktualizowane jednocześnie.
- Pojedyncze maszyny wirtualne, które nie są częścią zestawu dostępności, są wsadowe, aby uniknąć równoczesnych aktualizacji dla wszystkich maszyn wirtualnych w subskrypcji.  

**W ramach "zestawu":**
- Wszystkie maszyny wirtualne we wspólnym zestawie dostępności lub zestawie skalowania nie są aktualizowane współbieżnie.  
- Maszyny wirtualne we wspólnym zestawie dostępności są aktualizowane w granicach domeny aktualizacji, a maszyny wirtualne w wielu domenach aktualizacji nie są aktualizowane współbieżnie.  
- Maszyny wirtualne we wspólnym zestawie skalowania maszyn wirtualnych są grupowane w partiach i aktualizowane w granicach domeny aktualizacji.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Proces uaktualniania dla Virtual Machine Scale Sets
1. Przed rozpoczęciem procesu uaktualniania orkiestrator zapewni, że nie więcej niż 20% maszyn wirtualnych w całym zestawie skalowania będzie w złej kondycji (z jakiegokolwiek powodu).

2. Orkiestrator uaktualnienia identyfikuje partię wystąpień maszyn wirtualnych do uaktualnienia. Partia uaktualnienia może mieć maksymalnie 20% całkowitej liczby maszyn wirtualnych, z zastrzeżeniem minimalnego rozmiaru partii jednej maszyny wirtualnej.

3. W przypadku zestawów skalowania ze skonfigurowanymi sondami kondycji aplikacji lub rozszerzeniem kondycji aplikacji uaktualnienie oczekuje do 5 minut (lub zdefiniowanej konfiguracji sondy kondycji), aby maszyna wirtualna stała się w dobrej kondycji przed uaktualnieniem następnej partii. Jeśli maszyna wirtualna nie odzyska kondycji po uaktualnieniu, domyślnie jest ponownie instalowana poprzednia wersja rozszerzenia na maszynie wirtualnej.

4. Orkiestrator uaktualnienia śledzi również procent maszyn wirtualnych, które po uaktualnieniu stają się w złej kondycji. Uaktualnienie zostanie zatrzymane, jeśli ponad 20% uaktualnionych wystąpień stanie się w złej kondycji podczas procesu uaktualniania.

Powyższy proces jest kontynuowany do momentu uaktualnienia wszystkich wystąpień w zestawie skalowania.

Orkiestrator uaktualniania zestawu skalowania sprawdza ogólną kondycję zestawu skalowania przed uaktualnieniem każdej partii. Podczas uaktualniania partii mogą być wykonywane inne równoczesne planowane lub nieplanowane działania konserwacyjne, które mogą mieć wpływ na kondycję maszyn wirtualnych zestawu skalowania. W takich przypadkach, jeśli więcej niż 20% wystąpień zestawu skalowania stanie się w złej kondycji, uaktualnienie zestawu skalowania zatrzyma się na końcu bieżącej partii.

## <a name="supported-extensions"></a>Obsługiwane rozszerzenia
Wersja zapoznawcza automatycznego uaktualniania rozszerzeń obsługuje następujące rozszerzenia (i kolejne są dodawane okresowo):
- Agent zależności — [systemy Windows](./extensions/agent-dependency-windows.md) i [Linux](./extensions/agent-dependency-linux.md)
- [Rozszerzenie kondycji aplikacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) — systemy Windows i Linux


## <a name="enabling-preview-access"></a>Włączanie dostępu w wersji zapoznawczej
Włączenie funkcji w wersji zapoznawczej wymaga jednej zgody na włączenie funkcji **AutomaticExtensionUpgradePreview** na subskrypcję, zgodnie z poniższymi instrukcjami.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób włączania wersji zapoznawczej subskrypcji:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Register-AzProviderFeature,](/powershell/module/az.resources/register-azproviderfeature) aby włączyć podgląd subskrypcji.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [az feature register,](/cli/azure/feature#az_feature_register) aby włączyć podgląd subskrypcji.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Włączanie automatycznego uaktualniania rozszerzenia
Aby włączyć automatyczne uaktualnianie rozszerzenia dla rozszerzenia, należy upewnić się, że właściwość *enableAutomaticUpgrade* jest ustawiona na wartość *true* i dodana indywidualnie do każdej definicji rozszerzenia.


### <a name="rest-api-for-virtual-machines"></a>Interfejs API REST dla Virtual Machines
Aby włączyć automatyczne uaktualnianie rozszerzenia dla rozszerzenia (w tym przykładzie rozszerzenie Agent zależności) na maszynie wirtualnej platformy Azure, użyj następujących funkcji:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Interfejs API REST dla Virtual Machine Scale Sets
Aby dodać rozszerzenie do modelu zestawu skalowania, użyj następującego pliku:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell dla Virtual Machines
Użyj polecenia cmdlet [Set-AzVMExtension:](/powershell/module/az.compute/set-azvmextension)

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell for Virtual Machine Scale Sets
Użyj polecenia cmdlet [Add-AzVmssExtension,](/powershell/module/az.compute/add-azvmssextension) aby dodać rozszerzenie do modelu zestawu skalowania:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Zaktualizuj zestaw skalowania przy [użyciu narzędzia Update-AzVmss po](/powershell/module/az.compute/update-azvmss) dodaniu rozszerzenia.


### <a name="azure-cli-for-virtual-machines"></a>Interfejs wiersza polecenia platformy Azure dla usługi Virtual Machines
Użyj [polecenia cmdlet az vm extension set:](/cli/azure/vm/extension#az_vm_extension_set)

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Interfejs wiersza polecenia platformy Azure dla Virtual Machine Scale Sets
Użyj polecenia [cmdlet az vmss extension set,](/cli/azure/vmss/extension#az_vmss_extension_set) aby dodać rozszerzenie do modelu zestawu skalowania:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Uaktualnienia rozszerzeń z wieloma rozszerzeniami

Maszyna wirtualna lub zestaw skalowania maszyn wirtualnych może mieć wiele rozszerzeń z włączonym automatycznym uaktualnieniem rozszerzenia. Ta sama maszyna wirtualna lub zestaw skalowania może również mieć inne rozszerzenia bez włączonego automatycznego uaktualniania rozszerzeń.  

Jeśli dla maszyny wirtualnej jest dostępnych wiele uaktualnień rozszerzeń, uaktualnienia mogą być połączone w partie, ale każde uaktualnienie rozszerzenia jest stosowane indywidualnie na maszynie wirtualnej. Awaria jednego rozszerzenia nie ma wpływu na inne rozszerzenia, które mogą być uaktualniane. Jeśli na przykład zaplanowano uaktualnienie dwóch rozszerzeń, a pierwsze uaktualnienie rozszerzenia zakończy się niepowodzeniem, drugie rozszerzenie nadal będzie uaktualniane.

Automatyczne uaktualnienia rozszerzeń można również stosować, gdy maszyna wirtualna lub zestaw skalowania maszyn wirtualnych ma wiele rozszerzeń skonfigurowanych z [sekwencjonowaniem rozszerzeń.](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md) Sekwencjonowanie rozszerzeń ma zastosowanie do pierwszego wdrożenia maszyny wirtualnej, a wszystkie przyszłe uaktualnienia rozszerzenia dla rozszerzenia są stosowane niezależnie.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się więcej o rozszerzeniu kondycji aplikacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
