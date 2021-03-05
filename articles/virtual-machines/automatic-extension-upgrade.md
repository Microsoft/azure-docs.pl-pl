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
ms.openlocfilehash: fa4fa1c43ab9d31b879bdec8e724e896bd16e14c
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123907"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Wersja zapoznawcza: automatyczne uaktualnianie rozszerzeń dla maszyn wirtualnych i zestawów skalowania na platformie Azure

Automatyczne uaktualnianie rozszerzeń jest dostępne w wersji zapoznawczej dla maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Jeśli automatyczne uaktualnianie rozszerzeń jest włączone dla maszyny wirtualnej lub zestawu skalowania, rozszerzenie zostanie uaktualnione automatycznie za każdym razem, gdy Wydawca rozszerzenia zwolni nową wersję dla tego rozszerzenia.

 Automatyczne uaktualnianie rozszerzeń ma następujące funkcje:
- Obsługiwane w przypadku maszyn wirtualnych platformy Azure i usługi Azure Virtual Machine Scale Sets. Virtual Machine Scale Sets Service Fabric nie są obecnie obsługiwane.
- Uaktualnienia są stosowane w modelu wdrożenia pierwszego dostępności (szczegółowo poniżej).
- W przypadku zestawu skalowania maszyn wirtualnych nie można uaktualnić więcej niż 20% maszyn wirtualnych zestawu skalowania w jednej partii. Minimalny rozmiar wsadu to jedna maszyna wirtualna.
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz dla rozszerzeń systemu Windows i Linux.
- W dowolnym momencie możesz zrezygnować z automatycznych uaktualnień.
- Automatyczne uaktualnianie rozszerzeń można włączyć na Virtual Machine Scale Sets dowolnego rozmiaru.
- Każde obsługiwane rozszerzenie jest rejestrowane osobno i można wybrać rozszerzenia, które mają zostać uaktualnione automatycznie.
- Obsługiwane we wszystkich regionach chmury publicznej.


> [!IMPORTANT]
> Automatyczne uaktualnianie rozszerzeń jest obecnie w publicznej wersji zapoznawczej. Aby korzystać z funkcji publicznej wersji zapoznawczej opisanej poniżej, wymagana jest procedura zgody.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Jak działa automatyczne uaktualnianie rozszerzeń?
Proces uaktualniania rozszerzenia zastępuje istniejącą wersję rozszerzenia na maszynie wirtualnej nową wersją tego samego rozszerzenia, gdy jest publikowana przez wydawcę rozszerzenia. Kondycja maszyny wirtualnej jest monitorowana po zainstalowaniu nowego rozszerzenia. Jeśli maszyna wirtualna nie jest w dobrej kondycji w ciągu 5 minut od zakończenia uaktualniania, wersja rozszerzenia zostanie wycofana do poprzedniej wersji.

Nieudana próba aktualizacji rozszerzenia zostanie ponowiona automatycznie. Ponowna próba jest podejmowana co kilka dni automatycznie bez interwencji użytkownika.

### <a name="availability-first-updates"></a>Dostępność — aktualizacje pierwsze
Model "dostępność" dla aktualizacji z aranżacją platformą zapewnia, że konfiguracje dostępności na platformie Azure będą przestrzegane na wielu poziomach dostępności.

W przypadku grupy maszyn wirtualnych z aktualizacją platforma Azure będzie organizować aktualizacje:

