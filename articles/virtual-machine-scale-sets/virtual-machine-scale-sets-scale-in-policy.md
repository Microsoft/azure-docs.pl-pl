---
title: Korzystanie z niestandardowych zasad skalowania przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak używać niestandardowych zasad skalowania przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure, które używają konfiguracji skalowania automatycznego do zarządzania liczbą wystąpień
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747801"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Korzystanie z niestandardowych zasad skalowania przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure

Wdrożenie zestawu skalowania maszyn wirtualnych może być skalowane lub skalowane w oparciu o tablicę metryk, w tym platformę i metryki niestandardowe zdefiniowane przez użytkownika. Podczas gdy skalowanie w poziomie tworzy nowe maszyny wirtualne oparte na modelu zestawu skalowania, skalowanie w poziomie ma wpływ na uruchomione maszyny wirtualne, które mogą mieć różne konfiguracje i/lub funkcje w miarę rozwoju obciążeń zestawu skalowania. 

Funkcja skalowania w poziomie umożliwia użytkownikom skonfigurowanie kolejności skalowania maszyn wirtualnych w poziomie przy użyciu trzech konfiguracji skalowania: 

1. Domyślne
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Domyślne zasady skalowania

Domyślnie zestaw skalowania maszyn wirtualnych stosuje te zasady, aby określić, które wystąpienia będą skalowane. W przypadku *domyślnych* zasad maszyny wirtualne są wybierane w celu skalowania w poziomie w następującej kolejności:

1. Równoważyć obciążenie maszyn wirtualnych w różnych strefach dostępności (Jeśli zestaw skalowania jest wdrożony w konfiguracji strefowej)
2. Równoważyć maszynom wirtualnym w domenach błędów (najlepszy nakład pracy)
3. Usuń maszynę wirtualną z najwyższym IDENTYFIKATORem wystąpienia

Użytkownicy nie muszą określać zasad skalowania, jeśli chcą, aby kolejność była stosowana domyślnie.

Należy zauważyć, że równoważenie w strefach dostępności lub domenach błędów nie przenosi wystąpień między strefami dostępności ani domenami błędów. Równoważenie jest realizowane za pośrednictwem usuwania maszyn wirtualnych z niezrównoważonych stref dostępności lub domen błędów do momentu zrównoważenia dystrybucji maszyn wirtualnych.

### <a name="newestvm-scale-in-policy"></a>Zasady skalowania w NewestVM

Te zasady spowodują usunięcie najnowszej utworzonej maszyny wirtualnej w zestawie skalowania po przeprowadzeniu równoważenia maszyn wirtualnych między strefami dostępności (dla wdrożeń strefowych). Włączenie tych zasad wymaga zmiany konfiguracji modelu zestawu skalowania maszyn wirtualnych.

### <a name="oldestvm-scale-in-policy"></a>Zasady skalowania w OldestVM

Te zasady spowodują usunięcie najstarszej utworzonej maszyny wirtualnej w zestawie skalowania po przeprowadzeniu równoważenia maszyn wirtualnych w strefach dostępności (dla wdrożeń strefowych). Włączenie tych zasad wymaga zmiany konfiguracji modelu zestawu skalowania maszyn wirtualnych.

## <a name="enabling-scale-in-policy"></a>Włączanie zasad skalowania w poziomie

Zasady skalowania są zdefiniowane w modelu zestawu skalowania maszyn wirtualnych. Jak wskazano w powyższych sekcjach, w przypadku używania zasad "NewestVM" i "OldestVM" jest wymagana definicja zasad skalowania w poziomie. Zestaw skalowania maszyn wirtualnych automatycznie użyje "domyślnych" zasad skalowania, jeśli w modelu zestawu skalowania nie znaleziono definicji zasad skalowania. 

Zasady skalowania w poziomie można definiować w modelu zestawu skalowania maszyn wirtualnych w następujący sposób:

### <a name="azure-portal"></a>Azure Portal
 
Poniższe kroki definiują zasady skalowania podczas tworzenia nowego zestawu skalowania. 
 
1. Przejdź do **zestawu skalowania maszyn wirtualnych**.
1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **skalowanie** . 
1. Znajdź sekcję **zasady skalowania w poziomie** .
1. Wybierz zasady skalowania z listy rozwijanej.
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz pozycję **Recenzja + Utwórz** .

### <a name="using-api"></a>Używanie interfejsu API

Wykonaj umieszczenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania z zasadami skalowania w poziomie ustawionym jako *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniższy przykład dodaje zasady skalowania podczas tworzenia nowego zestawu skalowania. Najpierw utwórz grupę zasobów, a następnie utwórz nowy zestaw skalowania przy użyciu zasad skalowania jako *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Korzystanie z szablonu

