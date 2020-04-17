---
title: Automatyczne uaktualnianie obrazu systemu operacyjnego za pomocą zestawów skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak automatycznie uaktualnić obraz systemu operacyjnego w wystąpieniach maszyn wirtualnych w zestawie skalowania
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mimckitt
ms.openlocfilehash: 70810b21def1672758683abd49f92b86776c9d7b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458985"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Skalowanie maszyny wirtualnej platformy Azure ustawia automatyczne uaktualnienia obrazów systemu operacyjnego

Włączenie automatycznych uaktualnień obrazu systemu operacyjnego w zestawie skalowania ułatwia zarządzanie aktualizacjami, bezpiecznie i automatycznie aktualizuje dysk systemu operacyjnego dla wszystkich wystąpień w zestawie skalowania.

Automatyczne uaktualnianie systemu operacyjnego ma następujące cechy:

- Po skonfigurowaniu najnowszy obraz systemu operacyjnego opublikowany przez wydawców obrazów jest automatycznie stosowany do zestawu skalowania bez interwencji użytkownika.
- Uaktualnia partie wystąpień w sposób stopniowy za każdym razem, gdy nowy obraz jest publikowany przez wydawcę.
- Integruje się z sondami kondycji aplikacji i [rozszerzeniem kondycji aplikacji.](virtual-machine-scale-sets-health-extension.md)
- Działa dla wszystkich rozmiarów maszyn wirtualnych i obrazów systemu Windows i Linux.
- W każdej chwili możesz zrezygnować z automatycznych uaktualnień (uaktualnienia systemu operacyjnego można również inicjować ręcznie).
- Dysk systemu operacyjnego maszyny wirtualnej zostanie zastąpiony nowym dyskiem systemu operacyjnego utworzonym z najnowszą wersją obrazu. Skonfigurowane rozszerzenia i niestandardowe skrypty danych są uruchamiane, podczas gdy utrwalone dyski danych są zachowywane.
- [Sekwencjonowanie rozszerzenia](virtual-machine-scale-sets-extension-sequencing.md) jest obsługiwane.
- Automatyczne uaktualnianie obrazu systemu operacyjnego można włączyć w zestawie skali o dowolnym rozmiarze.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak działa automatyczna aktualizacja obrazu systemu operacyjnego?

Uaktualnienie polega na zastąpieniu dysku systemu operacyjnego maszyny wirtualnej nowym dyskiem utworzonym przy użyciu najnowszej wersji obrazu. Wszystkie skonfigurowane rozszerzenia i niestandardowe skrypty danych są uruchamiane na dysku systemu operacyjnego, podczas gdy utrwalone dyski danych są zachowywane. Aby zminimalizować przestoje aplikacji, uaktualnienia odbywają się w partiach, z nie więcej niż 20% skalowania zestaw uaktualniania w dowolnym momencie. Można również zintegrować sondę kondycji aplikacji równoważenia obciążenia platformy Azure lub [rozszerzenie kondycji aplikacji.](virtual-machine-scale-sets-health-extension.md) Firma We zaleca włączenie pulsu aplikacji i sprawdź poprawność sukcesu uaktualnienia dla każdej partii w procesie uaktualniania.

