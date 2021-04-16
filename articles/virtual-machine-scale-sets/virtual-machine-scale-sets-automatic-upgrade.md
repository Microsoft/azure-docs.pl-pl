---
title: Automatyczne uaktualnienia obrazów systemu operacyjnego za pomocą zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak automatycznie uaktualnić obraz systemu operacyjnego na wystąpieniach maszyn wirtualnych w zestawie skalowania
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: automatic-os-upgrade
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 1e32ff4bc1c39e8a3385f8037f88cedbdc17d3a6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375750"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania maszyn wirtualnych platformy Azure

Włączenie automatycznych uaktualnień obrazów systemu operacyjnego w zestawie skalowania ułatwia zarządzanie aktualizacjami dzięki bezpiecznemu i automatycznemu uaktualnianiu dysku systemu operacyjnego dla wszystkich wystąpień w zestawie skalowania.

Automatyczne uaktualnianie systemu operacyjnego ma następujące cechy:

- Po skonfigurowaniu najnowszy obraz systemu operacyjnego opublikowany przez wydawców obrazów jest automatycznie stosowany do zestawu skalowania bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowy za każdym razem, gdy nowy obraz jest publikowany przez wydawcę.
- Integruje się z sondami kondycji aplikacji i [rozszerzeniem application Health.](virtual-machine-scale-sets-health-extension.md)
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych oraz obrazów systemów Windows i Linux.
- W dowolnym momencie możesz zrezygnować z automatycznych uaktualnień (uaktualnienia systemu operacyjnego można również zainicjować ręcznie).
- Dysk systemu operacyjnego maszyny wirtualnej został zastąpiony nowym dyskiem systemu operacyjnego utworzonym przy użyciu najnowszej wersji obrazu. Skonfigurowane rozszerzenia i skrypty danych niestandardowych są uruchamiane, podczas gdy utrwalone dyski danych są zachowywane.
- [Sekwencjonowanie rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md) jest obsługiwane.
- Automatyczne uaktualnianie obrazu systemu operacyjnego można włączyć w zestawie skalowania o dowolnym rozmiarze.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak działa automatyczne uaktualnianie obrazu systemu operacyjnego?

Uaktualnienie działa przez zastąpienie dysku systemu operacyjnego maszyny wirtualnej nowym dyskiem utworzonym przy użyciu najnowszej wersji obrazu. Wszystkie skonfigurowane rozszerzenia i skrypty danych niestandardowych są uruchamiane na dysku systemu operacyjnego, podczas gdy utrwalone dyski danych są zachowywane. Aby zminimalizować przestoje aplikacji, uaktualnienia są odbywają się w partiach, przy użyciu nie więcej niż 20% uaktualnień zestawu skalowania w dowolnym momencie. Można również zintegrować sondę kondycji Azure Load Balancer lub [rozszerzenie kondycji aplikacji.](virtual-machine-scale-sets-health-extension.md) Zalecamy włączenie pulsu aplikacji i zweryfikowanie powodzenia uaktualnienia dla każdej partii w procesie uaktualniania.

Proces uaktualniania działa w następujący sposób:
1. Przed rozpoczęciem procesu uaktualniania orkiestrator zapewni, że nie więcej niż 20% wystąpień w całym zestawie skalowania będzie w złej kondycji (z jakiegokolwiek powodu).
2. Orkiestrator uaktualniania identyfikuje partię wystąpień maszyn wirtualnych do uaktualnienia, przy której każda partia ma maksymalnie 20% łącznej liczby wystąpień, z zastrzeżeniem minimalnego rozmiaru partii jednej maszyny wirtualnej.
3. Dysk systemu operacyjnego wybranej partii wystąpień maszyn wirtualnych jest zastępowany nowym dyskiem systemu operacyjnego utworzonym na podstawie najnowszego obrazu. Wszystkie określone rozszerzenia i konfiguracje w modelu zestawu skalowania są stosowane do uaktualnionego wystąpienia.
4. W przypadku zestawów skalowania ze skonfigurowanymi sondami kondycji aplikacji lub rozszerzeniem kondycji aplikacji uaktualnienie oczekuje do 5 minut, aż wystąpienie stanie się w dobrej kondycji, przed przejściem do uaktualnienia następnej partii. Jeśli wystąpienie nie odzyska kondycji w ciągu 5 minut po uaktualnieniu, domyślnie jest przywracany poprzedni dysk systemu operacyjnego dla wystąpienia.
5. Orkiestrator uaktualniania śledzi również procent wystąpień, które po uaktualnieniu stają się w złej kondycji. Uaktualnienie zostanie zatrzymane, jeśli ponad 20% uaktualnionych wystąpień stanie się w złej kondycji podczas procesu uaktualniania.
6. Powyższy proces jest kontynuowany do momentu uaktualnienia wszystkich wystąpień w zestawie skalowania.

