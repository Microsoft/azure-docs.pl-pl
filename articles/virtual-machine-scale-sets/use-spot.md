---
title: Tworzenie zestawu skalowania korzystającego z usługi Azure Spot Virtual Machines
description: Dowiedz się, jak tworzyć zestawy skalowania maszyn wirtualnych platformy Azure korzystające z Virtual Machines platformy Azure w celu oszczędzania kosztów.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: cynthn
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 265f78970f17fe7321db8786c2fb8dd2304bb578
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558678"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Virtual Machines platformy Azure dla zestawów skalowania maszyn wirtualnych 

Korzystanie z usługi Azure Spot Virtual Machines w ramach zestawów skalowania pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy wystąpienia maszyn wirtualnych platformy Azure. W związku z tym wystąpienia maszyn wirtualnych platformy Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, pory dnia i innych. Podczas wdrażania wystąpień maszyn wirtualnych platformy Azure w zestawach skalowania platforma Azure przydzieli to wystąpienie tylko wtedy, gdy jest dostępna pojemność, ale dla tych wystąpień nie ma umowy SLA. Zestaw skalowania maszyn wirtualnych platformy Azure jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności.


## <a name="pricing"></a>Cennik

Cennik wystąpień maszyn wirtualnych platformy Azure to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik dla systemów [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


W przypadku zmiennych cenowych istnieje możliwość ustawienia maksymalnej ceny w dolarach amerykańskich (USD) przy użyciu maksymalnie pięciu miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , wystąpienie nie zostanie wykluczone w oparciu o cenę. Cena dla tego wystąpienia będzie aktualna cena za maszynę wirtualną platformy Azure lub cena za wystąpienie standardowe, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.


## <a name="limitations"></a>Ograniczenia

Następujące rozmiary nie są obsługiwane w przypadku Virtual Machines na platformie Azure:
 - Seria B
 - Promocja wersji dowolnego rozmiaru (na przykład Dv2, NV, w obszarze rozmiary promocji)

Maszynę wirtualną platformy Azure można wdrożyć w dowolnym regionie, z wyjątkiem Microsoft Azure Chinach 21Vianet.

<a name="channel"></a>

Następujące [typy ofert](https://azure.microsoft.com/support/legal/offer-details/) są obecnie obsługiwane:

-   Enterprise Agreement
-   Kod oferty z opcją płatność zgodnie z rzeczywistym użyciem 003P
-   Sponsorowan
- W przypadku dostawcy usług w chmurze (CSP) skontaktuj się z partnerem

## <a name="eviction-policy"></a>Zasady eksmisji

Podczas tworzenia zestawów skalowania maszyn wirtualnych platformy Azure Możesz ustawić zasady wykluczania na *Cofnij przydział* (domyślnie) lub *Usuń*. 

Zasada cofania *przydziału* powoduje przeniesienie wykluczonych wystąpień do stanu zatrzymania bez alokacji, co pozwala na ponowne wdrożenie wykluczonych wystąpień. Nie ma jednak gwarancji, że alokacja powiedzie się. Cofnięte alokacje maszyn wirtualnych będą wliczane do limitu przydziału wystąpienia zestawu skalowania i będą naliczane opłaty za dyski bazowe. 

Jeśli chcesz, aby wystąpienia w zestawie skalowania maszyn wirtualnych na platformie Azure były usuwane po wykluczeniu, możesz ustawić zasady wykluczania do *usunięcia*. Za pomocą zasad wykluczania ustawionych do usunięcia można tworzyć nowe maszyny wirtualne, zwiększając Właściwość liczby wystąpień zestawu skalowania. Wykluczone maszyny wirtualne zostaną usunięte wraz z ich podstawowymi dyskami, w związku z czym nie zostanie naliczona opłata za magazyn. Można również użyć funkcji automatycznego skalowania zestawów skalowania, aby automatycznie próbować i wyrównać wykluczone maszyny wirtualne, ale nie ma gwarancji, że alokacja zakończy się pomyślnie. Zaleca się używanie funkcji automatycznego skalowania w zestawach skalowania maszyn wirtualnych platformy Azure po ustawieniu zasad wykluczania do usunięcia, aby uniknąć kosztów dysków i ograniczyć limity przydziału. 

Użytkownicy mogą zrezygnować z otrzymywania powiadomień w ramach maszyny wirtualnej za pomocą [usługi Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Spowoduje to powiadomienie użytkownika, jeśli maszyny wirtualne zostaną wykluczone, a użytkownik będzie miał 30 sekund na ukończenie zadań i wykonanie zadań zamknięcia przed wykluczeniem. 

## <a name="placement-groups"></a>Grupy umieszczania
Grupa umieszczania to konstrukcja podobna do zestawu dostępności platformy Azure z własnymi domenami błędów i domenami uaktualnienia. Domyślnie zestaw skalowania składa się z pojedynczej grupy umieszczania zawierającej maksymalnie 100 maszyn wirtualnych. Jeśli wywołana Właściwość zestawu skalowania `singlePlacementGroup` ma *wartość FAŁSZ*, zestaw skalowania może składać się z wielu grup umieszczania i ma zakres 0 – 1000 maszyn wirtualnych. 

> [!IMPORTANT]
> Jeśli nie korzystasz z funkcji InfiniBand z HPC, zdecydowanie zalecamy ustawienie dla właściwości zestawu skalowania `singlePlacementGroup` *wartości false* , aby umożliwić lepsze skalowanie w obrębie regionu lub strefy. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Wdrażanie Virtual Machines usługi Azure spot w zestawach skalowania

Aby wdrożyć Virtual Machines w miejscu na platformie Azure w zestawach skalowania, można *ustawić flagę* Nowa *Flaga.* Wszystkie maszyny wirtualne w zestawie skalowania zostaną ustawione jako aktywne. Aby utworzyć zestaw skalowania za pomocą Virtual Machines platformy Azure, użyj jednej z następujących metod:
- [Witryna Azure Portal](#portal)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Szablony usługi Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

Proces tworzenia zestawu skalowania korzystającego z usługi Azure Spot Virtual Machines jest taki sam jak szczegółowy w [artykule wprowadzenie](quick-create-portal.md). Podczas wdrażania zestawu skalowania można ustawić flagę punktu aktywnego i zasady wykluczania: ![ Utwórz zestaw skalowania za pomocą Virtual Machines platformy Azure](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Proces tworzenia zestawu skalowania za pomocą Virtual Machines platformy Azure jest taki sam jak szczegółowy w [artykule wprowadzenie](quick-create-cli.md). Po prostu Dodaj element "--Priority" i Dodaj `--max-price` . W tym przykładzie używamy do tego, `-1` `--max-price` że wystąpienie nie zostanie wykluczone w oparciu o cenę.

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

Proces tworzenia zestawu skalowania za pomocą Virtual Machines platformy Azure jest taki sam jak szczegółowy w [artykule wprowadzenie](quick-create-powershell.md).
Po prostu Dodaj opcję "-Priority" i podaj `-max-price` do [nowego AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Proces tworzenia zestawu skalowania korzystającego z usługi Azure Spot Virtual Machines jest taki sam jak szczegółowy w artykule wprowadzenie do systemu [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). 

W przypadku wdrożeń szablonów maszyn wirtualnych platformy Azure, użyj `"apiVersion": "2019-03-01"` lub nowszej. 

Dodaj `priority` `evictionPolicy` `billingProfile` właściwości i do sekcji `"virtualMachineProfile":` oraz `"singlePlacementGroup": false,` Właściwość do `"Microsoft.Compute/virtualMachineScaleSets"` sekcji w szablonie:

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

Aby usunąć wystąpienie po jego wykluczenia, należy zmienić `evictionPolicy` parametr na `Delete` .

## <a name="faq"></a>Często zadawane pytania

**P:** Czy po utworzeniu, czy wystąpienie maszyny wirtualnej platformy Azure jest takie samo jak w przypadku wystąpienia standardowego?

Odp **.:** Tak, z tą różnicą, że nie ma umowy SLA dla usługi Azure Virtual Machines i można je wykluczyć w dowolnym momencie.


**P:** Co należy zrobić po wykluczeniu, ale nadal potrzebujesz pojemności?

Odp **.:** Zalecamy używanie standardowych maszyn wirtualnych zamiast Virtual Machines platformy Azure, jeśli potrzebujesz pojemności od razu.


**P:** Jak jest zarządzany przydział dla maszyny wirtualnej platformy Azure w miejscu?

Odp **.:** Wystąpienia maszyn wirtualnych platformy Azure i wystąpienia standardowe będą mieć oddzielne pule przydziałów. Limit przydziału maszyn wirtualnych platformy Azure zostanie udostępniony między maszynami wirtualnymi i wystąpieniami zestawów skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).


**P:** Czy mogę zażądać dodatkowego przydziału dla maszyny wirtualnej platformy Azure w miejscu?

Odp **.:** Tak, będzie można przesłać żądanie, aby zwiększyć limit przydziału dla Virtual Machines w miejscu na platformie Azure przez [standardowy proces żądania limitu przydziału](../azure-portal/supportability/per-vm-quota-requests.md).


**P:** Czy mogę skonwertować istniejące zestawy skalowania do usługi Azure Machine Scale Sets?

Odp **.:** Nie, ustawienie `Spot` flagi jest obsługiwane tylko w czasie tworzenia.


**P:** Czy jeśli korzystam z `low` zestawów skalowania o niskim priorytecie, czy muszę zacząć używać `Spot` zamiast tego?

Odp **.:** Na razie obie `low` i `Spot` będą działały, ale należy rozpocząć przechodzenie do korzystania z programu `Spot` .


**P:** Czy mogę utworzyć zestaw skalowania z regularnymi maszynami wirtualnymi i Virtual Machinesem na platformie Azure?

Odp **.:** Nie, zestaw skalowania nie może obsługiwać więcej niż jednego typu priorytetu.


**P:**  Czy mogę używać funkcji automatycznego skalowania z zestawami skalowania maszyn wirtualnych platformy Azure?

Odp **.:** Tak. możesz ustawić reguły skalowania automatycznego w zestawie skalowania maszyn wirtualnych platformy Azure. Jeśli maszyny wirtualne zostaną wykluczone, Skalowanie automatyczne może próbować utworzyć nowe Virtual Machines usługi Azure spot. Pamiętaj, że nie gwarantujesz tej pojemności. 


**P:**  Czy automatyczne skalowanie jest wykonywane z użyciem zasad wykluczania (cofanie alokacji i usuwanie)?

Odp **.:** Tak, jednak zalecane jest ustawienie zasad wykluczania do usunięcia podczas korzystania z funkcji automatycznego skalowania. Wynika to z faktu, że nieprzypisane wystąpienia są zliczane względem liczby pojemności w zestawie skalowania. W przypadku korzystania z funkcji automatycznego skalowania prawdopodobnie dojdziemy do szybszej liczby wystąpień docelowych ze względu na cofnięte alokacje. Ponadto wykluczanie danych może mieć wpływ na operacje skalowania. Na przykład wystąpienia zestawu skalowania maszyn wirtualnych mogą spaść poniżej zestawu wartości minimalnej ze względu na wykluczenie wielu punktów podczas operacji skalowania. 


**P:** Gdzie mogę publikować pytania?

Odp **.:** Możesz ogłosić pytanie i oznaczyć je za pomocą `azure-spot` [elementu Q&a](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Następne kroki

Sprawdź [Cennik zestawu skalowania maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) , aby uzyskać szczegółowe informacje o cenach.