Proces uaktualniania działa w następujący sposób:
1. Przed rozpoczęciem procesu uaktualniania orchestrator zapewni, że nie więcej niż 20% wystąpień w całym zestawie skalowania są w złej kondycji (z dowolnego powodu).
2. Programranowy uaktualniania identyfikuje partię wystąpień maszyn wirtualnych do uaktualnienia, z dowolną partią o maksymalnie 20% całkowitej liczby wystąpień, z zastrzeżeniem minimalnego rozmiaru partii jednej maszyny wirtualnej.
3. Dysk systemu operacyjnego wybranej partii wystąpień maszyn wirtualnych zostanie zastąpiony nowym dyskiem systemu operacyjnego utworzonym na podstawie najnowszego obrazu. Wszystkie określone rozszerzenia i konfiguracje w modelu zestawu skalowania są stosowane do uaktualnionego wystąpienia.
4. W przypadku zestawów skalowania ze skonfigurowanymi sondami kondycji aplikacji lub rozszerzeniem kondycji aplikacji uaktualnienie czeka do 5 minut, aż wystąpienie stanie się w dobrej kondycji, zanim przejdzie do uaktualnienia następnej partii. Jeśli wystąpienie nie odzyska jego kondycji w ciągu 5 minut po uaktualnieniu, domyślnie zostanie przywrócony poprzedni dysk systemu operacyjnego dla wystąpienia.
5. Koordynator uaktualnienia śledzi również procent wystąpień, które stają się w złej kondycji po uaktualnieniu. Uaktualnienie zostanie zatrzymane, jeśli więcej niż 20% uaktualnionych wystąpień stanie się w złej kondycji podczas procesu uaktualniania.
6. Powyższy proces jest kontynuowany, dopóki nie uaktualniono wszystkich wystąpień w zestawie skalowania.

Zestaw skalowania programator uaktualnienia systemu operacyjnego sprawdza ogólną kondycję zestawu skalowania przed uaktualnieniem każdej partii. Podczas uaktualniania partii mogą istnieć inne równoczesne zaplanowane lub nieplanowane działania konserwacyjne, które mogą mieć wpływ na kondycję wystąpień zestawu skalowania. W takich przypadkach, jeśli więcej niż 20% wystąpień zestawu skalowania stają się w złej kondycji, uaktualnienie zestawu skalowania zatrzymuje się na końcu bieżącej partii.

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
Tylko niektóre obrazy platformy systemu operacyjnego są obecnie obsługiwane. Niestandardowa obsługa obrazów jest dostępna [w wersji zapoznawczej](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) obrazów niestandardowych za pośrednictwem [Galerii obrazów udostępnionych](shared-image-galleries.md).

Następujące jednostki SKU platformy są obecnie obsługiwane (i więcej są dodawane okresowo):

