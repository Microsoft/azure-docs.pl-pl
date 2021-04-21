---
title: Automatyczne naprawy wystąpień za pomocą zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak skonfigurować zasady automatycznej naprawy wystąpień maszyn wirtualnych w zestawie skalowania
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762915"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatyczne naprawy wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure

Włączenie automatycznej naprawy wystąpień dla zestawów skalowania maszyn wirtualnych platformy Azure pomaga zapewnić wysoką dostępność aplikacji dzięki utrzymywaniu zestawu wystąpień w dobrej kondycji. Jeśli wystąpienie w zestawie skalowania jest w złej kondycji zgłoszone przez rozszerzenie usługi [Application Health](./virtual-machine-scale-sets-health-extension.md) lub sondy kondycji usługi [Równoważenie](../load-balancer/load-balancer-custom-probe-overview.md)obciążenia, ta funkcja automatycznie naprawia wystąpienie, usuwając wystąpienie w złej kondycji i tworząc nowe, aby je zastąpić.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Wymagania dotyczące korzystania z automatycznych napraw wystąpień

**Włączanie monitorowania kondycji aplikacji dla zestawu skalowania**

Zestaw skalowania powinien mieć włączone monitorowanie kondycji aplikacji dla wystąpień. Można to zrobić przy użyciu rozszerzenia [kondycji aplikacji lub](./virtual-machine-scale-sets-health-extension.md) sond kondycji usługi [Równoważenie obciążenia.](../load-balancer/load-balancer-custom-probe-overview.md) Jednocześnie można włączyć tylko jedną z nich. Rozszerzenie kondycji aplikacji lub sondy usługi równoważenia obciążenia wysyłają polecenie ping do punktu końcowego aplikacji skonfigurowanego w wystąpieniach maszyny wirtualnej w celu określenia stanu kondycji aplikacji. Ten stan kondycji jest używany przez orkiestrator zestawu skalowania do monitorowania kondycji wystąpienia i przeprowadzania napraw w razie potrzeby.

**Konfigurowanie punktu końcowego w celu zapewnienia stanu kondycji**

Przed włączeniem zasad automatycznej naprawy wystąpień upewnij się, że wystąpienia zestawu skalowania mają skonfigurowany punkt końcowy aplikacji do emitowania stanu kondycji aplikacji. Gdy wystąpienie zwróci stan 200 (OK) w tym punkcie końcowym aplikacji, wystąpienie zostanie oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach wystąpienie jest oznaczone jako "W złej kondycji", w tym następujące scenariusze:

- Jeśli w wystąpieniach maszyny wirtualnej nie skonfigurowano punktu końcowego aplikacji w celu zapewnienia stanu kondycji aplikacji
- Gdy punkt końcowy aplikacji jest niepoprawnie skonfigurowany
- Gdy punkt końcowy aplikacji jest nieo osiągalny

W przypadku wystąpień oznaczonych jako "W złej kondycji" automatyczne naprawy są wyzwalane przez zestaw skalowania. Przed włączeniem zasad automatycznej naprawy upewnij się, że punkt końcowy aplikacji jest poprawnie skonfigurowany, aby uniknąć niezamierzonych napraw wystąpień podczas konfigurowania punktu końcowego.

**Maksymalna liczba wystąpień w zestawie skalowania**

Ta funkcja jest obecnie dostępna tylko dla zestawów skalowania, które mają maksymalnie 200 wystąpień. Zestaw skalowania można wdrożyć jako pojedynczą grupę umieszczania lub grupę z wieloma umieszczaniami, ale liczba wystąpień nie może być powyżej 200, jeśli dla zestawu skalowania włączono automatyczne naprawy wystąpień.

**Wersja interfejsu API**

Zasady automatycznej naprawy są obsługiwane w przypadku interfejsu API obliczeń w wersji 2018-10-01 lub wyższej.

**Ograniczenia dotyczące przenosiń zasobów lub subskrypcji**

Przeniesienie zasobu lub subskrypcji nie jest obecnie obsługiwane w przypadku zestawów skalowania, gdy jest włączona funkcja automatycznej naprawy.

