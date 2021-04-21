---
title: Tworzenie zestawu skalowania, który korzysta z usługi Azure Spot Virtual Machines
description: Dowiedz się, jak tworzyć zestawy skalowania maszyn wirtualnych platformy Azure, które korzystają Virtual Machines Azure Spot, aby zaoszczędzić na kosztach.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762951"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Usługa Azure Spot Virtual Machines dla zestawów skalowania maszyn wirtualnych 

Korzystanie z usługi Azure Spot Virtual Machines w zestawach skalowania umożliwia wykorzystanie nieużywanej pojemności przy znaczących oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje ponownie pojemności, infrastruktura platformy Azure eksmisja wystąpień maszyny wirtualnej usługi Azure Spot. W związku z tym wystąpienia maszyny wirtualnej usługi Azure Spot są doskonałe dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskich/testowe, duże obciążenia obliczeniowe i nie tylko.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, porze dnia i innych. Podczas wdrażania wystąpień maszyn wirtualnych usługi Azure Spot w zestawach skalowania platforma Azure przydzieli wystąpienie tylko wtedy, gdy jest dostępna pojemność, ale nie ma umowy SLA dla tych wystąpień. Zestaw skalowania maszyn wirtualnych usługi Azure Spot jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności.


## <a name="pricing"></a>Ceny

