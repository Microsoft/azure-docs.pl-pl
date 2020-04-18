---
title: Automatyczne naprawy wystąpień za pomocą zestawów skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak skonfigurować zasady automatycznego napraw dla wystąpień maszyn wirtualnych w zestawie skalowania
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603683"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatyczne naprawy wystąpień dla zestawów skalowania maszyny wirtualnej platformy Azure

Włączenie automatycznych napraw wystąpień dla zestawów skalowania maszyny wirtualnej platformy Azure pomaga osiągnąć wysoką dostępność dla aplikacji, utrzymując zestaw zdrowych wystąpień. Jeśli wystąpienie w zestawie skalowania zostanie uznane za złej kondycji, jak zgłaszane przez [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia,](../load-balancer/load-balancer-custom-probe-overview.md)ta funkcja automatycznie wykonuje naprawę wystąpienia, usuwając wystąpienie w złej kondycji i tworząc nowe, aby go zastąpić.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Wymagania dotyczące stosowania automatycznych napraw wystąpień

**Włącz monitorowanie kondycji aplikacji dla zestawu skalowania**

Zestaw skalowania powinien mieć włączone monitorowanie kondycji aplikacji dla wystąpień. Można to zrobić za pomocą [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub sond kondycji [modułu równoważenia obciążenia.](../load-balancer/load-balancer-custom-probe-overview.md) Tylko jeden z nich można włączyć w czasie. Rozszerzenie kondycji aplikacji lub moduł równoważenia obciążenia sonduje ping punktu końcowego aplikacji skonfigurowany w wystąpieniach maszyny wirtualnej w celu określenia stanu kondycji aplikacji. Ten stan kondycji jest używany przez orchestrator zestaw skalowania do monitorowania kondycji wystąpienia i wykonywania napraw, gdy jest to wymagane.

**Konfigurowanie punktu końcowego w celu zapewnienia stanu kondycji**

Przed włączeniem zasad automatycznego naprawy wystąpienia upewnij się, że wystąpienia zestawu skalowania mają punkt końcowy aplikacji skonfigurowany do emitowania stanu kondycji aplikacji. Gdy wystąpienie zwraca stan 200 (OK) w tym punkcie końcowym aplikacji, wystąpienie jest oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach wystąpienie jest oznaczone jako "w złej kondycji", w tym następujące scenariusze:

- Jeśli wewnątrz wystąpień maszyny wirtualnej nie skonfigurowano punktu końcowego aplikacji w celu zapewnienia stanu kondycji aplikacji
- Gdy punkt końcowy aplikacji jest niepoprawnie skonfigurowany
- Gdy punkt końcowy aplikacji jest niezdysklidalny

W przypadku wystąpień oznaczonych jako "W złej kondycji" automatyczne naprawy są wyzwalane przez zestaw skalowania. Upewnij się, że punkt końcowy aplikacji jest poprawnie skonfigurowany przed włączeniem zasad automatycznych napraw, aby uniknąć niezamierzonych napraw wystąpień, gdy punkt końcowy jest coraz skonfigurowany.

**Włączanie pojedynczej grupy umieszczania**

Ta funkcja jest obecnie dostępna tylko dla zestawów skalowania wdrożonych jako pojedyncza grupa miejsc docelowych. Właściwość *singlePlacementGroup* powinna być ustawiona na *true* dla zestawu skalowania, aby używać funkcji automatycznego naprawy wystąpienia. Dowiedz się więcej o [grupach miejsc docelowych](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Wersja API**

Zasady automatycznych napraw są obsługiwane dla interfejsu API obliczeń w wersji 2018-10-01 lub nowszej.

**Ograniczenia dotyczące przenoszenia zasobów lub subskrypcji**

Przenoszenie zasobów lub subskrypcji nie są obecnie obsługiwane dla zestawów skalowania, gdy funkcja automatycznego naprawy jest włączona.

**Ograniczenie dla zestawów wag sieci szkieletowych usług**

Ta funkcja nie jest obecnie obsługiwana w przypadku zestawów skalowania sieci szkieletowej usług.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak działają automatyczne naprawy instancji?

Funkcja automatycznej naprawy wystąpienia opiera się na monitorowaniu kondycji poszczególnych wystąpień w zestawie skalowania. Wystąpienia maszyn wirtualnych w zestawie skalowania można skonfigurować do emitowania stanu kondycji aplikacji przy użyciu [rozszerzenia kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub sond kondycji [modułu równoważenia obciążenia.](../load-balancer/load-balancer-custom-probe-overview.md) Jeśli wystąpienie zostanie uznane za w złej kondycji, zestaw skalowania wykonuje akcję naprawy, usuwając w złej kondycji wystąpienie i tworząc nowe, aby go zastąpić. Najnowszy model zestawu skalowania maszyny wirtualnej jest używany do tworzenia nowego wystąpienia. Ta funkcja może być włączona w modelu zestawu skalowania maszyny wirtualnej przy użyciu *obiektu automaticRepairsPolicy.*

### <a name="batching"></a>Tworzenie partii

Operacje automatycznej naprawy wystąpienia są wykonywane w partiach. W danym momencie nie więcej niż 5% wystąpień w zestawie skalowania są naprawiane za pomocą zasad automatycznych napraw. Pomaga to uniknąć jednoczesnego usuwania i ponownego tworzenia dużej liczby wystąpień, jeśli zostanie znaleziony w złej kondycji w tym samym czasie.

### <a name="grace-period"></a>Okres prolongaty

Gdy wystąpienie przechodzi przez operację zmiany stanu z powodu put, patch lub post akcji wykonywanej na zestaw skalowania (na przykład reimage, ponowne wdrożenie, aktualizacja, itp.), a następnie wszelkie działania naprawcze w tym wystąpieniu jest wykonywana tylko po oczekiwaniu na okres prolongaty. Okres prolongaty to czas, aby umożliwić wystąpieniu powrót do stanu dobrej kondycji. Okres prolongaty rozpoczyna się po zakończeniu zmiany stanu. Pomaga to uniknąć przedwczesnych lub przypadkowych operacji naprawy. Okres prolongaty jest honorowany dla każdego nowo utworzonego wystąpienia w zestawie skalowania (w tym utworzonego w wyniku operacji naprawy). Okres prolongaty jest określony w minutach w formacie ISO 8601 i można go ustawić za pomocą właściwości *automaticRepairsPolicy.gracePeriod*. Okres prolongaty może wynosić od 30 minut do 90 minut i ma wartość domyślną 30 minut.

### <a name="suspension-of-repairs"></a>Zawieszenie napraw 

Zestawy skalowania maszyny wirtualnej umożliwiają tymczasowe zawieszanie automatycznych napraw wystąpień w razie potrzeby. *ServiceState* dla automatycznych napraw w ramach *aranżacji właściwościUsługi* w widoku instancji zestawu skalowania maszyny wirtualnej pokazuje bieżący stan automatycznych napraw. Gdy zestaw skalowania jest opted do automatycznych napraw, wartość parametr *serviceState* jest ustawiona na *Uruchomione*. Gdy automatyczne naprawy są zawieszone dla zestawu skalowania, parametr *serviceState* jest ustawiony na *Zawieszone*. Jeśli *automaticRepairsPolicy* jest zdefiniowany na zestaw skalowania, ale funkcja automatycznego naprawy nie jest włączona, a następnie service parametr *jest* ustawiony na *Nie działa*.

Jeśli nowo utworzone wystąpienia do zastąpienia niezdrowych w zestawie skalowania nadal pozostają w złej kondycji, nawet po wielokrotnym wykonywaniu operacji naprawy, a następnie jako środek bezpieczeństwa platforma aktualizuje *serviceState* dla automatycznych napraw *zawieszone*. Można wznowić automatyczne naprawy ponownie, ustawiając wartość *serviceState* dla automatycznych napraw na *Uruchomione*. Szczegółowe instrukcje znajdują się w sekcji [dotyczące przeglądania i aktualizowania stanu usługi zasad automatycznych napraw](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) dla zestawu skalowania. 

Proces automatycznej naprawy wystąpienia działa w następujący sposób:

1. [Rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) ping punktu końcowego aplikacji wewnątrz każdej maszyny wirtualnej w zestawie skalowania, aby uzyskać stan kondycji aplikacji dla każdego wystąpienia.
2. Jeśli punkt końcowy odpowiada ze stanem 200 (OK), wystąpienie jest oznaczone jako "W dobrej kondycji". We wszystkich innych przypadkach (w tym jeśli punkt końcowy jest nieosiągalny), wystąpienie jest oznaczone jako "W złej kondycji".
3. Gdy wystąpienie okaże się w złej kondycji, zestaw skalowania wyzwala akcję naprawy, usuwając w złej kondycji wystąpienie i tworząc nowy, aby go zastąpić.
4. Naprawy wystąpień są wykonywane w partiach. W danym momencie nie więcej niż 5% całkowitych wystąpień w zestawie skalowania są naprawiane. Jeśli zestaw skalowania ma mniej niż 20 wystąpień, naprawy są wykonywane dla jednego wystąpienia w złej kondycji naraz.
5. Powyższy proces jest kontynuowany do momentu naprawienia wszystkich niezdrowych wystąpień w zestawie skalowania.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrona przed wystąpieniami i automatyczne naprawy

Jeśli wystąpienie w zestawie skalowania jest chronione przez zastosowanie jednej z [zasad ochrony,](./virtual-machine-scale-sets-instance-protection.md)wówczas automatyczne naprawy nie są wykonywane w tym wystąpieniu. Dotyczy to zarówno zasad ochrony: *Ochrona przed skalowaniem* i *chroń przed akcjami zestawu skalowania.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Zakończenie powiadomień i automatycznych napraw

Jeśli funkcja [powiadomienia o zakończeniu](./virtual-machine-scale-sets-terminate-notification.md) jest włączona na zestawie skalowania, podczas automatycznej naprawy usunięcie wystąpienia w złej kondycji następuje po zakończeniu konfiguracji powiadomień. Powiadomienie o zakończeniu jest wysyłane za pośrednictwem usługi metadanych platformy Azure — zaplanowane zdarzenia — a usunięcie wystąpienia jest opóźnione o czas trwania skonfigurowanego limitu czasu opóźnienia. Jednak utworzenie nowego wystąpienia, aby zastąpić złej kondycji jeden nie czeka na limit czasu opóźnienia, aby zakończyć.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Włączanie zasad automatycznych napraw podczas tworzenia nowego zestawu skalowania

Aby włączyć zasady automatycznego napraw podczas tworzenia nowego zestawu skalowania, upewnij się, że wszystkie [wymagania dotyczące](#requirements-for-using-automatic-instance-repairs) włączenia tej funkcji są spełnione. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. W przypadku nowo utworzonych zestawów skalowania wszelkie naprawy wystąpień są wykonywane tylko po odczekaniu okresu prolongaty. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj *automatycznegoRepairsPolicy* obiektu w modelu zestawu skalowania maszyny wirtualnej.

Za pomocą tego [szablonu szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) można również wdrożyć zestaw skalowania maszyny wirtualnej z sondą kondycji modułu równoważenia obciążenia i automatyczną obsługą wystąpień z włączonym okresem prolongaty wynoszącym 30 minut.

### <a name="azure-portal"></a>Azure Portal
 
Poniższe kroki umożliwiające automatyczne naprawianie zasad podczas tworzenia nowego zestawu skalowania.
 
1. Przejdź do **zestawów skalowania maszyny wirtualnej**.
1. Wybierz **+ Dodaj,** aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **Zdrowie.** 
1. Znajdź sekcję **Zdrowie.**
1. Włącz opcję **Monitoruj kondycję aplikacji.**
1. Znajdź sekcję **Zasady automatycznej naprawy.**
1. **Włącz** opcję **Napraw automatyczna.**
1. W **polu Okres prolongaty (min)** określ okres prolongaty w minutach, dozwolone wartości są od 30 do 90 minut. 
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz przycisk **Przejrzyj + utwórz.**

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie pokazano, jak włączyć automatyczną naprawę wystąpienia w modelu zestawu skalowania. Użyj wersji interfejsu API 2018-10-01 lub nowszej.

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

Funkcję automatycznej naprawy wystąpienia można włączyć podczas tworzenia nowego zestawu skalowania przy użyciu polecenia cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Ten przykładowy skrypt przechodzi przez tworzenie zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyny wirtualnej](./scripts/powershell-sample-create-complete-scale-set.md). Zasady automatycznego naprawy wystąpienia można skonfigurować, dodając parametry *EnableAutomaticRepair* i *AutomaticRepairGracePeriod* do obiektu konfiguracji do tworzenia zestawu skalowania. Poniższy przykład włącza operację z okresem prolongaty 30 minut.

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

Poniższy przykład umożliwia zasady automatycznego naprawy podczas tworzenia nowego zestawu skalowania przy użyciu *[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Najpierw utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z okresem prolongaty zasad automatycznej naprawy ustawionym na 30 minut.

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
  --automatic-repairs-period 30
```

W powyższym przykładzie użyto istniejącego modułu równoważenia obciążenia i sondy kondycji do monitorowania stanu kondycji aplikacji wystąpień. Jeśli wolisz użyć rozszerzenia kondycji aplikacji do monitorowania zamiast tego, można utworzyć zestaw skalowania, skonfigurować rozszerzenie kondycji aplikacji, a następnie włączyć automatyczne zasady naprawy wystąpienia przy użyciu *aktualizacji az vmss*, jak wyjaśniono w następnej sekcji.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Włączanie zasad automatycznych napraw podczas aktualizowania istniejącego zestawu skalowania

Przed włączeniem zasad automatycznego naprawy w istniejącym zestawie skalowania upewnij się, że wszystkie [wymagania dotyczące](#requirements-for-using-automatic-instance-repairs) włączenia tej funkcji są spełnione. Punkt końcowy aplikacji powinien być poprawnie skonfigurowany dla wystąpień zestawu skalowania, aby uniknąć wyzwalania niezamierzonych napraw podczas konfigurowania punktu końcowego. Aby włączyć automatyczną naprawę wystąpienia w zestawie skalowania, użyj *automatycznegoRepairsPolicy* obiektu w modelu zestawu skalowania maszyny wirtualnej.

Po zaktualizowaniu modelu istniejącego zestawu skalowania upewnij się, że najnowszy model jest stosowany do wszystkich wystąpień skali. Zapoznaj się z [instrukcją, jak aktualizować maszyny wirtualne z najnowszym modelem zestawu skalowania](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

Można zmodyfikować zasady automatycznego naprawy istniejącego zestawu skalowania za pośrednictwem witryny Azure portal. 
 
1. Przejdź do istniejącego zestawu skalowania maszyny wirtualnej.
1. W obszarze **Ustawienia** w menu po lewej stronie wybierz pozycję **Zdrowie i napraw .**
1. Włącz opcję **Monitoruj kondycję aplikacji.**
1. Znajdź sekcję **Zasady automatycznej naprawy.**
1. **Włącz** opcję **Napraw automatyczna.**
1. W **polu Okres prolongaty (min)** określ okres prolongaty w minutach, dozwolone wartości są od 30 do 90 minut. 
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład umożliwia zasady z okresem prolongaty 40 minut. Użyj wersji interfejsu API 2018-10-01 lub nowszej.

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

Polecenie cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) służy do modyfikowania konfiguracji funkcji automatycznego naprawy wystąpienia w istniejącym zestawie skalowania. W poniższym przykładzie aktualizuje okres prolongaty do 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniżej przedstawiono przykład aktualizacji zasad automatycznego naprawy wystąpienia istniejącego zestawu skalowania przy użyciu *[aktualizacji az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Wyświetlanie i aktualizowanie stanu usługi automatycznego instancja napraw zasady

### <a name="rest-api"></a>Interfejs API REST 

Użyj [widoku Wystąpienia Pobierz](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) z wersją interfejsu API 2019-12-01 lub nowszą dla zestawu skalowania maszyny wirtualnej, aby wyświetlić *serviceState* dla automatycznych napraw w ramach *aranżacji właściwościUsługi*. 

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

Użyj *setOrchestrationServiceState* API z api w wersji 2019-12-01 lub nowszej w skali maszyny wirtualnej ustawionej, aby ustawić stan automatycznych napraw. Po wybraniu zestawu skalowania do funkcji automatycznego naprawy można użyć tego interfejsu API, aby zawiesić lub wznowić automatyczne naprawy zestawu skalowania. 

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

Użyj polecenia cmdlet [get-instance-view,](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) aby wyświetlić *serviceState* dla automatycznych napraw wystąpień. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Użyj polecenia cmdlet [set-orchestration-service-state,](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) aby zaktualizować *serviceState* dla automatycznych napraw wystąpień. Po wydzieleniu zestawu skalowania do funkcji automatycznej naprawy, możesz użyć tego polecenia cmdlet, aby zawiesić lub wznowić automatyczne naprawy dla zestawu skalowania. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) z *parametrem InstanceView,* aby wyświetlić *servicestate* dla automatycznych napraw wystąpień.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Użyj polecenia cmdlet Set-AzVmssOrchestrationServiceState, aby zaktualizować *serviceState* dla automatycznych napraw wystąpień. Po wydzieleniu zestawu skalowania do funkcji automatycznej naprawy można użyć tego polecenia cmdlet, aby zawiesić lub wznowić automatyczne naprawy zestawu skalowania.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Niepodtrzymanie zasad automatycznego napraw**

Jeśli pojawi się błąd "BadRequest" z komunikatem "Nie można znaleźć elementu członkowskiego 'automaticRepairsPolicy' na obiekcie typu 'właściwości'", sprawdź wersję interfejsu API używaną do zestawu skalowania maszyny wirtualnej. Dla tej funkcji wymagana jest wersja interfejsu API 2018-10-01 lub nowsza.

**Wystąpienie nie jest naprawione nawet wtedy, gdy zasada jest włączona**

Wystąpienie może być w okresie prolongaty. Jest to czas oczekiwania po każdej zmianie stanu w wystąpieniu przed wykonaniem napraw. Ma to na celu uniknięcie przedwczesnych lub przypadkowych napraw. Akcja naprawy powinna nastąpić po zakończeniu okresu prolongaty dla wystąpienia.

**Wyświetlanie stanu kondycji aplikacji dla wystąpień zestawu skalowania**

Można użyć [get instance view api](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) dla wystąpień w zestawie skali maszyny wirtualnej, aby wyświetlić stan kondycji aplikacji. Za pomocą programu Azure PowerShell można użyć polecenia cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) z flagą *-InstanceView.* Stan kondycji aplikacji jest podany w ramach właściwości *vmHealth*.

W witrynie Azure portal można zobaczyć stan kondycji, jak również. Przejdź do istniejącego zestawu skalowania, wybierz **wystąpienia** z menu po lewej stronie i spójrz na kolumnę **Stan kondycji** dla stanu kondycji każdego wystąpienia zestawu skalowania. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować [rozszerzenie kondycji aplikacji](./virtual-machine-scale-sets-health-extension.md) lub [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) dla zestawów skalowania.