| Wydawca               | Oferta systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Fala nieuczciwych (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | Centrum danych 2016    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Centrum danych z kontenerami |
| Microsoft Corporation   | WindowsServer | 2019-Centrum danych |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Centrum danych z kontenerami |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Wymagania dotyczące konfigurowania automatycznego uaktualniania obrazu systemu operacyjnego

- Właściwość *wersji* obrazu musi być ustawiona na *najnowszą*.
- Użyj sond kondycji aplikacji lub [rozszerzenia kondycji aplikacji](virtual-machine-scale-sets-health-extension.md) dla zestawów skalowania sieci szkieletowej innych niż usługi.
- Użyj interfejsu COMPUTE API w wersji 2018-10-01 lub nowszej.
- Upewnij się, że zasoby zewnętrzne określone w modelu zestawu skalowania są dostępne i aktualizowane. Przykłady obejmują identyfikator URI sygnatury dostępu Współdzielonego dla boottrapping ładunku we właściwości rozszerzenia maszyny Wirtualnej, ładunek na koncie magazynu, odwołanie do wpisów tajnych w modelu i więcej.
- Dla zestawów skalowania przy użyciu maszyn wirtualnych systemu Windows, począwszy od compute API w wersji 2019-03-01, właściwość *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* właściwość musi być *ustawiona* na false w definicji modelu zestawu skalowania. Powyższa właściwość umożliwia uaktualnienia w maszynie wirtualnej, gdzie "Windows Update" stosuje poprawki systemu operacyjnego bez wymiany dysku systemu operacyjnego. Po włączeniu automatycznych uaktualnień obrazów systemu operacyjnego w zestawie skalowania dodatkowa aktualizacja za pośrednictwem usługi "Windows Update" nie jest wymagana.

### <a name="service-fabric-requirements"></a>Wymagania dotyczące sieci szkieletowej usług

Jeśli korzystasz z sieci szkieletowej usług, upewnij się, że spełnione są następujące warunki:
-   Poziom [trwałości](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) tkaniny serwisowej to srebro lub złoto, a nie brąz.
-   Rozszerzenie sieci szkieletowej usług w definicji modelu zestawu skalowania musi mieć TypeHandlerVersion 1.1 lub powyżej.
-   Poziom trwałości powinien być taki sam w klastrze sieci szkieletowej usług i rozszerzenie sieci szkieletowej usług w definicji modelu zestawu skalowania.

Upewnij się, że ustawienia trwałości nie są niezgodne z klastrem sieci szkieletowej usług i rozszerzeniem sieci szkieletowej usług, ponieważ niezgodność spowoduje błędy uaktualnienia. Poziomy trwałości można zmienić zgodnie z wytycznymi przedstawionymi na [tej stronie.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Automatyczne uaktualnianie obrazu systemu operacyjnego dla obrazów niestandardowych (wersja zapoznawcza)

> [!IMPORTANT]
> Automatyczne uaktualnianie obrazu systemu operacyjnego dla obrazów niestandardowych jest obecnie w publicznej wersji zapoznawczej. Aby korzystać z funkcji publicznej wersji zapoznawczej opisanej poniżej, potrzebna jest procedura opt-in.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automatyczne uaktualnianie obrazu systemu operacyjnego jest dostępne w wersji zapoznawczej dla obrazów niestandardowych wdrożonych za pośrednictwem [Udostępnionej Galerii Obrazów](shared-image-galleries.md). Inne obrazy niestandardowe nie są obsługiwane w przypadku automatycznych uaktualnień obrazów systemu operacyjnego.

Włączenie funkcji w wersji zapoznawczej wymaga jednorazowej zgody na funkcję *AutomaticOSUpgradeWithGalleryImage* na subskrypcję, jak opisano poniżej.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób włączania wersji zapoznawczej subskrypcji:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić status rejestracji:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Po zarejestrowaniu funkcji dla subskrypcji, należy zakończyć proces opt-in, propagując zmiany do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Register-AzProviderFeature,](/powershell/module/az.resources/register-azproviderfeature) aby włączyć podgląd subskrypcji.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić status rejestracji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu funkcji dla subskrypcji, należy zakończyć proces opt-in, propagując zmiany do dostawcy zasobów obliczeniowych.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [rejestru funkcji az,](/cli/azure/feature#az-feature-register) aby włączyć podgląd subskrypcji.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić status rejestracji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Po zarejestrowaniu funkcji dla subskrypcji, należy zakończyć proces opt-in, propagując zmiany do dostawcy zasobów obliczeniowych.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Dodatkowe wymagania dotyczące obrazów niestandardowych
- Opisany powyżej proces opt-in musi zostać zakończony tylko raz na subskrypcję. Po zakończeniu opt-in automatyczne uaktualnienia systemu operacyjnego można włączyć dla dowolnej skali ustawionej w tej subskrypcji.
- Galeria obrazów udostępnionych może być w dowolnej subskrypcji i nie wymaga oddzielnej zgody. Tylko subskrypcja zestawu skalowania wymaga zgody funkcji.
- Proces konfiguracji automatycznego uaktualniania obrazu systemu operacyjnego jest taki sam dla wszystkich zestawów skalowania, jak opisano w [sekcji konfiguracji](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) tej strony.
- Wystąpienia zestawów skalowania skonfigurowane do automatycznego uaktualniania obrazów systemu operacyjnego zostaną uaktualnione do najnowszej wersji obrazu Shared Image Gallery, gdy nowa wersja obrazu zostanie opublikowana i [zreplikowana](shared-image-galleries.md#replication) do regionu tego zestawu skalowania. Jeśli nowy obraz nie jest replikowany do regionu, w którym jest wdrażana skala, wystąpienia zestawu skalowania nie zostaną uaktualnione do najnowszej wersji. Replikacja obrazów regionalnych umożliwia sterowanie wdrażaniem nowego obrazu dla zestawów skalowania.
- Nowa wersja obrazu nie powinna być wykluczona z najnowszej wersji dla tego obrazu galerii. Wersje obrazów wykluczone z najnowszej wersji obrazu galerii nie są wprowadzane do skalowania ustawionego przez automatyczne uaktualnianie obrazu systemu operacyjnego.

> [!NOTE]
>Może upłynąć do 3 godzin dla skalowania zestaw do wyzwolenia pierwszego wdrożenia uaktualnienia obrazu po skonfigurowaniu zestawu skalowania dla automatycznych uaktualnień systemu operacyjnego. Jest to opóźnienie jednorazowe na zestaw skalowania. Kolejne wdrożenia obrazów są wyzwalane w zestawie skalowania w ciągu 30 minut.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurowanie automatycznego uaktualniania obrazu systemu operacyjnego
Aby skonfigurować automatyczne uaktualnianie obrazu systemu operacyjnego, upewnij się, że *właściwość automaticOSUpgradePolicy.enableAutomaticOSUpgrade* jest ustawiona na *true* w definicji modelu zestawu skalowania.

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
Użyj polecenia cmdlet [Update-AzVmss,](/powershell/module/az.compute/update-azvmss) aby skonfigurować automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie skonfigurowano automatyczne uaktualnienia dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [aktualizacji az vmss,](/cli/azure/vmss#az-vmss-update) aby skonfigurować automatyczne uaktualnienia obrazów systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub wyższej. W poniższym przykładzie skonfigurowano automatyczne uaktualnienia dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Korzystanie z sond kondycji aplikacji

Podczas uaktualniania systemu operacyjnego wystąpienia maszyn wirtualnych w zestawie skalowania są uaktualniane po jednej partii naraz. Uaktualnienie należy kontynuować tylko wtedy, gdy aplikacja klienta jest w dobrej kondycji w przypadku uaktualnionych wystąpień maszyny Wirtualnej. Zaleca się, że aplikacja dostarcza sygnały kondycji do aparatu uaktualnienia zestawu skalowania systemu operacyjnego. Domyślnie podczas uaktualnień systemu operacyjnego platforma uwzględnia stan zasilania maszyny Wirtualnej i stan inicjowania obsługi administracyjnej rozszerzeń, aby ustalić, czy wystąpienie maszyny Wirtualnej jest w dobrej kondycji po uaktualnieniu. Podczas uaktualniania systemu operacyjnego wystąpienia maszyny Wirtualnej dysk systemu operacyjnego w wystąpieniu maszyny Wirtualnej jest zastępowany nowym dyskiem opartym na najnowszej wersji obrazu. Po zakończeniu uaktualnienia systemu operacyjnego skonfigurowane rozszerzenia są uruchamiane na tych maszynach wirtualnych. Aplikacja jest uznawana za w dobrej kondycji tylko wtedy, gdy wszystkie rozszerzenia w wystąpieniu są pomyślnie aprowizować.

Zestaw skalowania można opcjonalnie skonfigurować za pomocą sond kondycji aplikacji, aby zapewnić platformie dokładne informacje o bieżącym stanie aplikacji. Sondy kondycji aplikacji są niestandardowe sondy modułu równoważenia obciążenia, które są używane jako sygnał kondycji. Aplikacja uruchomiona na skalowanie zestaw wystąpienia maszyny Wirtualnej może odpowiadać na zewnętrzne żądania HTTP lub TCP wskazując, czy jest w dobrej kondycji. Aby uzyskać więcej informacji na temat działania sond modułu niestandardowego równoważenia obciążenia, zobacz [Opis sondy modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). Sondy kondycji aplikacji nie są obsługiwane dla zestawów skalowania sieci szkieletowej usług. Zestawy skalowania sieci szkieletowej nieobsługiwają się sondami kondycji aplikacji równoważenia obciążenia lub [rozszerzeniem kondycji aplikacji.](virtual-machine-scale-sets-health-extension.md)

Jeśli zestaw skalowania jest skonfigurowany do używania wielu grup umieszczania, należy użyć sond przy użyciu [standardowego modułu równoważenia obciążenia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurowanie niestandardowej sondy równoważenia obciążenia jako sondy kondycji aplikacji w zestawie skalowania
Najlepszym rozwiązaniem, aby utworzyć sondę modułu równoważenia obciążenia jawnie dla kondycji zestawu skalowania. Można użyć tego samego punktu końcowego dla istniejącej sondy HTTP lub sondy TCP, ale sonda kondycji może wymagać innego zachowania niż tradycyjna sonda modułu równoważenia obciążenia. Na przykład tradycyjna sonda modułu równoważenia obciążenia może zwrócić złą złą kondycję, jeśli obciążenie wystąpienia jest zbyt wysokie, ale nie byłoby to odpowiednie do określania kondycji wystąpienia podczas automatycznego uaktualniania systemu operacyjnego. Skonfiguruj sondę, aby mieć wysoką szybkość sondowania mniejszą niż dwie minuty.

Sonda modułu równoważenia obciążenia może być odwoływana w *sieciProfil* zestawu skalowania i może być skojarzona z wewnętrznym lub publicznym modułem równoważenia obciążenia w następujący sposób:

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
> Podczas korzystania z automatycznych uaktualnień systemu operacyjnego z sieci szkieletowej usług, nowy obraz systemu operacyjnego jest wdrażany aktualizuj domeny przez domenę aktualizacji, aby utrzymać wysoką dostępność usług uruchomionych w sieci szkieletowej usług. Aby korzystać z automatycznych uaktualnień systemu operacyjnego w sieci szkieletowej usług, klaster musi być skonfigurowany do używania warstwy Silver Durability lub nowszej. Aby uzyskać więcej informacji na temat charakterystyki trwałości klastrów sieci szkieletowej usług, zobacz [tę dokumentację](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Dbaj o aktualną datę poświadczeń
Jeśli zestaw skalowania używa żadnych poświadczeń, aby uzyskać dostęp do zasobów zewnętrznych, takich jak rozszerzenie maszyny Wirtualnej skonfigurowane do używania tokenu sygnatury dostępu Współdzielonego dla konta magazynu, upewnij się, że poświadczenia są aktualizowane. Jeśli wszelkie poświadczenia, w tym certyfikaty i tokeny, wygasły, uaktualnienie zakończy się niepowodzeniem, a pierwsza partia maszyn wirtualnych pozostanie w stanie awarii.

Zalecane kroki, aby odzyskać maszyny wirtualne i ponownie włączyć automatyczne uaktualnianie systemu operacyjnego, jeśli występuje błąd uwierzytelniania zasobów są:

* Ponowne generowanie tokenu (lub innych poświadczeń) przekazanych do rozszerzeń.
* Upewnij się, że wszystkie poświadczenia używane z wewnątrz maszyny Wirtualnej do rozmowy z zewnętrznymi jednostkami jest aktualna.
* Aktualizuj rozszerzenia w modelu zestawu skalowania za pomocą nowych tokenów.
* Wdrożenie zaktualizowanego zestawu skalowania, który zaktualizuje wszystkie wystąpienia maszyn wirtualnych, w tym nieudane.

## <a name="using-application-health-extension"></a>Korzystanie z rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażany wewnątrz wystąpienia zestawu skalowania maszyny wirtualnej i raporty na temat kondycji maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i zaktualizować stan aplikacji w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure, aby ustalić, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie raportuje kondycję z poziomu maszyny Wirtualnej, rozszerzenie może być używane w sytuacjach, gdy nie można używać zewnętrznych sond, takich jak sondy kondycji aplikacji (które korzystają z niestandardowych [sond](../load-balancer/load-balancer-custom-probe-overview.md)równoważenia obciążenia platformy Azure).

Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji do zestawów skalowania, jak opisano w przykładach w [tym artykule.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Poznaj historię automatycznych uaktualnień obrazów systemu operacyjnego
Możesz sprawdzić historię najnowszego uaktualnienia systemu operacyjnego wykonywanego w zestawie skalowania za pomocą programu Azure PowerShell, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure 2.0 lub interfejsów API REST. Możesz uzyskać historię ostatnich pięciu prób uaktualnienia systemu operacyjnego w ciągu ostatnich dwóch miesięcy.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie użyto [interfejsu API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) do sprawdzenia stanu zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

Wywołanie GET zwraca właściwości podobne do następującego przykładowego wyjścia:

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
Użyj polecenia cmdlet [Get-AzVmss,](/powershell/module/az.compute/get-azvmss) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie opisano sposób przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [historii uaktualnienia az vmss,](/cli/azure/vmss#az-vmss-get-os-upgrade-history) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub wyższej. W poniższym przykładzie opisano sposób przeglądania stanu uaktualnienia systemu operacyjnego dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak uzyskać najnowszą wersję obrazu systemu operacyjnego platformy?

Dostępne wersje obrazów dla automatycznego uaktualniania systemu operacyjnego obsługiwane jednostki SKU, korzystając z poniższych przykładów:

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
Po włączeniu automatycznego uaktualniania obrazu systemu operacyjnego w zestawie skalowania nie trzeba ręcznie wyzwalać aktualizacji obrazu w zestawie skalowania. Koordynator uaktualnienia systemu operacyjnego automatycznie zastosuje najnowszą dostępną wersję obrazu do wystąpień zestawu skalowania bez ręcznej interwencji.

W szczególnych przypadkach, w których nie chcesz czekać na orchestrator zastosować najnowszy obraz, można wyzwolić uaktualnienie obrazu systemu operacyjnego ręcznie przy użyciu poniższych przykładów.

> [!NOTE]
> Ręczny wyzwalacz uaktualnień obrazu systemu operacyjnego nie zapewnia możliwości automatycznego wycofywania. Jeśli wystąpienie nie odzyska kondycji po operacji uaktualnienia, nie można przywrócić jego poprzedniego dysku systemu operacyjnego.

### <a name="rest-api"></a>Interfejs API REST
Użyj [wywołania interfejsu API uaktualnienia systemu operacyjnego Start,](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) aby rozpocząć uaktualnienie stopniowe, aby przenieść wszystkie wystąpienia zestawu skalowania maszyny wirtualnej do najnowszej dostępnej wersji systemu operacyjnego obrazu. Nie ma to wpływu na wystąpienia, które są już uruchomione najnowszą dostępną wersją systemu operacyjnego. W poniższym przykładzie opisano, jak można rozpocząć uaktualnienie systemu operacyjnego na zestaw skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Start-AzVmssRollingOSUpgrade,](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. W poniższym przykładzie opisano, jak można rozpocząć uaktualnienie systemu operacyjnego na zestaw skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az vmss rolling-upgrade start,](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) aby sprawdzić historię uaktualniania systemu operacyjnego dla zestawu skalowania. Użyj interfejsu wiersza polecenia platformy Azure 2.0.47 lub wyższej. W poniższym przykładzie opisano, jak można rozpocząć uaktualnienie systemu operacyjnego na zestaw skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Za pomocą szablonów można wdrożyć zestaw skalowania z automatycznymi uaktualnieniami systemu operacyjnego dla obsługiwanych obrazów, takich jak [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej przykładów na temat używania automatycznych uaktualnień systemu operacyjnego z zestawami skalowania, przejrzyj [repozytorium GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
