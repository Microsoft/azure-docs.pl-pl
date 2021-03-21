---
title: Automatyczne uaktualnianie obrazów systemu operacyjnego przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure
description: Informacje o automatycznym uaktualnianiu obrazu systemu operacyjnego na wystąpieniach maszyn wirtualnych w zestawie skalowania
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff1a29577c0778d6ef88d3523c726f7a48739cdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684614"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania maszyn wirtualnych platformy Azure

Włączenie automatycznych uaktualnień obrazu systemu operacyjnego w zestawie skalowania ułatwia zarządzanie aktualizacjami, bezpiecznie i automatycznie uaktualnia dysk systemu operacyjnego dla wszystkich wystąpień w zestawie skalowania.

Automatyczne uaktualnianie systemu operacyjnego ma następującą charakterystykę:

- Po skonfigurowaniu najnowszy obraz systemu operacyjnego Opublikowany przez wydawców obrazów jest automatycznie stosowany do zestawu skalowania bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób ciągły za każdym razem, gdy nowy obraz jest publikowany przez wydawcę.
- Integruje się z sondami kondycji aplikacji i [rozszerzeniem kondycji aplikacji](virtual-machine-scale-sets-health-extension.md).
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz obrazów systemów Windows i Linux.
- Możesz zrezygnować z automatycznych uaktualnień w dowolnym momencie (uaktualnienia systemu operacyjnego można także zainicjować ręcznie).
- Dysk systemu operacyjnego maszyny wirtualnej jest zastępowany nowym dyskiem systemu operacyjnego utworzonym przy użyciu najnowszej wersji obrazu. Skonfigurowane rozszerzenia i niestandardowe skrypty danych są uruchamiane, podczas gdy utrwalane dyski danych są zachowywane.
- [Sekwencjonowanie rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md) jest obsługiwane.
- Automatyczne uaktualnianie obrazu systemu operacyjnego można włączyć w zestawie skalowania o dowolnym rozmiarze.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak działa automatyczna aktualizacja obrazu systemu operacyjnego?

Uaktualnienie działa przez zastąpienie dysku systemu operacyjnego maszyny wirtualnej nowym dyskiem utworzonym przy użyciu najnowszej wersji obrazu. Wszystkie skonfigurowane rozszerzenia i skrypty danych niestandardowych są uruchamiane na dysku systemu operacyjnego, podczas gdy utrwalane dyski danych są zachowywane. Aby zminimalizować przestoje aplikacji, uaktualnienia odbywają się w partiach, ale nie więcej niż 20% zestawu skalowania w dowolnym momencie. Można także zintegrować Azure Load Balancer z sondą kondycji aplikacji lub [rozszerzeniem kondycji aplikacji](virtual-machine-scale-sets-health-extension.md). Zalecamy włączenie pulsu aplikacji i zweryfikowanie sukcesu uaktualnienia dla każdej partii w procesie uaktualniania.

Proces uaktualniania przebiega w następujący sposób:
1. Przed rozpoczęciem procesu uaktualniania program Orchestrator sprawdzi, czy nie ma więcej niż 20% wystąpień w całym zestawie skalowania (z jakiegokolwiek powodu).
2. Program Orchestrator Upgrades identyfikuje partię wystąpień maszyn wirtualnych do uaktualnienia, a jedna partia ma maksymalnie 20% całkowitej liczby wystąpień, z zastrzeżeniem minimalnej wielkości partii jednej maszyny wirtualnej.
3. Dysk systemu operacyjnego wybranej partii wystąpień maszyn wirtualnych został zastąpiony nowym dyskiem systemu operacyjnego utworzonym na podstawie najnowszego obrazu. Wszystkie określone rozszerzenia i konfiguracje w modelu zestawu skalowania są stosowane do uaktualnionego wystąpienia.
4. W przypadku zestawów skalowania ze skonfigurowanymi sondami kondycji aplikacji lub rozszerzeniem kondycji aplikacji uaktualnienie czeka do 5 minut, zanim wystąpienie stanie się w dobrej kondycji, przed przejściem do uaktualnienia następnej partii. Jeśli wystąpienie nie odzyska kondycji w ciągu 5 minut od uaktualnienia, domyślnie przywracany jest poprzedni dysk systemu operacyjnego dla tego wystąpienia.
5. W ramach uaktualnienia programu Orchestrator jest również śledzony procent wystąpień, które staną się w złej kondycji. Uaktualnienie zostanie zatrzymane, jeśli ponad 20% uaktualnionych wystąpień stanie się zła w trakcie procesu uaktualniania.
6. Powyższy proces jest kontynuowany do momentu uaktualnienia wszystkich wystąpień w zestawie skalowania.