W szablonie w obszarze "właściwości" Dodaj następujące elementy:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Powyższe bloki określają, że zestaw skalowania maszyn wirtualnych usunie najstarszą maszynę wirtualną w zestawie skalowania z równoważną strefą, gdy zostanie wyzwolona skalowanie (za pomocą automatycznego skalowania lub usuwania ręcznego).

Gdy zestaw skalowania maszyn wirtualnych nie jest zrównoważony przez strefę, zestaw skalowania najpierw usunie maszyny wirtualne w strefach, które nie zostały zbilansowane. W strefach ze zrównoważonym obciążeniem zestaw skalowania użyje określonych powyżej zasad skalowania, aby określić, która maszyna wirtualna ma zostać przeskalowana. W takim przypadku w strefie niezrównoważonej zestaw skalowania wybierze najstarszą maszynę wirtualną w tej strefie, która ma zostać usunięta.

W przypadku zestawu skalowania maszyn wirtualnych niebędących strefami zasady wybierają najstarszą maszynę wirtualną w zestawie skalowania do usunięcia.

Ten sam proces ma zastosowanie w przypadku używania "NewestVM" w powyższych zasadach skalowania w poziomie.

## <a name="modifying-scale-in-policies"></a>Modyfikowanie zasad skalowania w poziomie

Modyfikacja zasad skalowania odbywa się zgodnie z tym samym procesem co zastosowanie zasad skalowania w poziomie. Na przykład jeśli w powyższym przykładzie chcesz zmienić zasady z "OldestVM" na "NewestVM", możesz to zrobić, wykonując następujące czynności:

### <a name="azure-portal"></a>Azure Portal

Możesz zmodyfikować zasady skalowania istniejącego zestawu skalowania za pomocą Azure Portal. 
 
1. W istniejącym zestawie skalowania maszyn wirtualnych wybierz pozycję **skalowanie** z menu po lewej stronie.
1. Wybierz kartę **zasady skalowania w poziomie** .
1. Wybierz zasady skalowania z listy rozwijanej.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. 

### <a name="using-api"></a>Używanie interfejsu API

Wykonaj umieszczenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Zaktualizuj zasady skalowania w istniejącym zestawie skalowania:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniżej przedstawiono przykład aktualizowania zasad skalowania w istniejącym zestawie skalowania: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Korzystanie z szablonu

W szablonie w obszarze "właściwości" zmodyfikuj szablon jak poniżej i Wdróż ponownie: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Ten sam proces zostanie zastosowany, jeśli zdecydujesz się zmienić "NewestVM" na "default" lub "OldestVM"

## <a name="instance-protection-and-scale-in-policy"></a>Zasady ochrony wystąpień i skalowania