Cennik wystąpień maszyn wirtualnych usługi Azure Spot jest zmienny w zależności od regionu i wersji SKU. Aby uzyskać więcej informacji, zobacz cennik systemów [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) i [Windows.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


W przypadku zmiennych cen można ustawić maksymalną cenę w dolarach amerykańskich (USD) z dokładnością do pięciu miejsc dziesiętnych. Na przykład wartość byłaby `0.98765` maksymalną ceną w wysokości 0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę na , wystąpienie nie zostanie `-1` eksmisji na podstawie ceny. Cena wystąpienia to bieżąca cena maszyny wirtualnej usługi Azure Spot lub cena wystąpienia standardowego, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i limit przydziału.


## <a name="limitations"></a>Ograniczenia

Następujące rozmiary nie są obsługiwane w przypadku usługi Azure Spot Virtual Machines:
 - Seria B
 - Wersje promocyjny o dowolnym rozmiarze (np. rozmiary Dv2, NV, NC, H)

Maszynę wirtualną usługi Azure Spot można wdrożyć w dowolnym regionie, z Microsoft Azure Chiny 21Vianet.

<a name="channel"></a>

Obecnie obsługiwane [są następujące](https://azure.microsoft.com/support/legal/offer-details/) typy ofert:

-   Enterprise Agreement
-   Kod oferty z płatnością zgodnie z ty (003P)
-   Sponsorowane (0036P i 0136P)
- W przypadku dostawcy usług w chmurze (CSP) zobacz Partner Center [lub](/partner-center/azure-plan-get-started) skontaktuj się bezpośrednio z partnerem.

## <a name="eviction-policy"></a>Zasady eksmisji

Podczas tworzenia zestawu skalowania przy użyciu usługi Azure Spot Virtual Machines można ustawić zasady eksmisji na Cofanie alokacji *(ustawienie* domyślne) lub *Usuń*. 

Zasady *Cofnięcia* alokacji przesuną eksmitowane wystąpienia do stanu zatrzymanego cofnięcia alokacji, co pozwala na ponowne wduowanie eksmitowanych wystąpień. Nie ma jednak gwarancji, że alokacja powiedzie się. Cofane przydziały maszyn wirtualnych będą wliczane do limitu przydziału wystąpienia zestawu skalowania i zostaną naliczone opłaty za dyski bazowe. 

Jeśli chcesz, aby wystąpienia były usuwane po eksmisji, możesz ustawić zasady eksmisji w *taki sposób, aby je usuwały.* Po usunięciu zasad eksmisji można tworzyć nowe maszyny wirtualne, zwiększając właściwość liczby wystąpień zestawu skalowania. Eksmitowane maszyny wirtualne są usuwane wraz z ich dyskami bazowymi, w związku z tym nie są naliczane opłaty za magazyn. Można również użyć funkcji automatycznego skalowania zestawów skalowania, aby automatycznie spróbować i skompensować eksmitowane maszyny wirtualne, jednak nie ma gwarancji, że alokacja powiedzie się. Zaleca się używanie funkcji automatycznego skalowania w zestawach skalowania maszyn wirtualnych usługi Azure Spot tylko w przypadku ustawienia zasad eksmisji do usunięcia w celu uniknięcia kosztów dysków i osiągnięcia limitów przydziału. 

Użytkownicy mogą wyrazić zgodę na otrzymywanie powiadomień w ramach maszyny wirtualnej za [pośrednictwem usługi Azure Scheduled Events.](../virtual-machines/linux/scheduled-events.md) Spowoduje to powiadomienie, jeśli maszyny wirtualne są eksmisji i będziesz mieć 30 sekund na zakończenie wszystkich zadań i wykonywanie zadań zamykania przed eksmisją. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Wypróbuj & przywracania (wersja zapoznawcza)

Ta nowa funkcja na poziomie platformy będzie używać AI, aby automatycznie próbować przywrócić eksmisję wystąpień maszyny wirtualnej usługi Azure Spot wewnątrz zestawu skalowania w celu zachowania liczby wystąpień docelowych. 

> [!IMPORTANT]
> Spróbuj & przywracania jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wypróbuj & przywracania:
- Próby przywrócenia usługi Azure Spot Virtual Machines eksmisji z powodu pojemności.
- Oczekuje się, że przywrócona usługa Azure Spot Virtual Machines będzie działać przez dłuższy czas z niższym prawdopodobieństwem eksmisji wyzwalanej przez pojemność.
- Wydłuża cykl życia maszyny wirtualnej usługi Azure Spot, dzięki czemu obciążenia są uruchamiane przez dłuższy czas.
- Ułatwia Virtual Machine Scale Sets celu utrzymania liczby docelowej dla usługi Azure Spot Virtual Machines, podobnie jak w przypadku maszyn wirtualnych z płatnością zgodnie z Virtual Machines.

Spróbuj & przywracanie jest wyłączone w zestawach skalowania, które używają [skalowania automatycznego.](virtual-machine-scale-sets-autoscale-overview.md) Liczba maszyn wirtualnych w zestawie skalowania jest kierowana przez reguły skalowania automatycznego.

### <a name="register-for-try--restore"></a>Rejestrowanie w celu wypróbowania & przywracania

Aby można było użyć funkcji wypróbuj & przywracania, musisz zarejestrować subskrypcję w celu korzystania z wersji zapoznawczej. Rejestracja może potrwać kilka minut. Aby ukończyć rejestrację funkcji, możesz użyć interfejsu wiersza polecenia platformy Azure lub programu PowerShell.


**Korzystanie z interfejsu wiersza polecenia**

Użyj [az feature register,](/cli/azure/feature#az_feature_register) aby włączyć podgląd subskrypcji. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Korzystanie z programu PowerShell** 

Użyj polecenia cmdlet [Register-AzProviderFeature,](/powershell/module/az.resources/register-azproviderfeature) aby włączyć podgląd subskrypcji. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Grupy umieszczania

Grupa umieszczania to konstrukcja podobna do zestawu dostępności platformy Azure z własnymi domenami błędów i domenami uaktualnienia. Domyślnie zestaw skalowania składa się z pojedynczej grupy umieszczania zawierającej maksymalnie 100 maszyn wirtualnych. Jeśli właściwość zestawu skalowania o nazwie ma wartość false, zestaw skalowania może składać się z wielu grup umieszczania i może mieć zakres od 0 do `singlePlacementGroup` 1000 maszyn wirtualnych.  

> [!IMPORTANT]
> Jeśli nie używasz infiniband z HPC, zdecydowanie zaleca się ustawienie właściwości zestawu skalowania na wartość false, aby umożliwić wielu grupom umieszczania lepsze skalowanie w regionie lub `singlePlacementGroup` strefie.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Wdrażanie usługi Azure Spot Virtual Machines zestawach skalowania

Aby wdrożyć usługę Azure Spot Virtual Machines zestawach skalowania, możesz ustawić nową flagę *Priorytet* na *spot*. Wszystkie maszyny wirtualne w zestawie skalowania zostaną ustawione na spot. Aby utworzyć zestaw skalowania za pomocą usługi Azure Spot Virtual Machines, użyj jednej z następujących metod:
- [Witryna Azure Portal](#portal)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Szablony usługi Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

Proces tworzenia zestawu skalowania, który używa usługi Azure Spot Virtual Machines, jest taki sam jak w artykule [z wprowadzeniem.](quick-create-portal.md) Podczas wdrażania zestawu skalowania możesz ustawić flagę spot i zasady eksmisji: Tworzenie zestawu skalowania za pomocą usługi ![ Azure Spot Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Proces tworzenia zestawu skalowania za pomocą usługi Azure Spot Virtual Machines jest taki sam jak w artykule [z wprowadzeniem.](quick-create-cli.md) Po prostu dodaj pozycję "--Priority Spot" i dodaj pozycję `--max-price` . W tym przykładzie używamy dla , aby wystąpienie nie było eksmisji `-1` `--max-price` na podstawie ceny.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Proces tworzenia zestawu skalowania za pomocą usługi Azure Spot Virtual Machines jest taki sam jak w artykule [z wprowadzeniem.](quick-create-powershell.md)
Wystarczy dodać polecenie "-Priority Spot" i podać polecenie w `-max-price` pliku [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Proces tworzenia zestawu skalowania, który korzysta z usługi Azure Spot Virtual Machines, jest taki sam jak w artykule z wprowadzeniem dla systemu [Linux](quick-create-template-linux.md) lub [Windows.](quick-create-template-windows.md) 

W przypadku wdrożeń szablonu maszyny wirtualnej usługi Azure Spot użyj `"apiVersion": "2019-03-01"` lub nowszej wersji. 

Dodaj `priority` właściwości , `evictionPolicy` i do sekcji oraz właściwość do sekcji w `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` szablonie:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Aby usunąć wystąpienie po jego eksmisji, zmień `evictionPolicy` parametr na `Delete` .


## <a name="simulate-an-eviction"></a>Symulowanie eksmisji

Możesz [zasymulować eksmisję](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej usługi Azure Spot, aby sprawdzić, jak dobrze aplikacja będzie reagować na nagłe eksmisje. 

Zastąp następujące informacje swoimi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` oznacza, że symulowane eksmisja powiodła się. 

## <a name="faq"></a>Często zadawane pytania

**Pytanie:** Czy po utworzeniu wystąpienie maszyny wirtualnej usługi Azure Spot jest takie samo jak wystąpienie standardowe?

**A:** Tak, z wyjątkiem tego, że nie ma umowy SLA dla usługi Azure Spot Virtual Machines i można je eksmituje w dowolnym momencie.


**Pytanie:** Co zrobić po eksmisji, ale nadal potrzebujesz pojemności?

**A:** Zalecamy używanie standardowych maszyn wirtualnych zamiast usługi Azure Spot Virtual Machines, jeśli potrzebujesz pojemności od razu.


**Pytanie:** Jak jest zarządzany limit przydziału dla maszyny wirtualnej usługi Azure Spot?

**A:** Wystąpienia maszyn wirtualnych usługi Azure Spot i wystąpienia standardowe będą mieć oddzielne pule przydziałów. Limit przydziału maszyny wirtualnej usługi Azure Spot będzie współdzielony między maszynami wirtualnymi i wystąpieniami zestawu skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).


**Pytanie:** Czy mogę zażądać dodatkowego limitu przydziału dla maszyny wirtualnej usługi Azure Spot?

**A:** Tak, będzie można przesłać żądanie zwiększenia limitu przydziału dla usługi Azure Spot Virtual Machines za pośrednictwem [standardowego procesu żądania limitu przydziału.](../azure-portal/supportability/per-vm-quota-requests.md)


**Pytanie:** Czy mogę przekonwertować istniejące zestawy skalowania na zestawy skalowania maszyn wirtualnych usługi Azure Spot?

**A:** Nie, ustawienie `Spot` flagi jest obsługiwane tylko podczas tworzenia.


**Pytanie:** Jeśli korzystam z zestawów skalowania o niskim `low` priorytecie, czy zamiast tego muszę zacząć `Spot` z nich korzystać?

**A:** Na razie zarówno , `low` jak `Spot` i będą działać, ale należy zacząć korzystać z funkcji `Spot` .


**Pytanie:** Czy można utworzyć zestaw skalowania przy użyciu zwykłych maszyn wirtualnych i usługi Azure Spot Virtual Machines?

**A:** Nie, zestaw skalowania nie może obsługiwać więcej niż jednego typu priorytetu.


**Pytanie:**  Czy mogę używać skalowania automatycznego z zestawami skalowania maszyn wirtualnych usługi Azure Spot?

**A:** Tak, można ustawić reguły skalowania automatycznego w zestawie skalowania maszyn wirtualnych usługi Azure Spot. Jeśli maszyny wirtualne są eksmisje, autoskalowanie może spróbować utworzyć nową usługę Azure Spot Virtual Machines. Pamiętaj jednak, że ta pojemność nie jest gwarantowana. 


**Pytanie:**  Czy autoskalowanie działa z zasadami eksmisji (cofanie alokacji i usuwanie)?

**A:** Tak, jednak zaleca się ustawienie zasad eksmisji do usunięcia podczas korzystania ze skalowania automatycznego. Wynika to z tego, że cofane alokacje wystąpień są wliczane do liczby pojemności w zestawie skalowania. W przypadku korzystania ze skalowania automatycznego prawdopodobnie szybko dotrzesz do liczby wystąpień docelowych ze względu na cofane, eksmisje wystąpień. Ponadto na operacje skalowania mogą mieć wpływ eksmisje punktowe. Na przykład wystąpienia zestawu skalowania maszyn wirtualnych mogą być poniżej ustawionej minimalnej liczby z powodu wielu eksmisji punktowych podczas operacji skalowania. 


**Pytanie:** Gdzie mogę publikować pytania?

**A:** Pytanie możesz opublikować i otagować za `azure-spot` pomocą&[A.](/answers/topics/azure-spot.html) 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje [o cenach,](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) zapoznaj się ze stroną cennika zestawu skalowania maszyn wirtualnych.