Aktualizacja systemu operacyjnego zestawu skalowania programu Orchestrator sprawdza dostępność ogólnego zestawu skalowania przed uaktualnieniem każdej partii. Podczas uaktualniania partii mogą istnieć inne współbieżne planowane lub nieplanowane działania konserwacyjne, które mogą mieć wpływ na kondycję wystąpień zestawów skalowania. W takich przypadkach, jeśli więcej niż 20% wystąpień zestawu skalowania stanie się zła, wówczas uaktualnienie zestawu skalowania zostanie zatrzymane na końcu bieżącej partii.

> [!NOTE]
>Automatyczne uaktualnianie systemu operacyjnego nie uaktualnia jednostki SKU obrazu referencyjnego w zestawie skalowania. Aby zmienić jednostkę SKU (na przykład Ubuntu 16,04-LTS do 18,04-LTS), należy zaktualizować [model zestawu skalowania](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) bezpośrednio z odpowiednią jednostką SKU obrazu. Nie można zmienić wydawcy i oferty obrazu dla istniejącego zestawu skalowania.  

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Obrazy niestandardowe [są obsługiwane](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) , jeśli zestaw skalowania używa obrazów niestandardowych za pośrednictwem [galerii obrazów udostępnionych](../virtual-machines/shared-image-galleries.md).

Następujące jednostki SKU platformy są obecnie obsługiwane (i więcej jest dodawanych okresowo):

| Publisher               | Oferta systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18,04 – LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 — centrum danych    |
| MicrosoftWindowsServer  | WindowsServer | 2016 — Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016 — Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019 — centrum danych |
| MicrosoftWindowsServer  | WindowsServer | 2019 — Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019 — Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Wymagania dotyczące konfigurowania automatycznego uaktualniania obrazu systemu operacyjnego

- Właściwość *Version* obrazu musi być ustawiona na wartość *Najnowsza*.
- Użyj sond kondycji aplikacji lub [rozszerzenia kondycji aplikacji](virtual-machine-scale-sets-health-extension.md) dla zestawów skalowania, które nie są Service Fabric.
- Użyj interfejsu API obliczeń w wersji 2018-10-01 lub nowszej.
- Upewnij się, że zasoby zewnętrzne określone w modelu zestawu skalowania są dostępne i zaktualizowane. Przykłady obejmują identyfikator URI sygnatury dostępu współdzielonego dla ładowania ładunku we właściwościach rozszerzenia maszyny wirtualnej, ładunek na koncie magazynu, odwołanie do wpisów tajnych w modelu i inne.
- W przypadku zestawów skalowania korzystających z maszyn wirtualnych z systemem Windows począwszy od interfejsu API obliczeń w wersji 2019-03-01, właściwość *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* musi mieć wartość *false* w definicji modelu zestawu skalowania. Powyższa Właściwość włącza uaktualnienia w maszynie wirtualnej, gdzie "Windows Update" stosuje poprawki systemu operacyjnego bez zastępowania dysku systemu operacyjnego. Po włączeniu automatycznych uaktualnień obrazu systemu operacyjnego w zestawie skalowania dodatkowa aktualizacja za pomocą "Windows Update" nie jest wymagana.

### <a name="service-fabric-requirements"></a>Wymagania Service Fabric

