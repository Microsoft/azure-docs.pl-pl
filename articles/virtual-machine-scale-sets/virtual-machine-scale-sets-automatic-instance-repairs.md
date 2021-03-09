---
title: Automatyczne naprawianie wystąpień przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak skonfigurować zasady naprawy automatycznych dla wystąpień maszyn wirtualnych w zestawie skalowania
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff67ac4be32142848a12185199d63db5a14e6c34
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501859"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatyczne naprawy wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure

Włączenie automatycznego naprawiania wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure ułatwia uzyskanie wysokiej dostępności dla aplikacji przez utrzymywanie zestawu prawidłowych wystąpień. Jeśli wystąpienie w zestawie skalowania zostanie uznane za niezdrowe zgodnie z informacjami podanymi przez [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md), ta funkcja automatycznie wykonuje naprawy wystąpienia, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Wymagania dotyczące korzystania z automatycznych napraw wystąpień

**Włączanie monitorowania kondycji aplikacji w zestawie skalowania**

Dla zestawu skalowania powinna być włączona funkcja monitorowania kondycji aplikacji dla wystąpień. Można to zrobić przy użyciu funkcji [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sond kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Można jednocześnie włączyć tylko jedną z nich. Aby określić stan kondycji aplikacji, należy użyć rozszerzenia kondycja aplikacji lub sond modułu równoważenia obciążenia. Ten stan kondycji jest używany przez koordynatora zestawu skalowania do monitorowania kondycji wystąpienia i wykonywania napraw, gdy jest to wymagane.

**Skonfiguruj punkt końcowy, aby zapewnić stan kondycji**

Przed włączeniem zasad automatycznego naprawiania wystąpienia, upewnij się, że wystąpienia zestawu skalowania mają skonfigurowany punkt końcowy aplikacji do emisji stanu kondycji aplikacji. Gdy wystąpienie zwraca stan 200 (OK) w tym punkcie końcowym aplikacji, wystąpienie zostanie oznaczone jako "w dobrej kondycji". We wszystkich innych przypadkach wystąpienie jest oznaczone jako "w złej kondycji", w tym następujące scenariusze:

- Jeśli nie ma skonfigurowanego punktu końcowego aplikacji w wystąpieniach maszyny wirtualnej w celu zapewnienia stanu kondycji aplikacji
- Gdy punkt końcowy aplikacji jest niepoprawnie skonfigurowany
- Gdy punkt końcowy aplikacji jest nieosiągalny

W przypadku wystąpień oznaczonych jako "w złej kondycji" Automatyczne naprawy są wyzwalane przez zestaw skalowania. Upewnij się, że punkt końcowy aplikacji został prawidłowo skonfigurowany przed włączeniem zasad automatycznego naprawiania w celu uniknięcia nieplanowanych napraw wystąpień, gdy punkt końcowy jest skonfigurowany.

**Maksymalna liczba wystąpień w zestawie skalowania**

Ta funkcja jest obecnie dostępna tylko dla zestawów skalowania, które mają maksymalnie 200 wystąpień. Zestaw skalowania można wdrożyć jako pojedynczą grupę umieszczania lub grupę z możliwością przemieszczenia, ale liczba wystąpień nie może być większa niż 200, jeśli automatyczne naprawy wystąpienia są włączone dla zestawu skalowania.

**Wersja interfejsu API**

Zasady automatycznego naprawiania są obsługiwane w przypadku interfejsu API obliczeń w wersji 2018-10-01 lub nowszej.

**Ograniczenia dotyczące przenoszenia zasobów lub subskrypcji**

Przenoszenie zasobów lub subskrypcji nie jest obecnie obsługiwane w przypadku zestawów skalowania, gdy funkcja naprawy automatyczne jest włączona.

**Ograniczenie dla zestawów skalowania usługi Service Fabric**

Ta funkcja nie jest obecnie obsługiwana w przypadku zestawów skalowania usługi Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak działają automatyczne naprawy wystąpień?

Funkcja automatycznej naprawy wystąpienia polega na monitorowaniu kondycji poszczególnych wystąpień w zestawie skalowania. Wystąpienia maszyn wirtualnych w zestawie skalowania można skonfigurować tak, aby emitować stan kondycji aplikacji przy użyciu [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sond kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Jeśli wystąpienie jest w złej kondycji, zestaw skalowania wykonuje akcję naprawy, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić. Do utworzenia nowego wystąpienia jest używany najnowszy model zestawu skalowania maszyn wirtualnych. Tę funkcję można włączyć w modelu zestawu skalowania maszyn wirtualnych przy użyciu obiektu *automaticRepairsPolicy* .

### <a name="batching"></a>Przetwarzanie wsadowe

Operacje naprawy wystąpienia automatycznego są wykonywane w partiach. W danym momencie nie więcej niż 5% wystąpień w zestawie skalowania jest naprawione za pomocą zasad automatycznej naprawy. Pozwala to uniknąć jednoczesnego usunięcia i ponownego utworzenia dużej liczby wystąpień, jeśli znaleziono w złej kondycji.

### <a name="grace-period"></a>Okres prolongaty

Gdy wystąpienie przechodzi przez operację zmiany stanu z powodu akcji PUT, PATCH lub POST wykonanej na zestawie skalowania (na przykład reobraz, ponownym wdrożeniu, aktualizacji itp.), wówczas każda akcja naprawy na tym wystąpieniu jest wykonywana dopiero po upływie okresu prolongaty. Okres prolongaty to ilość czasu, przez jaką wystąpienie może powrócić do stanu prawidłowego. Okres prolongaty rozpoczyna się po zakończeniu zmiany stanu. Pozwala to uniknąć wszelkich przedwczesnych lub przypadkowych operacji naprawy. Okres prolongaty jest uznawany za wszystkie nowo utworzone wystąpienia w zestawie skalowania (łącznie z tym utworzonym w wyniku operacji naprawy). Okres prolongaty jest określany w minutach w formacie ISO 8601 i można go ustawić przy użyciu właściwości *automaticRepairsPolicy. gracePeriod*. Okres prolongaty może wynosić od 30 minut do 90 minut i ma wartość domyślną 30 minut.

### <a name="suspension-of-repairs"></a>Zawieszenie naprawy 

Zestawy skalowania maszyn wirtualnych zapewniają możliwość tymczasowego zawieszenia automatycznych napraw wystąpień w razie potrzeby. W widoku wystąpienia zestawu skalowania maszyn wirtualnych *stan ServiceState* dla automatycznych napraw w obszarze właściwości *orchestrationServices* . Gdy zestaw skalowania jest ustawiony na automatyczne naprawy, parametr *ServiceState* ma ustawioną wartość *Running*. Gdy automatyczne naprawy są zawieszane dla zestawu skalowania, parametr *ServiceState* jest ustawiony na wartość *zawieszone*. Jeśli *automaticRepairsPolicy* jest zdefiniowany w zestawie skalowania, ale funkcja automatycznego naprawiania nie jest włączona, wówczas parametr *ServiceState* ma wartość *Nieuruchomiony*.

Jeśli nowo utworzone wystąpienia służące do zamiany złej kondycji w zestawie skalowania nadal pozostaną w złej kondycji nawet po wielokrotnym wykonaniu operacji naprawczych, to jako środek bezpieczeństwa platforma aktualizuje *stan ServiceState* na potrzeby *zawieszonych* automatycznych napraw. Możesz ponownie wznowić automatyczne naprawy, ustawiając wartość *ServiceState* dla automatycznych napraw do *uruchomienia*. Szczegółowe instrukcje znajdują się w sekcji dotyczącej [przeglądania i aktualizowania stanu usługi automatycznych napraw](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) dla danego zestawu skalowania. 

Proces automatycznego naprawiania wystąpienia przebiega w następujący sposób:

1. Funkcja [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia wysyła](../load-balancer/load-balancer-custom-probe-overview.md) polecenie ping do punktu końcowego aplikacji wewnątrz każdej maszyny wirtualnej w zestawie skalowania, aby uzyskać stan kondycji aplikacji dla każdego wystąpienia.
2. Jeśli punkt końcowy odpowie na status 200 (OK), wystąpienie zostanie oznaczone jako "w dobrej kondycji". We wszystkich innych przypadkach (łącznie z tym, czy punkt końcowy jest nieosiągalny) wystąpienie jest oznaczone jako "w złej kondycji".
3. Jeśli wystąpienie jest w złej kondycji, zestaw skalowania wyzwala akcję naprawy, usuwając wystąpienie złej kondycji i tworząc nowe, aby je zastąpić.
4. Naprawy wystąpień są wykonywane w partiach. W danym momencie nie można naprawić więcej niż 5% łącznej liczby wystąpień w zestawie skalowania. Jeśli zestaw skalowania ma mniej niż 20 wystąpień, naprawy są wykonywane dla jednego wystąpienia w złej kondycji.
5. Powyższy proces jest kontynuowany do momentu naprawienia wszystkich wystąpień w zestawie skalowania w złej kondycji.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrona wystąpień i automatyczne naprawy

Jeśli wystąpienie w zestawie skalowania jest chronione przez zastosowanie jednej z [zasad ochrony](./virtual-machine-scale-sets-instance-protection.md), automatyczne naprawy nie są wykonywane w tym wystąpieniu. Dotyczy to zarówno zasad ochrony: *Ochrona przed skalowaniem w* poziomie, jak i *Ochrona przed akcjami zestawu skalowania* . 

## <a name="terminatenotificationandautomaticrepairs"></a>Przerwij powiadomienia i automatycznie naprawiaj

Jeśli funkcja [powiadomień o przerwaniu](./virtual-machine-scale-sets-terminate-notification.md) jest włączona w zestawie skalowania, a następnie podczas automatycznej operacji naprawy, usunięcie wystąpienia w złej kondycji następuje po zakończeniu konfiguracji powiadomienia o przerwaniu. Powiadomienie o przerwaniu jest wysyłane za pomocą usługi Azure Metadata Service — zaplanowane zdarzenia — a usuwanie wystąpienia jest opóźnione na czas trwania skonfigurowanego limitu czasu opóźnienia. Jednak utworzenie nowego wystąpienia w celu zamiany złej kondycji nie czeka na zakończenie limitu czasu opóźnienia.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Włączanie zasad automatycznego naprawiania podczas tworzenia nowego zestawu skalowania

Aby włączyć automatyczne naprawy zasad podczas tworzenia nowego zestawu skalowania, należy upewnić się, że spełnione są wszystkie [wymagania](#requirements-for-using-automatic-instance-repairs) dotyczące tego funkcji. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. W przypadku nowo utworzonych zestawów skalowania wszystkie naprawy wystąpienia są wykonywane dopiero po upływie czasu oczekiwania przez okres prolongaty. Aby włączyć automatyczne naprawianie wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

Ten [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) umożliwia również wdrożenie zestawu skalowania maszyn wirtualnych z sondą kondycji modułu równoważenia obciążenia i włączenie automatycznego naprawiania wystąpień z okresem prolongaty wynoszącym 30 minut.

### <a name="azure-portal"></a>Azure Portal
 
Poniższe kroki umożliwiają włączenie zasad automatycznego naprawiania podczas tworzenia nowego zestawu skalowania.
 
1. Przejdź do **zestawu skalowania maszyn wirtualnych**.
1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **kondycja** . 
1. Znajdź sekcję **kondycja** .
1. Włącz opcję **Monitoruj kondycję aplikacji** .
1. Znajdź sekcję **zasady naprawy automatycznej** .
1. Włącz **opcję** **naprawy automatyczne** .
1. W **okresie prolongaty (min)** Określ okres prolongaty w minutach, dozwolone wartości to od 30 do 90 minut. 
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz pozycję **Recenzja + Utwórz** .

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład pokazuje, jak włączyć automatyczne naprawianie wystąpień w modelu zestawu skalowania. Użyj interfejsu API w wersji 2018-10-01 lub nowszej.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Funkcję automatycznej naprawy wystąpienia można włączyć podczas tworzenia nowego zestawu skalowania za pomocą polecenia cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Ten przykładowy skrypt przeprowadzi Cię przez proces tworzenia zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyn wirtualnych](./scripts/powershell-sample-create-complete-scale-set.md). Można skonfigurować zasady automatycznego naprawiania wystąpienia, dodając parametry *EnableAutomaticRepair* i *AutomaticRepairGracePeriod* do obiektu konfiguracji w celu utworzenia zestawu skalowania. Poniższy przykład włącza funkcję z okresem prolongaty wynoszącym 30 minut.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniższy przykład włącza zasady naprawy automatycznej podczas tworzenia nowego zestawu skalowania za pomocą polecenia *[AZ VMSS Create](/cli/azure/vmss#az-vmss-create)*. Najpierw utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z okresem prolongaty automatycznej naprawy ustawiony na 30 minut.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

Powyższy przykład używa istniejącego modułu równoważenia obciążenia i sondy kondycji do monitorowania stanu kondycji aplikacji wystąpień. Jeśli zamiast tego wolisz używać rozszerzenia kondycji aplikacji do monitorowania, możesz utworzyć zestaw skalowania, skonfigurować rozszerzenie kondycji aplikacji, a następnie włączyć zasady naprawy automatycznego wystąpienia przy użyciu polecenia *AZ VMSS Update*, jak wyjaśniono w następnej sekcji.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Włączanie zasad automatycznego naprawiania podczas aktualizowania istniejącego zestawu skalowania

Przed włączeniem zasad automatycznego naprawiania w istniejącym zestawie skalowania upewnij się, że spełniono wszystkie [wymagania](#requirements-for-using-automatic-instance-repairs) , które należy wykonać, aby uzyskać dostęp do tej funkcji. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. Aby włączyć automatyczne naprawianie wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

Po zaktualizowaniu modelu istniejącego zestawu skalowania upewnij się, że najnowszy model jest stosowany do wszystkich wystąpień skali. Zapoznaj się z instrukcjami dotyczącymi [sposobu przenoszenia maszyn wirtualnych na bieżąco z najnowszym modelem zestawu skalowania](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

Możesz zmodyfikować zasady automatycznego naprawiania istniejącego zestawu skalowania za pomocą Azure Portal. 
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. W obszarze **Ustawienia** w menu po lewej stronie wybierz pozycję **kondycja i naprawa**.
1. Włącz opcję **Monitoruj kondycję aplikacji** .
1. Znajdź sekcję **zasady naprawy automatycznej** .
1. Włącz **opcję** **naprawy automatyczne** .
1. W **okresie prolongaty (min)** Określ okres prolongaty w minutach, dozwolone wartości to od 30 do 90 minut. 
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład włącza zasady z okresem prolongaty wynoszącym 40 minut. Użyj interfejsu API w wersji 2018-10-01 lub nowszej.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) , aby zmodyfikować konfigurację funkcji automatycznego naprawiania wystąpienia w istniejącym zestawie skalowania. Poniższy przykład aktualizuje okres prolongaty do 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniżej przedstawiono przykład aktualizowania zasad automatycznego naprawiania wystąpienia w istniejącym zestawie skalowania przy użyciu polecenia *[AZ VMSS Update](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Wyświetlanie i aktualizowanie stanu usługi automatycznego naprawiania zasad

### <a name="rest-api"></a>Interfejs API REST 

Użyj [widoku Pobierz wystąpienie](/rest/api/compute/virtualmachinescalesets/getinstanceview) z interfejsem API w wersji 2019-12-01 lub nowszej dla zestawu skalowania maszyn wirtualnych, aby wyświetlić *stan* *serviceorchestrationServices* na potrzeby napraw automatycznych w obszarze właściwości. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Użyj interfejsu API *setOrchestrationServiceState* z interfejsem API w wersji 2019-12-01 lub nowszej na zestawie skalowania maszyn wirtualnych w celu ustawienia stanu automatycznych napraw. Gdy zestaw skalowania zostanie uwzględniony w funkcji naprawy automatyczne, możesz użyć tego interfejsu API, aby zawiesić lub wznowić automatyczne naprawy dla zestawu skalowania. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Użyj polecenia cmdlet [Get-instance-View](/cli/azure/vmss#az-vmss-get-instance-view) , aby wyświetlić *stan ServiceState* dla automatycznych napraw wystąpień. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Użyj polecenia cmdlet [Set-Orchestration-Service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) , aby zaktualizować *stan ServiceState* dla automatycznych napraw wystąpień. Gdy zestaw skalowania zostanie uwzględniony w funkcji automatycznej naprawy, możesz użyć tego polecenia cmdlet, aby zawiesić lub wznowić automatyczne naprawy dla zestawu skalowania. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) z parametrem *InstanceView* , aby wyświetlić *stan ServiceState* dla automatycznych napraw wystąpień.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Użyj polecenia cmdlet Set-AzVmssOrchestrationServiceState, aby zaktualizować *stan ServiceState* dla automatycznych napraw wystąpień. Gdy zestaw skalowania zostanie uwzględniony w funkcji automatycznej naprawy, możesz użyć tego polecenia cmdlet, aby zawiesić lub wznowić automatyczne naprawy dla zestawu skalowania.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Nie można włączyć zasad automatycznego naprawiania**

Jeśli zostanie wyświetlony błąd "nieprawidłowego żądania" z komunikatem "nie można odnaleźć elementu członkowskiego" automaticRepairsPolicy "w obiekcie typu" Properties "", sprawdź wersję interfejsu API używaną dla zestawu skalowania maszyn wirtualnych. Dla tej funkcji wymagany jest interfejs API w wersji 2018-10-01 lub nowszej.

**Wystąpienie nie jest naprawione nawet po włączeniu zasad**

Wystąpienie może być w okresie prolongaty. Jest to czas oczekiwania po zmianie stanu wystąpienia przed wykonaniem napraw. Ma to na celu uniknięcie jakichkolwiek przedwczesnych lub przypadkowych napraw. Akcja naprawy powinna wystąpić po zakończeniu okresu prolongaty dla tego wystąpienia.

**Wyświetlanie stanu kondycji aplikacji dla wystąpień zestawu skalowania**

Aby wyświetlić stan kondycji aplikacji, można użyć [interfejsu API widoku wystąpienia](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) dla wystąpień w zestawie skalowania maszyn wirtualnych. Za pomocą Azure PowerShell można użyć polecenia cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) z flagą *-InstanceView* . Stan kondycji aplikacji jest podany pod właściwością *vmHealth*.

W Azure Portal można zobaczyć również stan kondycji. Przejdź do istniejącego zestawu skalowania, wybierz pozycję **wystąpienia** z menu po lewej stronie, a następnie w kolumnie **stan kondycji** Sprawdź stan kondycji każdego wystąpienia zestawu skalowania. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) dla zestawów skalowania.