Orkiestrator uaktualniania systemu operacyjnego zestawu skalowania sprawdza ogólną kondycję zestawu skalowania przed uaktualnieniem każdej partii. Podczas uaktualniania partii mogą być wykonywane inne równoczesne planowane lub nieplanowane działania konserwacyjne, które mogą mieć wpływ na kondycję wystąpień zestawu skalowania. W takich przypadkach, jeśli więcej niż 20% wystąpień zestawu skalowania stanie się w złej kondycji, uaktualnienie zestawu skalowania zatrzyma się na końcu bieżącej partii.

> [!NOTE]
>Automatyczne uaktualnianie systemu operacyjnego nie uaktualnia wersji SKU obrazu referencyjnego w zestawie skalowania. Aby zmienić wartość SKU (na przykład Ubuntu 16.04-LTS na 18.04-LTS), należy zaktualizować [model](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) zestawu skalowania bezpośrednio przy użyciu żądanej wersji SKU obrazu. Wydawcy obrazów i oferty nie można zmienić dla istniejącego zestawu skalowania.  

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Obecnie obsługiwane są tylko niektóre obrazy platformy systemu operacyjnego. Obrazy [niestandardowe są obsługiwane,](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) jeśli zestaw skalowania używa obrazów niestandardowych za [pośrednictwem Shared Image Gallery](../virtual-machines/shared-image-galleries.md).

Obecnie obsługiwane są następujące jednostki SKU platformy (i kolejne są dodawane okresowo):

| Publisher               | Oferta systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016 Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019 Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Wymagania dotyczące konfigurowania automatycznego uaktualniania obrazu systemu operacyjnego

- Właściwość *version* obrazu musi mieć ustawioną *najnowszą wartość*.
- Używaj sond kondycji aplikacji lub [rozszerzenia kondycji aplikacji w](virtual-machine-scale-sets-health-extension.md) przypadku zestawów skalowania Service Fabric innych niż.
- Użyj interfejsu API obliczeń w wersji 2018-10-01 lub wyższej.
- Upewnij się, że zasoby zewnętrzne określone w modelu zestawu skalowania są dostępne i zaktualizowane. Przykłady obejmują identyfikator URI sygnatury dostępu współdzielonego dla ładowania początkowego ładunku we właściwościach rozszerzenia maszyny wirtualnej, ładunek na koncie magazynu, odwołanie do wpisów tajnych w modelu i inne.
- W przypadku zestawów skalowania korzystających z maszyn wirtualnych z systemem Windows, począwszy od właściwości Interfejs API obliczeń w wersji 2019-03-01, właściwość *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* musi mieć wartość *false* w definicji modelu zestawu skalowania. Powyższe właściwości umożliwiają uaktualnienia w maszynie wirtualnej, w których funkcja "Windows Update" stosuje poprawki systemu operacyjnego bez zastępowania dysku systemu operacyjnego. Po włączeniu automatycznych uaktualnień obrazu systemu operacyjnego w zestawie skalowania dodatkowa aktualizacja za pośrednictwem Windows Update nie jest wymagana.

### <a name="service-fabric-requirements"></a>Service Fabric wymagań

Jeśli używasz usługi Service Fabric, upewnij się, że zostały spełnione następujące warunki:
-   Service Fabric poziom [](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) trwałości to Silver lub Gold, a nie brąz (z wyjątkiem węzłów tylko do stanu, które obsługują automatyczne uaktualnienia systemu operacyjnego).
-   Rozszerzenie Service Fabric definicji modelu zestawu skalowania musi mieć typeHandlerVersion 1.1 lub więcej.
-   Poziom trwałości powinien być taki sam w klastrze Service Fabric i Service Fabric definicji modelu zestawu skalowania.
- Dodatkowa sonda kondycji ani użycie rozszerzenia kondycji aplikacji nie jest wymagane.