W przypadku korzystania z Service Fabric upewnij się, że zostały spełnione następujące warunki:
-   [Poziom trwałości](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric to Silver lub Gold, a nie brązowy.
-   Rozszerzenie Service Fabric w definicji modelu zestawu skalowania musi mieć wartość TypeHandlerVersion 1,1 lub nowszą.
-   Poziom trwałości powinien być taki sam w przypadku klastra Service Fabric i rozszerzenia Service Fabric w definicji modelu zestawu skalowania.
- Dodatkowa sonda kondycji lub użycie rozszerzenia kondycji aplikacji nie jest wymagane.

Upewnij się, że ustawienia trwałości nie są niezgodne w przypadku klastra Service Fabric i rozszerzenia Service Fabric, ponieważ niezgodność spowoduje błędy uaktualniania. Poziomy trwałości można modyfikować zgodnie z wytycznymi opisanymi na [tej stronie](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatyczne uaktualnianie obrazu systemu operacyjnego dla obrazów niestandardowych

Automatyczne uaktualnianie obrazu systemu operacyjnego jest obsługiwane w przypadku obrazów niestandardowych wdrożonych za poorednictwem [galerii obrazów udostępnionych](../virtual-machines/shared-image-galleries.md). Inne obrazy niestandardowe nie są obsługiwane na potrzeby automatycznych uaktualnień obrazu systemu operacyjnego.

### <a name="additional-requirements-for-custom-images"></a>Dodatkowe wymagania dotyczące obrazów niestandardowych
- Proces instalacji i konfiguracji automatycznego uaktualniania obrazu systemu operacyjnego jest taki sam dla wszystkich zestawów skalowania, jak opisano w [sekcji Konfiguracja](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) tej strony.
- Wystąpienia zestawów skalowania skonfigurowane na potrzeby automatycznych uaktualnień obrazu systemu operacyjnego zostaną uaktualnione do najnowszej wersji obrazu udostępnionej galerii obrazów po opublikowaniu nowej wersji obrazu i [replikacji](../virtual-machines/shared-image-galleries.md#replication) do regionu tego zestawu skalowania. Jeśli nowy obraz nie jest replikowany do regionu, w którym wdrożono skalowanie, wystąpienia zestawu skalowania nie zostaną uaktualnione do najnowszej wersji. Funkcja replikacji obrazów regionalnych pozwala kontrolować wprowadzanie nowego obrazu dla zestawów skalowania.
- Nie można wykluczyć nowej wersji obrazu z najnowszej wersji dla tego obrazu galerii. Wersje obrazów wykluczone z najnowszej wersji obrazu galerii nie są przełączane do zestawu skalowania za pośrednictwem automatycznego uaktualniania obrazu systemu operacyjnego.

> [!NOTE]
>Zestaw skalowania może potrwać do 3 godzin, aby wyzwolić wdrożenie pierwszego obrazu po skonfigurowaniu zestawu skalowania na potrzeby automatycznych uaktualnień systemu operacyjnego. Jest to jednorazowe opóźnienie na zestaw skalowania. Kolejne wdrożenia obrazu są wyzwalane na zestawie skalowania w ciągu 30-60 minut.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurowanie automatycznego uaktualniania obrazu systemu operacyjnego
Aby skonfigurować automatyczne uaktualnianie obrazu systemu operacyjnego, należy się upewnić, że właściwość *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* ma wartość *true* w definicji modelu zestawu skalowania.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób ustawiania automatycznych uaktualnień systemu operacyjnego w modelu zestawu skalowania:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Za pomocą polecenia cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) Skonfiguruj automatyczne uaktualnianie obrazu systemu operacyjnego dla zestawu skalowania. Poniższy przykład służy do konfigurowania automatycznych uaktualnień zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *zasobów*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) , aby skonfigurować automatyczne uaktualnienia obrazu systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub nowszego. Poniższy przykład służy do konfigurowania automatycznych uaktualnień zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Po skonfigurowaniu automatycznych uaktualnień obrazu systemu operacyjnego dla zestawu skalowania należy również przenieść maszyny wirtualne zestawu skalowania do najnowszego modelu zestawu skalowania, jeśli zestaw skalowania korzysta z [zasad uaktualniania](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)"ręczne".

## <a name="using-application-health-probes"></a>Korzystanie z sond kondycji aplikacji

Podczas uaktualniania systemu operacyjnego wystąpienia maszyn wirtualnych w zestawie skalowania są uaktualniane po jednej partii jednocześnie. Uaktualnienie powinno być kontynuowane tylko wtedy, gdy aplikacja klienta jest w dobrej kondycji na uaktualnionych wystąpieniach maszyn wirtualnych. Zalecamy, aby aplikacja dostarcza sygnały kondycji do aparatu uaktualniania zestawu skalowania systemu operacyjnego. Domyślnie podczas uaktualniania systemu operacyjnego platforma rozważa stan stanu i aprowizacji maszyny wirtualnej, aby określić, czy wystąpienie maszyny wirtualnej jest w dobrej kondycji po uaktualnieniu. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny wirtualnej dysk systemu operacyjnego w wystąpieniu maszyny wirtualnej jest zastępowany nowym dyskiem w oparciu o najnowszą wersję obrazu. Po zakończeniu uaktualniania systemu operacyjnego skonfigurowane rozszerzenia są uruchamiane na tych maszynach wirtualnych. Aplikacja jest traktowana jako w dobrej kondycji, gdy pomyślnie zainicjowano wszystkie rozszerzenia w wystąpieniu.

Zestaw skalowania można opcjonalnie skonfigurować przy użyciu sond kondycji aplikacji, aby zapewnić platformę z dokładnymi informacjami o stanie trwającym aplikacji. Sondy kondycji aplikacji to niestandardowe sondy Load Balancer, które są używane jako sygnał kondycji. Aplikacja uruchomiona w wystąpieniu maszyny wirtualnej zestawu skalowania może odpowiadać na zewnętrzne żądania HTTP lub TCP, wskazując, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat działania niestandardowych sond Load Balancer, zobacz Aby [zrozumieć sondy modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Sondy kondycji aplikacji nie są obsługiwane w przypadku zestawów skalowania Service Fabric. Zestawy skalowania inne niż Service Fabric wymagają Load Balancer sond kondycji aplikacji lub [rozszerzenia kondycji aplikacji](virtual-machine-scale-sets-health-extension.md).

Jeśli zestaw skalowania jest skonfigurowany tak, aby korzystał z wielu grup umieszczania, sondy używające [Usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md) muszą być używane.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie sondy Load Balancer niestandardowego jako sondy kondycji aplikacji w zestawie skalowania
Najlepszym rozwiązaniem jest tworzenie sondy modułu równoważenia obciążenia jawnie dla kondycji zestawu skalowania. Można użyć tego samego punktu końcowego dla istniejącej sondy HTTP lub sondy TCP, ale sonda kondycji może wymagać innego zachowania od tradycyjnej sondy modułu równoważenia obciążenia. Na przykład tradycyjna sonda modułu równoważenia obciążenia może zwracać stan w złej kondycji, jeśli obciążenie wystąpienia jest zbyt duże, ale nie jest to odpowiednie do określania kondycji wystąpienia podczas automatycznego uaktualniania systemu operacyjnego. Skonfiguruj sondę w taki sposób, aby miała wysoką częstotliwość sondowania mniejszą niż dwie minuty.

Sondcy równoważenia obciążenia można przywoływać w *networkProfile* zestawu skalowania i można go skojarzyć z wewnętrznym lub publicznym modułem równoważenia obciążenia w następujący sposób:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> W przypadku korzystania z automatycznych uaktualnień systemu operacyjnego z Service Fabric nowy obraz systemu operacyjnego jest wdrażany w domenie aktualizacji w celu zapewnienia wysokiej dostępności usług działających w Service Fabric. Aby korzystać z automatycznych uaktualnień systemu operacyjnego w Service Fabric klaster musi być skonfigurowany do korzystania z warstwy trwałości Silver lub wyższej. Aby uzyskać więcej informacji na temat właściwości trwałości klastrów Service Fabric, zobacz [tę dokumentację](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Aktualizowanie poświadczeń na bieżąco
Jeśli zestaw skalowania korzysta z dowolnych poświadczeń w celu uzyskania dostępu do zasobów zewnętrznych, takich jak rozszerzenie maszyny wirtualnej skonfigurowane do używania tokenu sygnatury dostępu współdzielonego dla konta magazynu, upewnij się, że poświadczenia zostały zaktualizowane. Po wygaśnięciu jakichkolwiek poświadczeń, w tym certyfikatów i tokenów, uaktualnienie zakończy się niepowodzeniem, a pierwsza partia maszyn wirtualnych zostanie pozostawiona w stanie niepowodzenia.

Zalecane kroki odzyskiwania maszyn wirtualnych i ponownego włączania automatycznego uaktualniania systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów:

* Wygeneruj ponownie token (lub wszystkie inne poświadczenia) przesłane do swoich rozszerzeń.
* Upewnij się, że wszystkie poświadczenia używane z poziomu maszyny wirtualnej do komunikowania się z jednostkami zewnętrznymi są aktualne.
* Zaktualizuj rozszerzenia w modelu zestawu skalowania przy użyciu dowolnych nowych tokenów.
* Wdróż zaktualizowany zestaw skalowania, który zaktualizuje wszystkie wystąpienia maszyn wirtualnych, w tym awarie.

## <a name="using-application-health-extension"></a>Korzystanie z rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażane wewnątrz wystąpienia zestawu skalowania maszyn wirtualnych i raportuje kondycję maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i zaktualizować stan aplikacji w tym wystąpieniu. To wystąpienie jest sprawdzane przez platformę Azure w celu ustalenia, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie raportuje kondycję z maszyny wirtualnej, rozszerzenie może być używane w sytuacjach, w których nie można używać sond zewnętrznych, takich jak sondy kondycji aplikacji (które korzystają z [sond](../load-balancer/load-balancer-custom-probe-overview.md)Azure Load Balancer niestandardowych).

Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji w zestawach skalowania, zgodnie z opisem w przykładach w [tym artykule](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Pobierz historię automatycznych uaktualnień obrazu systemu operacyjnego
Historię najnowszych uaktualnień systemu operacyjnego wykonywanych w zestawie skalowania można sprawdzić za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure 2,0 lub interfejsów API REST. Można uzyskać historię dla ostatnich pięciu prób uaktualnienia systemu operacyjnego w ciągu ostatnich dwóch miesięcy.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie przedstawiono użycie [interfejsu API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) w celu sprawdzenia stanu zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *zasobów*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

Wywołanie GET zwraca właściwości podobne do następujących przykładowych danych wyjściowych:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) , aby sprawdzić historię uaktualnienia systemu operacyjnego dla zestawu skalowania. Poniższy przykład zawiera szczegółowe informacje dotyczące sposobu przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *zasobów*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Aby sprawdzić historię uaktualnienia systemu operacyjnego dla zestawu skalowania, użyj [AZ VMSS Get-OS-Upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) . Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub nowszego. Poniższy przykład zawiera szczegółowe informacje dotyczące sposobu przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak uzyskać najnowszą wersję obrazu systemu operacyjnego platformy?

Dostępne wersje obrazu dla automatycznych obsługiwanych jednostek SKU uaktualnienia systemu operacyjnego można uzyskać, korzystając z poniższych przykładów:

### <a name="rest-api"></a>Interfejs API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Ręcznie Wyzwól uaktualnienia obrazu systemu operacyjnego
Gdy włączone jest automatyczne uaktualnianie obrazu systemu operacyjnego w zestawie skalowania, nie trzeba ręcznie wyzwalać aktualizacji obrazów w zestawie skalowania. W przypadku uaktualnienia systemu operacyjnego program Orchestrator będzie automatycznie stosował najnowszą dostępną wersję obrazu do wystąpień zestawu skalowania bez żadnej ręcznej interwencji.

W przypadku określonych przypadków, w których nie chcesz czekać na zastosowanie najnowszego obrazu przez program Orchestrator, możesz wyzwolić uaktualnienie obrazu systemu operacyjnego ręcznie, korzystając z poniższych przykładów.

> [!NOTE]
> Ręczne wyzwalacze uaktualnień obrazów systemu operacyjnego nie zapewniają możliwości automatycznego wycofywania. Jeśli wystąpienie nie odzyska jego kondycji po zakończeniu operacji uaktualniania, jego poprzedni dysk systemu operacyjnego nie może zostać przywrócony.

### <a name="rest-api"></a>Interfejs API REST
Użyj wywołania interfejsu API [uruchamiania systemu operacyjnego](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) , aby rozpocząć uaktualnianie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu. Nie wpłynie to na wystąpienia, na których jest już uruchomiona najnowsza dostępna wersja systemu operacyjnego. Poniższy przykład zawiera szczegółowe informacje na temat sposobu uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *Grupa:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) , aby sprawdzić historię uaktualnienia systemu operacyjnego dla zestawu skalowania. Poniższy przykład zawiera szczegółowe informacje na temat sposobu uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *Grupa:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [AZ VMSS krocząc-upgrade Start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) , aby sprawdzić historię uaktualnienia systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub nowszego. Poniższy przykład zawiera szczegółowe informacje na temat sposobu uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie Moja *Grupa:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Za pomocą szablonów można wdrożyć zestaw skalowania z automatycznymi uaktualnieniami systemu operacyjnego dla obsługiwanych obrazów, takich jak [Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów dotyczących używania automatycznych uaktualnień systemu operacyjnego z zestawami skalowania, przejrzyj [repozytorium GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).