**Ograniczenie dotyczące zestawów skalowania usługi Service Fabric**

Ta funkcja nie jest obecnie obsługiwana w przypadku zestawów skalowania usługi Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak działają automatyczne naprawy wystąpień?

Funkcja automatycznej naprawy wystąpień opiera się na monitorowaniu kondycji poszczególnych wystąpień w zestawie skalowania. Wystąpienia maszyn wirtualnych w zestawie skalowania można skonfigurować tak, aby emitowały stan kondycji aplikacji przy użyciu rozszerzenia [kondycji](./virtual-machine-scale-sets-health-extension.md) aplikacji lub sond kondycji usługi [Load Balancer.](../load-balancer/load-balancer-custom-probe-overview.md) Jeśli wystąpienie jest w złej kondycji, zestaw skalowania wykonuje akcję naprawy, usuwając wystąpienie w złej kondycji i tworząc nowe, aby je zastąpić. Najnowszy model zestawu skalowania maszyn wirtualnych służy do tworzenia nowego wystąpienia. Tę funkcję można włączyć w modelu zestawu skalowania maszyn wirtualnych przy użyciu obiektu *automaticRepairsPolicy.*

### <a name="batching"></a>Przetwarzanie wsadowe

Automatyczne operacje naprawy wystąpień są wykonywane w partiach. W dowolnym momencie nie więcej niż 5% wystąpień w zestawie skalowania są naprawiane za pomocą zasad automatycznej naprawy. Pozwala to uniknąć jednoczesnego usuwania i ponownego tworzenia dużej liczby wystąpień w przypadku wystąpienia złej kondycji w tym samym czasie.

### <a name="grace-period"></a>Okres prolongaty

Gdy wystąpienie przechodzi przez operację zmiany stanu z powodu akcji PUT, PATCH lub POST wykonywanej w zestawie skalowania (na przykład ponownego obrazu, ponownego wykonania, aktualizacji itp.), każda akcja naprawy tego wystąpienia jest wykonywana tylko po oczekiwaniu na okres prolongaty. Okres prolongaty to czas, przez który wystąpienie może wrócić do dobrej kondycji. Okres prolongaty rozpoczyna się po zakończeniu zmiany stanu. Pozwala to uniknąć przedwczesnych lub przypadkowych operacji naprawy. Okres prolongaty jest honorowany dla każdego nowo utworzonego wystąpienia w zestawie skalowania (w tym dla wystąpienia utworzonego w wyniku operacji naprawy). Okres prolongaty jest określony w minutach w formacie ISO 8601 i można go ustawić przy użyciu właściwości *automaticRepairsPolicy.gracePeriod.* Okres prolongaty może być z zakresu od 30 minut do 90 minut i ma wartość domyślną 30 minut.

### <a name="suspension-of-repairs"></a>Zawieszenie napraw 

Zestawy skalowania maszyn wirtualnych zapewniają możliwość tymczasowego wstrzymania w razie potrzeby automatycznych napraw wystąpień. Właściwość *serviceState* do automatycznej naprawy w obszarze aranżacji *właściwościUsługi* w widoku wystąpienia zestawu skalowania maszyn wirtualnych pokazuje bieżący stan automatycznych napraw. Gdy zestaw skalowania jest wybierany do automatycznych napraw, wartość parametru *serviceState* jest ustawiona na *Running*. Gdy automatyczne naprawy są wstrzymane dla zestawu skalowania, parametr *serviceState* jest ustawiany na *wartość Wstrzymano*. Jeśli *parametr automaticRepairsPolicy* jest zdefiniowany w zestawie skalowania, ale funkcja automatycznej naprawy nie jest włączona, parametr *serviceState* jest ustawiony na wartość *Nie działa.*

Jeśli nowo utworzone wystąpienia w celu zastąpienia wystąpień w złej kondycji w zestawie skalowania nadal pozostają w złej kondycji nawet po wielokrotnym wykonywaniu operacji naprawy, jako miara bezpieczeństwa platforma aktualizuje stan *usługi* na czas automatycznej naprawy na Wstrzymano *.* Automatyczne naprawy można wznowić ponownie, ustawiając wartość *serviceState* dla automatycznych napraw na *Uruchomiona.* Szczegółowe instrukcje znajdują się w sekcji dotyczącej wyświetlania [i aktualizowania](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) stanu usługi zasad automatycznej naprawy zestawu skalowania. 