Upewnij się, że ustawienia trwałości nie są niezgodne w klastrze Service Fabric i rozszerzeniu Service Fabric, ponieważ niezgodność spowoduje błędy uaktualniania. Poziomy trwałości można modyfikować zgodnie z wytycznymi opisanymi [na tej stronie.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatyczne uaktualnianie obrazu systemu operacyjnego dla obrazów niestandardowych

Automatyczne uaktualnianie obrazu systemu operacyjnego jest obsługiwane w przypadku obrazów niestandardowych wdrożonych za [pośrednictwem Shared Image Gallery](../virtual-machines/shared-image-galleries.md). Inne obrazy niestandardowe nie są obsługiwane w przypadku automatycznych uaktualnień obrazów systemu operacyjnego.

### <a name="additional-requirements-for-custom-images"></a>Dodatkowe wymagania dotyczące obrazów niestandardowych
- Proces instalacji i konfiguracji automatycznego uaktualniania obrazu systemu operacyjnego jest taki sam dla wszystkich zestawów skalowania, jak o szczegółowe informacje w sekcji konfiguracji [na](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) tej stronie.
- Wystąpienia zestawów skalowania skonfigurowane do automatycznego uaktualniania obrazu systemu operacyjnego zostaną uaktualnione do najnowszej wersji obrazu usługi [](../virtual-machines/shared-image-galleries.md#replication) Shared Image Gallery, gdy nowa wersja obrazu zostanie opublikowana i zreplikowana do regionu tego zestawu skalowania. Jeśli nowy obraz nie jest replikowany do regionu, w którym wdrożono skalę, wystąpienia zestawu skalowania nie zostaną uaktualnione do najnowszej wersji. Regionalna replikacja obrazów umożliwia kontrolowanie we/wy nowego obrazu dla zestawów skalowania.
- Nowej wersji obrazu nie należy wykluczać z najnowszej wersji tego obrazu galerii. Wersje obrazów wykluczone z najnowszej wersji obrazu galerii nie są dostępne w zestawie skalowania dzięki automatycznemu uaktualnieniu obrazu systemu operacyjnego.

> [!NOTE]
>Wyzwolenie pierwszego uaktualnienia obrazu przez zestaw skalowania po pierwszym skonfigurowaniu zestawu skalowania do automatycznych uaktualnień systemu operacyjnego może potrwać do 3 godzin. Jest to opóźnienie raz na zestaw skalowania. Kolejne zmiany obrazów są wyzwalane w zestawie skalowania w ciągu 30–60 minut.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurowanie automatycznego uaktualniania obrazu systemu operacyjnego
Aby skonfigurować automatyczne uaktualnianie obrazu systemu operacyjnego, upewnij się, że właściwość *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* jest ustawiona na wartość *true* w definicji modelu zestawu skalowania.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób ustawienia automatycznych uaktualnień systemu operacyjnego w modelu zestawu skalowania:

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
Użyj polecenia cmdlet [Update-AzVmss,](/powershell/module/az.compute/update-azvmss) aby skonfigurować automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania. Poniższy przykład umożliwia skonfigurowanie automatycznych uaktualnień zestawu skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az vmss update,](/cli/azure/vmss#az-vmss-update) aby skonfigurować automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.47 lub wersji powyżej. Poniższy przykład umożliwia skonfigurowanie automatycznych uaktualnień zestawu skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Po skonfigurowaniu automatycznych uaktualnień obrazów systemu operacyjnego dla zestawu skalowania należy również przywrócić maszyny wirtualne zestawu skalowania do najnowszego modelu zestawu skalowania, jeśli zestaw skalowania używa zasad uaktualniania [ręcznego.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

## <a name="using-application-health-probes"></a>Korzystanie z sond kondycji aplikacji

Podczas uaktualniania systemu operacyjnego wystąpienia maszyn wirtualnych w zestawie skalowania są uaktualniane po jednej partii na raz. Uaktualnienie powinno być kontynuowane tylko wtedy, gdy aplikacja klienta jest w dobrej kondycji na uaktualnionych wystąpieniach maszyn wirtualnych. Zalecamy, aby aplikacja dostarczała sygnały kondycji do aparatu uaktualniania systemu operacyjnego zestawu skalowania. Domyślnie podczas uaktualniania systemu operacyjnego platforma uwzględnia stan zasilania maszyny wirtualnej i stan aprowizowania rozszerzenia, aby określić, czy wystąpienie maszyny wirtualnej jest w dobrej kondycji po uaktualnieniu. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny wirtualnej dysk systemu operacyjnego w wystąpieniu maszyny wirtualnej jest zastępowany nowym dyskiem opartym na najnowszej wersji obrazu. Po zakończeniu uaktualniania systemu operacyjnego skonfigurowane rozszerzenia są uruchamiane na tych maszyn wirtualnych. Aplikacja jest uznawana za w dobrej kondycji tylko wtedy, gdy wszystkie rozszerzenia w wystąpieniu zostały pomyślnie aprowowane.

Zestaw skalowania można opcjonalnie skonfigurować za pomocą sond kondycji aplikacji, aby zapewnić platformie dokładne informacje na temat bieżącego stanu aplikacji. Sondy kondycji aplikacji to niestandardowe Load Balancer, które są używane jako sygnał kondycji. Aplikacja uruchomiona w wystąpieniu maszyny wirtualnej zestawu skalowania może odpowiadać na zewnętrzne żądania HTTP lub TCP wskazujące, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat działania sond Load Balancer niestandardowych, zobacz Understand load balancer probes (Opis [sondy równoważenia obciążenia).](../load-balancer/load-balancer-custom-probe-overview.md) Sondy kondycji aplikacji nie są obsługiwane w przypadku Service Fabric skalowania. Zestawy skalowania inne Service Fabric wymagają Load Balancer kondycji aplikacji lub [rozszerzenia kondycji aplikacji.](virtual-machine-scale-sets-health-extension.md)

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, należy użyć [sond usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md) konfiguracji.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie niestandardowej sondy Load Balancer jako sondy kondycji aplikacji w zestawie skalowania
Najlepszym rozwiązaniem jest jawne utworzenie sondy równoważenia obciążenia dla kondycji zestawu skalowania. Można użyć tego samego punktu końcowego dla istniejącej sondy HTTP lub sondy TCP, ale sonda kondycji może wymagać innego zachowania niż tradycyjny sonda równoważenia obciążenia. Na przykład tradycyjna sonda usługi równoważenia obciążenia może zwrócić zła kondycję, jeśli obciążenie wystąpienia jest zbyt duże, ale nie byłoby to odpowiednie do określenia kondycji wystąpienia podczas automatycznego uaktualniania systemu operacyjnego. Skonfiguruj sondę tak, aby jej sondowanie było szybkie i wynosiło mniej niż dwie minuty.

Sonda usługi równoważenia obciążenia może być przywołyowana w pliku *networkProfile* zestawu skalowania i może być skojarzona z wewnętrznym lub publicznym równoważeniem obciążenia w następujący sposób:

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
> W przypadku korzystania z automatycznych uaktualnień systemu operacyjnego za Service Fabric nowy obraz systemu operacyjnego jest wycofywał aktualizację domeny przez domenę aktualizacji w celu zachowania wysokiej dostępności usług uruchomionych w programie Service Fabric. Aby korzystać z automatycznych uaktualnień systemu operacyjnego Service Fabric typ węzła klastra musi być skonfigurowany do używania warstwy trwałości Silver lub wyższej. W przypadku warstwy trwałości brązowa automatyczne uaktualnianie systemu operacyjnego jest obsługiwane tylko w przypadku węzłów bez stanowych. Aby uzyskać więcej informacji na temat właściwości trwałości Service Fabric klastrach, zobacz [tę dokumentację.](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Dbanie o aktualne poświadczenia
Jeśli zestaw skalowania używa poświadczeń w celu uzyskania dostępu do zasobów zewnętrznych, takich jak rozszerzenie maszyny wirtualnej skonfigurowane do używania tokenu SAS dla konta magazynu, upewnij się, że poświadczenia zostały zaktualizowane. Jeśli jakiekolwiek poświadczenia, w tym certyfikaty i tokeny, wygasły, uaktualnienie nie powiedzie się, a pierwsza partia maszyn wirtualnych zostanie pozostawiona w stanie awarii.

Zalecane kroki odzyskiwania maszyn wirtualnych i ponownego włączania automatycznego uaktualniania systemu operacyjnego w przypadku niepowodzenia uwierzytelniania zasobów to:

* Wygeneruj ponownie token (lub inne poświadczenia) przekazany do rozszerzeń.
* Upewnij się, że wszystkie poświadczenia używane z wewnątrz maszyny wirtualnej do rozmów z jednostkami zewnętrznymi są aktualne.
* Zaktualizuj rozszerzenia w modelu zestawu skalowania przy użyciu nowych tokenów.
* Wdrażanie zaktualizowanego zestawu skalowania, który spowoduje zaktualizowanie wszystkich wystąpień maszyn wirtualnych, w tym tych, które zakończyły się niepowodzeniem.

## <a name="using-application-health-extension"></a>Korzystanie z rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażane wewnątrz wystąpienia zestawu skalowania maszyn wirtualnych i raportuje kondycję maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Rozszerzenie można skonfigurować do sondowania w punkcie końcowym aplikacji i aktualizowania stanu aplikacji w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure w celu określenia, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie zgłasza kondycję z poziomu maszyny wirtualnej, rozszerzenie może być używane w sytuacjach, w których [](../load-balancer/load-balancer-custom-probe-overview.md)nie można używać sond zewnętrznych, takich jak sondy kondycji aplikacji (które korzystają z niestandardowych sond Azure Load Balancer).

Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji w zestawach skalowania zgodnie z informacjami w przykładach w [tym artykule.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Uzyskiwanie historii automatycznych uaktualnień obrazu systemu operacyjnego
Historię najnowszego uaktualnienia systemu operacyjnego wykonanego w zestawie skalowania można sprawdzić przy użyciu interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure 2.0 lub interfejsów API REST. Historię ostatnich pięciu prób uaktualnienia systemu operacyjnego można uzyskać w ciągu ostatnich dwóch miesięcy.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto interfejsu [API REST,](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) aby sprawdzić stan zestawu skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

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
Użyj polecenia [cmdlet Get-AzVmss,](/powershell/module/az.compute/get-azvmss) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie szczegółowo opisano sposób przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az vmss get-os-upgrade-history,](/cli/azure/vmss#az-vmss-get-os-upgrade-history) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.47 lub wersji powyżej. W poniższym przykładzie szczegółowo opisano sposób przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak uzyskać najnowszą wersję obrazu systemu operacyjnego platformy?

Dostępne wersje obrazów dla obsługiwanych przez automatyczne uaktualnianie systemu operacyjnego jednostki SKU można uzyskać, korzystając z poniższych przykładów:

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

## <a name="manually-trigger-os-image-upgrades"></a>Ręczne wyzwalanie uaktualnień obrazu systemu operacyjnego
Po włączeniu automatycznego uaktualniania obrazu systemu operacyjnego w zestawie skalowania nie trzeba ręcznie wyzwalać aktualizacji obrazów w zestawie skalowania. Orkiestrator uaktualniania systemu operacyjnego automatycznie zastosuje najnowszą dostępną wersję obrazu do wystąpień zestawu skalowania bez żadnej interwencji ręcznej.

W określonych przypadkach, gdy nie chcesz czekać na zastosowanie najnowszego obrazu przez orkiestratora, możesz ręcznie wyzwolić uaktualnienie obrazu systemu operacyjnego, korzystając z poniższych przykładów.

> [!NOTE]
> Ręczne wyzwalanie uaktualnień obrazu systemu operacyjnego nie zapewnia możliwości automatycznego wycofywania. Jeśli wystąpienie nie odzyska kondycji po operacji uaktualniania, nie można przywrócić poprzedniego dysku systemu operacyjnego.

### <a name="rest-api"></a>Interfejs API REST
Użyj wywołania [interfejsu](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API uruchamiania uaktualnienia systemu operacyjnego, aby rozpocząć uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyn wirtualnych do najnowszej dostępnej wersji systemu operacyjnego obrazu. Nie ma to wpływu na wystąpienia, na których jest już uruchomiona najnowsza dostępna wersja systemu operacyjnego. W poniższym przykładzie przedstawiono szczegółowo sposób uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Start-AzVmssRollingOSUpgrade,](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie przedstawiono szczegółowo sposób uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az vmss rolling-upgrade start,](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.47 lub wersji powyżej. W poniższym przykładzie przedstawiono szczegółowo sposób uruchamiania stopniowego uaktualniania systemu operacyjnego w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Za pomocą szablonów można wdrożyć zestaw skalowania z automatycznymi uaktualnieniami systemu operacyjnego dla obsługiwanych obrazów, takich jak [Ubuntu 16.04-LTS.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów użycia automatycznych uaktualnień systemu operacyjnego z zestawami skalowania, zapoznaj się [z repozytorium GitHub.](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)