**Między regionami:**
- Aktualizacja zostanie przeniesiona na platformę Azure globalnie w sposób przejściowy, aby zapobiec awariom wdrożenia w całej platformie Azure.
- Element "Phase" może mieć co najmniej jeden region, a aktualizacja przechodzi między fazami tylko wtedy, gdy odpowiednie maszyny wirtualne w poprzedniej fazie zostały pomyślnie zaktualizowane.
- Regiony z parowaniem geograficznym nie będą aktualizowane współbieżnie i nie mogą znajdować się w tej samej fazie regionalnej.
- Powodzenie aktualizacji jest mierzone przez śledzenie kondycji aktualizacji po stronie maszyny wirtualnej. Kondycja maszyny wirtualnej jest śledzona przez wskaźniki kondycji platformy dla maszyny wirtualnej. W przypadku Virtual Machine Scale Sets kondycja maszyny wirtualnej jest śledzona przez sondy kondycji aplikacji lub rozszerzenie kondycji aplikacji, jeśli ma zastosowanie do zestawu skalowania.

**W obrębie regionu:**
- Maszyny wirtualne w różnych Strefy dostępności nie są aktualizowane współbieżnie.
- Pojedyncze maszyny wirtualne niebędące częścią zestawu dostępności są przetwarzane wsadowo w celu uniknięcia współbieżnych aktualizacji dla wszystkich maszyn wirtualnych w ramach subskrypcji.  

**W ramach elementu "Set":**
- Wszystkie maszyny wirtualne we wspólnym zestawie dostępności lub zestawie skalowania nie są aktualizowane współbieżnie.  
- Maszyny wirtualne w ramach wspólnego zestawu dostępności są aktualizowane w ramach granic domeny aktualizacji i maszyny wirtualne w wielu domenach aktualizacji nie są aktualizowane współbieżnie.  
- Maszyny wirtualne we wspólnym zestawie skalowania maszyn wirtualnych są pogrupowane w partiach i aktualizowane w ramach granic domeny aktualizacji.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Proces uaktualniania Virtual Machine Scale Sets
1. Przed rozpoczęciem procesu uaktualniania program Orchestrator sprawdzi, czy w całym zestawie skalowania nie ma więcej niż 20% maszyn wirtualnych w złej kondycji (z jakiegokolwiek powodu).

2. Usługa Orchestrator Upgrades identyfikuje partię wystąpień maszyn wirtualnych do uaktualnienia. Partia aktualizacji może mieć maksymalnie 20% łącznej liczby maszyn wirtualnych z uwzględnieniem minimalnej wielkości partii jednej maszyny wirtualnej.

3. W przypadku zestawów skalowania ze skonfigurowanymi sondami kondycji aplikacji lub rozszerzeniem kondycji aplikacji uaktualnienie czeka maksymalnie 5 minut (lub zdefiniowaną konfigurację sondy kondycji), aby maszyna wirtualna była w dobrej kondycji przed uaktualnieniem kolejnej partii. Jeśli maszyna wirtualna nie odzyska kondycji po uaktualnieniu, domyślnie zostanie ponownie zainstalowana poprzednia wersja rozszerzenia na maszynie wirtualnej.

4. Uaktualnienie programu Orchestrator śledzi również procent maszyn wirtualnych, które uległy złej kondycji po uaktualnieniu. Uaktualnienie zostanie zatrzymane, jeśli ponad 20% uaktualnionych wystąpień stanie się zła w trakcie procesu uaktualniania.

Powyższy proces jest kontynuowany do momentu uaktualnienia wszystkich wystąpień w zestawie skalowania.

Uaktualnianie zestawu skalowania programu Orchestrator sprawdza dostępność ogólnego zestawu skalowania przed uaktualnieniem każdej partii. Podczas uaktualniania partii mogą istnieć inne współbieżne planowane lub nieplanowane działania konserwacyjne, które mogą mieć wpływ na kondycję maszyn wirtualnych zestawu skalowania. W takich przypadkach, jeśli więcej niż 20% wystąpień zestawu skalowania stanie się zła, wówczas uaktualnienie zestawu skalowania zostanie zatrzymane na końcu bieżącej partii.