Proces automatycznej naprawy wystąpień działa w następujący sposób:

1. [Sondy kondycji rozszerzenia](./virtual-machine-scale-sets-health-extension.md) kondycji aplikacji lub usługi [równoważenia](../load-balancer/load-balancer-custom-probe-overview.md) obciążenia wysyłają polecenie ping do punktu końcowego aplikacji wewnątrz każdej maszyny wirtualnej w zestawie skalowania w celu uzyskania stanu kondycji aplikacji dla każdego wystąpienia.
2. Jeśli punkt końcowy odpowiada ze stanem 200 (OK), wystąpienie jest oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach (w tym w przypadku, gdy punkt końcowy jest nieosiągalny) wystąpienie jest oznaczone jako "W złej kondycji".
3. Gdy wystąpienie jest w złej kondycji, zestaw skalowania wyzwala akcję naprawy, usuwając wystąpienie w złej kondycji i tworząc nowe, aby je zastąpić.
4. Naprawy wystąpień są wykonywane w partiach. W danym momencie nie więcej niż 5% całkowitej liczby wystąpień w zestawie skalowania jest naprawianych. Jeśli zestaw skalowania ma mniej niż 20 wystąpień, naprawy są wykonywane dla jednego wystąpienia w złej kondycji na raz.
5. Powyższy proces jest kontynuowany do momentu naprawy całego wystąpienia w złej kondycji w zestawie skalowania.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrona wystąpień i automatyczne naprawy

Jeśli wystąpienie w zestawie skalowania jest chronione przez zastosowanie jednej z zasad ochrony [,](./virtual-machine-scale-sets-instance-protection.md)automatyczne naprawy nie są wykonywane w tym wystąpieniu. Dotyczy to obu zasad ochrony: Ochrona przed *skalowaniem w skali w i* Ochrona przed *akcjami zestawu skalowania.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Przerywanie powiadomień i automatycznych napraw

Jeśli funkcja [powiadomienia o zakończeniu](./virtual-machine-scale-sets-terminate-notification.md) jest włączona w zestawie skalowania, podczas automatycznej operacji naprawy usunięcie wystąpienia w złej kondycji następuje po konfiguracji powiadomienia o zakończeniu. Powiadomienie o zakończeniu jest wysyłane za pośrednictwem usługi Azure Metadata Service — zaplanowanych zdarzeń — a usunięcie wystąpienia jest opóźnione o czas trwania skonfigurowanego limitu czasu opóźnienia. Jednak utworzenie nowego wystąpienia w celu zastąpienia wystąpienia w złej kondycji nie oczekuje na zakończenie limitu czasu opóźnienia.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Włączanie zasad automatycznej naprawy podczas tworzenia nowego zestawu skalowania