Zestawy skalowania maszyn wirtualnych oferują dwa typy [ochrony wystąpienia](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Ochrona przed skalowaniem w poziomie
2. Ochrona przed akcjami zestawu skalowania

Chroniona maszyna wirtualna nie jest usuwana za pomocą akcji skalowania, niezależnie od zastosowanych zasad skalowania. Na przykład jeśli VM_0 (najstarsza maszyna wirtualna w zestawie skalowania) jest chroniona przed skalowaniem w poziomie, a zestaw skalowania ma włączone zasady skalowania "OldestVM", VM_0 nie będzie brana pod uwagę podczas skalowania w, mimo że jest najstarszą maszyną wirtualną w zestawie skalowania. 

Chroniona maszyna wirtualna może zostać ręcznie usunięta przez użytkownika w dowolnym momencie, niezależnie od zasad skalowania w poziomie włączonych w zestawie skalowania. 

## <a name="usage-examples"></a>Przykłady użycia 

W poniższych przykładach pokazano, jak zestaw skalowania maszyn wirtualnych będzie wybierać maszyny wirtualne do usunięcia, gdy zostanie wyzwolone zdarzenie skalowania. Założono, że maszyny wirtualne o największych identyfikatorach wystąpienia są najnowszymi maszynami wirtualnymi w zestawie skalowania, a maszyny wirtualne z najmniejszymi identyfikatorami wystąpień są zakładane jako najstarsze maszyny wirtualne w zestawie skalowania. 

### <a name="oldestvm-scale-in-policy"></a>Zasady skalowania w OldestVM

| Zdarzenie                 | Identyfikatory wystąpień w strefie 1  | Identyfikatory wystąpień w strefie 2  | Identyfikatory wystąpień w strefie 3  | Skalowanie w poziomie                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Wartość początkowa               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skalowanie w poziomie              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Wybierz między Strefa 1 i 2, chociaż Strefa 3 ma najstarszą maszynę wirtualną. Usuń VM2 z Strefa 2, ponieważ jest najstarszą maszyną wirtualną w tej strefie.   |
| Skalowanie w poziomie              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Wybierz Strefa 1, mimo że Strefa 3 ma najstarszą maszynę wirtualną. Usuń VM3 z Strefa 1, ponieważ jest najstarszą maszyną wirtualną w tej strefie.                  |
| Skalowanie w poziomie              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Strefy są zrównoważone. Usuń VM1 w Strefa 3, ponieważ jest najstarszą maszyną wirtualną w zestawie skalowania.                                               |
| Skalowanie w poziomie              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Wybierz między Strefa 1 i Strefa 2. Usuń VM4 w Strefa 1 jak najstarsza maszyna wirtualna w obu strefach.                              |
| Skalowanie w poziomie              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Wybierz Strefa 2, mimo że Strefa 1 ma najstarszą maszynę wirtualną. Usuń VM6 w Strefa 1, ponieważ jest najstarszą maszyną wirtualną w tej strefie.                    |
| Skalowanie w poziomie              | ***5***, 10            | 9, 11                  | 7, 8                   | Strefy są zrównoważone. Usuń VM5 w Strefa 1, ponieważ jest najstarszą maszyną wirtualną w zestawie skalowania.                                                |

W przypadku zestawów skalowania maszyn wirtualnych niebędących strefami zasady wybierają najstarszą maszynę wirtualną w zestawie skalowania do usunięcia. Wszystkie chronione maszyny wirtualne zostaną pominięte do usunięcia.

### <a name="newestvm-scale-in-policy"></a>Zasady skalowania w NewestVM

| Zdarzenie                 | Identyfikatory wystąpień w strefie 1  | Identyfikatory wystąpień w strefie 2  | Identyfikatory wystąpień w strefie 3  | Skalowanie w poziomie                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Wartość początkowa               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skalowanie w poziomie              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Wybierz między Strefa 1 i 2. Usuń MW11 z Strefa 2, ponieważ jest to najnowsza maszyna wirtualna w ramach obu tych stref.                                |
| Skalowanie w poziomie              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Wybierz Strefa 1, ponieważ zawiera więcej maszyn wirtualnych niż pozostałe dwie strefy. Usuń MW10 z Strefa 1, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.          |
| Skalowanie w poziomie              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Strefy są zrównoważone. Usuń VM9 w Strefa 2, ponieważ jest to najnowsza maszyna wirtualna w zestawie skalowania.                                                |
| Skalowanie w poziomie              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Wybierz między Strefa 1 i Strefa 3. Usuń VM8 w Strefa 3, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.                                      |
| Skalowanie w poziomie              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Wybierz Strefa 1, mimo że Strefa 3 ma najnowszą maszynę wirtualną. Usuń VM5 w Strefa 1, ponieważ jest to najnowsza maszyna wirtualna w tej strefie.                    |
| Skalowanie w poziomie              | 3, 4                   | 2, 6                   | 1, ***7***             | Strefy są zrównoważone. Usuń VM7 w Strefa 3, ponieważ jest to najnowsza maszyna wirtualna w zestawie skalowania.                                                |

W przypadku zestawów skalowania maszyn wirtualnych niebędących strefami zasady wybierają najnowszą maszynę wirtualną w zestawie skalowania do usunięcia. Wszystkie chronione maszyny wirtualne zostaną pominięte do usunięcia. 

## <a name="troubleshoot"></a>Rozwiązywanie problemów

1. Nie można włączyć scaleInPolicy, jeśli wystąpi błąd "nieprawidłowego żądania" z komunikatem o błędzie z informacją "nie można odnaleźć elementu członkowskiego" scaleInPolicy "w obiekcie typu" Properties "", a następnie sprawdź wersję interfejsu API używaną dla zestawu skalowania maszyn wirtualnych. Dla tej funkcji wymagany jest interfejs API w wersji 2019-03-01 lub nowszej.

2. Niewłaściwy wybór maszyn wirtualnych do skalowania w górę zapoznaj się z powyższymi przykładami. Jeśli zestaw skalowania maszyn wirtualnych jest wdrożeniem strefowym, zasady skalowania w poziomie są stosowane najpierw w strefach ze zrównoważonym obciążeniem, a następnie przez zestaw skalowania, gdy jest on zbilansowany. Jeśli kolejność skalowania w poziomie nie jest spójna z powyższymi przykładami, zgłoś zapytanie z zespołem zestawu skalowania maszyn wirtualnych w celu rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