## <a name="supported-extensions"></a>Obsługiwane rozszerzenia
Wersja zapoznawcza automatycznego uaktualniania rozszerzeń obsługuje następujące rozszerzenia (i więcej jest dodawanych okresowo):
- Agent zależności — [systemy Windows](./extensions/agent-dependency-windows.md) i [Linux](./extensions/agent-dependency-linux.md)
- [Rozszerzenie kondycji aplikacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) — systemy Windows i Linux


## <a name="enabling-preview-access"></a>Włączanie dostępu do wersji zapoznawczej
Włączenie funkcji wersji zapoznawczej wymaga jednorazowej zgody na funkcję **AutomaticExtensionUpgradePreview** na subskrypcję, jak opisano poniżej.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób włączania wersji zapoznawczej subskrypcji:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Aby włączyć podgląd subskrypcji, użyj polecenia cmdlet [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [AZ Feature Register](/cli/azure/feature#az-feature-register) , aby włączyć podgląd dla Twojej subskrypcji.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Włączanie automatycznego uaktualniania rozszerzeń
Aby włączyć automatyczne uaktualnianie rozszerzeń dla rozszerzenia, należy upewnić się, że właściwość *enableAutomaticUpgrade* jest ustawiona na *wartość true* i dodana do każdej definicji rozszerzenia pojedynczo.


### <a name="rest-api-for-virtual-machines"></a>Interfejs API REST dla Virtual Machines
Aby włączyć automatyczne uaktualnianie rozszerzeń dla rozszerzenia (w tym przykładzie rozszerzenia Agent zależności) na maszynie wirtualnej platformy Azure, użyj następujących czynności:

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
Użyj poniższego, aby dodać rozszerzenie do modelu zestawu skalowania:

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

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell Virtual Machines
Użyj polecenia cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

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


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell Virtual Machine Scale Sets
Użyj polecenia cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) , aby dodać rozszerzenie do modelu zestawu skalowania:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Zaktualizuj zestaw skalowania przy użyciu polecenia [Update-AzVmss](/powershell/module/az.compute/update-azvmss) po dodaniu rozszerzenia.


### <a name="azure-cli-for-virtual-machines"></a>Interfejs wiersza polecenia platformy Azure dla usługi Virtual Machines
Użyj polecenia [AZ VM Extension Set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet:

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
Użyj polecenia [AZ VMSS Extension Set](/cli/azure/vmss/extension#az_vmss_extension_set) , aby dodać rozszerzenie do modelu zestawu skalowania:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Uaktualnianie rozszerzeń z wieloma rozszerzeniami

Maszyna wirtualna lub zestaw skalowania maszyn wirtualnych może mieć wiele rozszerzeń z włączonym automatycznym uaktualnianiem rozszerzeń. Ta sama maszyna wirtualna lub zestaw skalowania może również mieć inne rozszerzenia bez włączonej funkcji automatycznego uaktualniania rozszerzenia.  

Jeśli dla maszyny wirtualnej dostępne są uaktualnienia wielu rozszerzeń, uaktualnienia mogą być przetwarzane zbiorczo, ale każde uaktualnienie rozszerzenia jest stosowane pojedynczo na maszynie wirtualnej. Awaria jednego rozszerzenia nie ma wpływu na inne rozszerzenia, które mogą zostać uaktualnione. Na przykład jeśli dwa rozszerzenia są zaplanowane do uaktualnienia, a pierwsze uaktualnienie rozszerzenia nie powiedzie się, drugie rozszerzenie będzie nadal uaktualniane.

Automatyczne uaktualnienia rozszerzeń można również zastosować, gdy maszyna wirtualna lub zestaw skalowania maszyn wirtualnych ma wiele rozszerzeń skonfigurowanych przy użyciu [sekwencjonowania rozszerzeń](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Sekwencjonowanie rozszerzeń dotyczy pierwszego wdrożenia maszyny wirtualnej, a wszystkie przyszłe uaktualnienia rozszerzeń na rozszerzeniu są stosowane niezależnie.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się więcej o rozszerzeniu kondycji aplikacji](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