W przypadku włączania zasad automatycznej naprawy podczas tworzenia [](#requirements-for-using-automatic-instance-repairs) nowego zestawu skalowania upewnij się, że zostały spełnione wszystkie wymagania dotyczące korzystania z tej funkcji. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. W przypadku nowo utworzonych zestawów skalowania naprawy wystąpień są wykonywane dopiero po oczekiwaniu na okres prolongaty. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

Za pomocą tego [](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) szablonu szybkiego startu można również wdrożyć zestaw skalowania maszyn wirtualnych z włączoną sondą kondycji usługi równoważenia obciążenia i automatycznymi naprawami wystąpień z okresem prolongaty 30 minut.

### <a name="azure-portal"></a>Azure Portal
 
Poniższe kroki umożliwiające automatyczne naprawianie zasad podczas tworzenia nowego zestawu skalowania.
 
1. Przejdź do **zestawu skalowania maszyn wirtualnych.**
1. Wybierz **pozycję + Dodaj,** aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **Kondycja.** 
1. Znajdź **sekcję** Kondycja.
1. Włącz opcję **Monitoruj kondycję** aplikacji.
1. Znajdź **sekcję Zasady automatycznej** naprawy.
1. Włącz **opcję** **Automatyczne naprawy.**
1. W **okresie prolongaty (min)** określ okres prolongaty w minutach, a dozwolone wartości to od 30 do 90 minut. 
1. Po utworzeniu nowego zestawu skalowania wybierz przycisk **Przejrzyj i utwórz.**

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie pokazano, jak włączyć automatyczną naprawę wystąpienia w modelu zestawu skalowania. Użyj interfejsu API w wersji 2018-10-01 lub wyższej.

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

Funkcję automatycznej naprawy wystąpień można włączyć podczas tworzenia nowego zestawu skalowania za pomocą polecenia cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Ten przykładowy skrypt zawiera opis tworzenia zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Tworzenie kompletnego zestawu skalowania maszyn wirtualnych.](./scripts/powershell-sample-create-complete-scale-set.md) Zasady automatycznej naprawy wystąpień można skonfigurować, dodając parametry *EnableAutomaticRepair* i *AutomaticRepairGracePeriod* do obiektu konfiguracji w celu utworzenia zestawu skalowania. Poniższy przykład włącza funkcję z okresem prolongaty 30 minut.

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

Poniższy przykład włącza zasady automatycznej naprawy podczas tworzenia nowego zestawu skalowania za pomocą *[narzędzia az vmss create](/cli/azure/vmss#az_vmss_create)*. Najpierw utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z okresem prolongaty zasad automatycznej naprawy ustawionym na 30 minut.

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

W powyższym przykładzie użyto istniejącego usługi równoważenia obciążenia i sondy kondycji do monitorowania stanu kondycji aplikacji wystąpień. Jeśli zamiast tego wolisz używać rozszerzenia kondycji aplikacji do monitorowania, możesz utworzyć zestaw skalowania, skonfigurować rozszerzenie kondycji aplikacji, a następnie włączyć zasady automatycznej naprawy wystąpień przy użyciu aktualizacji *az vmss,* jak wyjaśniono w następnej sekcji.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Włączanie zasad automatycznej naprawy podczas aktualizowania istniejącego zestawu skalowania

Przed włączeniem zasad automatycznej naprawy w istniejącym zestawie [](#requirements-for-using-automatic-instance-repairs) skalowania upewnij się, że zostały spełnione wszystkie wymagania dotyczące zgody na tę funkcję. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj obiektu *automaticRepairsPolicy* w modelu zestawu skalowania maszyn wirtualnych.

Po zaktualizowaniu modelu istniejącego zestawu skalowania upewnij się, że najnowszy model jest stosowany do wszystkich wystąpień skalowania. Zapoznaj się z instrukcjami na temat sposobu aktualizacji maszyn wirtualnych przy użyciu [najnowszego modelu zestawu skalowania.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Azure Portal

Zasady automatycznej naprawy istniejącego zestawu skalowania można modyfikować za pomocą Azure Portal. 
 
1. Przejdź do istniejącego zestawu skalowania maszyn wirtualnych.
1. W **obszarze** Ustawienia w menu po lewej stronie wybierz pozycję **Kondycja i napraw .**
1. Włącz opcję **Monitoruj kondycję** aplikacji.
1. Znajdź **sekcję Zasady automatycznej** naprawy.
1. Włącz **opcję** **Automatyczne naprawy.**
1. W **wartości Okres prolongaty (min)** określ okres prolongaty w minutach, a dozwolone wartości to od 30 do 90 minut. 
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład włącza zasady z okresem prolongaty 40 minut. Użyj interfejsu API w wersji 2018-10-01 lub wyższej.

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

Użyj polecenia cmdlet [Update-AzVmss,](/powershell/module/az.compute/update-azvmss) aby zmodyfikować konfigurację funkcji automatycznej naprawy wystąpienia w istniejącym zestawie skalowania. Poniższy przykład aktualizuje okres prolongaty do 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniżej przedstawiono przykład aktualizowania zasad automatycznego naprawiania wystąpień istniejącego zestawu skalowania przy użyciu *[narzędzia az vmss update](/cli/azure/vmss#az_vmss_update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Wyświetlanie i aktualizowanie stanu usługi zasad automatycznej naprawy wystąpień

### <a name="rest-api"></a>Interfejs API REST 

Użyj [funkcji Pobierz](/rest/api/compute/virtualmachinescalesets/getinstanceview) widok wystąpienia z interfejsem API w wersji 2019-12-01 lub wyższej dla zestawu skalowania maszyn wirtualnych, aby wyświetlić właściwość *serviceState* dla automatycznych napraw w obszarze *aranżacji właściwościServices.* 

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

W *zestawie skalowania maszyn* wirtualnych należy używać interfejsu API setO za pomocą interfejsu API w wersji 2019-12-01 lub wyższej w zestawie skalowania maszyn wirtualnych, aby ustawić stan automatycznych napraw. Po skonfigurowaniu zestawu skalowania w funkcji automatycznej naprawy można użyć tego interfejsu API do wstrzymania lub wznowienia automatycznej naprawy zestawu skalowania. 

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

Użyj [polecenia cmdlet get-instance-view,](/cli/azure/vmss#az_vmss_get_instance_view) aby wyświetlić *stan usługi* na użytek automatycznej naprawy wystąpień. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Użyj polecenia cmdlet [set-orchestration-service-state,](/cli/azure/vmss#az_vmss_set_orchestration_service_state) aby zaktualizować *stan usługi* na użytek automatycznych napraw wystąpień. Po skonfigurowaniu zestawu skalowania w funkcji automatycznej naprawy można użyć tego polecenia cmdlet, aby wstrzymać lub wznowić automatyczne naprawy zestawu skalowania. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Użyj [polecenia cmdlet Get-AzVmss z](/powershell/module/az.compute/get-azvmss) *parametrem InstanceView,* aby wyświetlić *wartość ServiceState* dla automatycznych napraw wystąpień.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Użyj Set-AzVmssOrchestrationServiceState polecenia cmdlet, aby zaktualizować *stan usługi* na użytek automatycznej naprawy wystąpień. Po skonfigurowaniu zestawu skalowania w funkcji automatycznej naprawy można użyć tego polecenia cmdlet, aby wstrzymać lub wznowić automatyczne naprawy zestawu skalowania.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Nie można włączyć zasad automatycznej naprawy**

Jeśli zostanie wyświetlony błąd "BadRequest" z komunikatem "Could not find member 'automaticRepairsPolicy' on object of type 'properties' (Nie można odnaleźć członka 'automaticRepairsPolicy' dla obiektu typu 'properties'), sprawdź wersję interfejsu API używaną dla zestawu skalowania maszyn wirtualnych. Interfejs API w wersji 2018-10-01 lub wyższej jest wymagany dla tej funkcji.

**Wystąpienie nie jest naprawiane nawet wtedy, gdy zasady są włączone**

Wystąpienie może być w okresie prolongaty. Jest to czas oczekiwania po każdej zmianie stanu wystąpienia przed wykonaniem naprawy. Ma to na celu uniknięcie przedwczesnych lub przypadkowych napraw. Akcja naprawy powinna nastąpić po zakończeniu okresu prolongaty dla wystąpienia.

**Wyświetlanie stanu kondycji aplikacji dla wystąpień zestawu skalowania**

Możesz użyć interfejsu [API Pobierz widok wystąpienia](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) dla wystąpień w zestawie skalowania maszyn wirtualnych, aby wyświetlić stan kondycji aplikacji. Za Azure PowerShell polecenia cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) można użyć *flagi -InstanceView.* Stan kondycji aplikacji jest podany we właściwości *vmHealth*.

W Azure Portal można również sprawdzić stan kondycji. Przejdź do istniejącego zestawu  skalowania, wybierz pozycję Wystąpienia z  menu po lewej stronie i sprawdź stan kondycji każdego wystąpienia zestawu skalowania w kolumnie Stan kondycji. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub sondy kondycji usługi [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) dla zestawów skalowania.
