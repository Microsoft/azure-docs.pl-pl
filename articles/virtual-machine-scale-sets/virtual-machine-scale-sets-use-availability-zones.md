---
title: Utwórz zestaw skalowania platformy Azure, który używa Strefy dostępności
description: Dowiedz się, jak tworzyć zestawy skalowania maszyn wirtualnych platformy Azure, które używają Strefy dostępności, aby zwiększyć nadmiarowość przed awarią
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745839"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Utwórz zestaw skalowania maszyn wirtualnych, który używa Strefy dostępności

Aby chronić zestawy skalowania maszyn wirtualnych z niepowodzeń poziomu centrum danych, można utworzyć zestaw skalowania między Strefy dostępności. Regiony platformy Azure, które obsługują Strefy dostępności, mają co najmniej trzy oddzielne strefy, z których każda jest niezależna od źródła, sieci i chłodzenia. Aby uzyskać więcej informacji, zobacz [omówienie strefy dostępności](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

W przypadku wdrożenia zestawu skalowania Regionalnego (bez stref) w jednej lub większej liczbie stref w przypadku interfejsu API w wersji *2017-12-01* dostępne są następujące opcje dostępności:
- Maksymalne rozmieszczenie (platformFaultDomainCount = 1)
- Statyczne stałe rozmieszczenie (platformFaultDomainCount = 5)
- Rozproszenie wyrównane z domenami błędów dysku magazynu (platforFaultDomainCount = 2 lub 3)

W przypadku maksymalnego rozmieszczenia, zestaw skalowania rozkłada maszyny wirtualne w możliwie największej liczbie domen błędów w ramach każdej strefy. Ta rozproszenie może znajdować się w więcej niż pięciu domenach błędów na strefę. Ze statycznym stałym rozłożeniem zestaw skalowania rozkłada maszyny wirtualne w dokładnie pięciu domenach błędów na strefę. Jeśli zestaw skalowania nie może znaleźć pięciu odrębnych domen błędów dla strefy w celu spełnienia żądania alokacji, żądanie nie powiedzie się.

**Zalecamy wdrożenie z maksymalną ilością rozmieszczenia dla większości obciążeń** , ponieważ takie podejście zapewnia najlepsze rozmieszczenie w większości przypadków. Jeśli konieczne jest rozproszenie replik między różnymi jednostkami izolacji sprzętowej, zalecamy rozproszenie między Strefy dostępności i wykorzystać maksymalne rozmieszczenie w każdej strefie.

> [!NOTE]
> W przypadku maksymalnego rozproszenia widoczna jest tylko jedna domena błędów w widoku wystąpienia maszyny wirtualnej zestawu skalowania i w metadanych wystąpienia, niezależnie od tego, ile domen błędów są rozpraszane przez maszyny wirtualne. Rozproszenie w ramach każdej strefy jest niejawne.

### <a name="placement-groups"></a>Grupy umieszczania

Podczas wdrażania zestawu skalowania można również wykonać wdrożenie z jedną [grupą umieszczania](./virtual-machine-scale-sets-placement-groups.md) na strefę dostępności lub z wielokrotnością dla każdej strefy. W przypadku zestawów skalowania Regionalnego (non-Zona) wybór ma mieć pojedynczą grupę umieszczania w regionie lub wiele w regionie. W przypadku większości obciążeń zalecamy stosowanie wielu grup umieszczania, które umożliwiają większą skalę. W interfejsie API w wersji *2017-12-01* , skalowanie ustawia domyślnie na wiele grup umieszczania dla zestawów skalowania z jedną strefą i między strefami, ale domyślnie do pojedynczej grupy umieszczania dla zestawów skalowania Regionalnego (bez stref).

> [!NOTE]
> Jeśli używasz maksymalnego rozmieszczenia, musisz użyć wielu grup umieszczania.

### <a name="zone-balancing"></a>Równoważenie strefy

Na koniec dla zestawów skalowania wdrożonych w wielu strefach można również wybrać opcję "Najlepsza stawka strefy" lub "ścisłe saldo strefy". Zestaw skalowania jest uznawany za "zrównoważony", jeśli każda strefa ma taką samą liczbę maszyn wirtualnych lub \\ maszynę wirtualną "+-1" we wszystkich innych strefach zestawu skalowania. Na przykład:

- Zestaw skalowania z 2 maszynami wirtualnymi w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest uznawany za zrównoważony. Istnieje tylko jedna strefa z inną liczbą maszyn wirtualnych, która jest tylko 1 mniejsza niż w przypadku innych stref. 
- Zestaw skalowania z 1 maszyną wirtualną w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest traktowany jako niezrównoważony. Strefa 1 ma 2 mniej maszyn wirtualnych niż strefa 2 i 3.

Istnieje możliwość, że maszyny wirtualne w zestawie skalowania zostały pomyślnie utworzone, ale wdrożenie rozszerzeń na tych maszynach wirtualnych nie powiedzie się. Te maszyny wirtualne z błędami rozszerzeń są nadal zliczane podczas określania, czy zestaw skalowania jest zrównoważony. Na przykład zestaw skalowania z trzema maszynami wirtualnymi w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyny wirtualne w strefie 3 jest uznawany za zrównoważony, nawet jeśli wszystkie rozszerzenia w strefie 1 zostały zakończone pomyślnie, a wszystkie rozszerzenia w strefach 2 i 3.

Ze względu na optymalną strefę, zestaw skalowania próbuje skalować się i wyrównać z zachowaniem równowagi. Jednakże, jeśli z jakiegoś powodu nie jest to możliwe (na przykład jeśli jedna strefa ulegnie awarii, zestaw skalowania nie może utworzyć nowej maszyny wirtualnej w tej strefie), zestaw skalowania pozwala na tymczasowe skalowanie w celu pomyślnej skali. W przypadku kolejnych prób skalowania w poziomie zestaw skalowania dodaje maszyny wirtualne do stref, które wymagają więcej maszyn wirtualnych w celu zrównoważenia zestawu skalowania. Podobnie w przypadku kolejnej skalowanej próby zestaw skalowania usuwa maszyny wirtualne ze stref, które wymagają mniej maszyn wirtualnych, aby można było zrównoważyć zestaw skalowania. W przypadku użycia opcji "ścisłe saldo strefy" zestaw skalowania kończy się niepowodzeniem podczas skalowania w górę lub w dół, jeśli spowoduje to nierównowaga.

Aby użyć najlepszego nakładu strefowego, ustaw *zoneBalance* na *false* . To ustawienie jest domyślne w interfejsie API w wersji *2017-12-01* . Aby użyć ścisłego salda strefy, ustaw *zoneBalance* na *wartość true* .

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Pojedyncze strefy i nadmiarowe zestawy skalowania

Podczas wdrażania zestawu skalowania maszyn wirtualnych można wybrać opcję użycia pojedynczej strefy dostępności w regionie lub wielu strefach.

Podczas tworzenia zestawu skalowania w ramach jednej strefy można kontrolować, w której strefie są uruchamiane wszystkie wystąpienia maszyn wirtualnych, a zestaw skalowania jest zarządzany i automatycznie skalowany tylko w ramach tej strefy. Nadmiarowy zestaw skalowania strefowo umożliwia utworzenie jednego zestawu skalowania obejmującego wiele stref. Ponieważ wystąpienia maszyn wirtualnych są tworzone, domyślnie są równomiernie zrównoważone w strefach. W przypadku wystąpienia przerwy w jednej ze stref zestaw skalowania nie jest automatycznie skalowany w celu zwiększenia pojemności. Najlepszym rozwiązaniem jest skonfigurowanie reguł skalowania automatycznego na podstawie użycia procesora lub pamięci. Reguły skalowania automatycznego pozwolą, aby zestaw skalowania odpowiadał utracie wystąpień maszyn wirtualnych w tej strefie przez skalowanie nowych wystąpień w pozostałych strefach operacyjnych.

Aby można było używać Strefy dostępności, zestaw skalowania musi być utworzony w [obsługiwanym regionie platformy Azure](../availability-zones/az-region.md). Można utworzyć zestaw skalowania, który używa Strefy dostępności z jedną z następujących metod:

- [Witryna Azure Portal](#use-the-azure-portal)
- Interfejs wiersza polecenia platformy Azure
- [Azure PowerShell](#use-azure-powershell)
- [Szablony Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces tworzenia zestawu skalowania, który używa strefy dostępności, jest taki sam jak szczegółowy w [artykule wprowadzenie](quick-create-portal.md). Po wybraniu obsługiwanego regionu platformy Azure można utworzyć zestaw skalowania w jednej lub kilku dostępnych strefach, jak pokazano w następującym przykładzie:

![Tworzenie zestawu skalowania w pojedynczej strefie dostępności](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Zestaw skalowania i zasoby pomocnicze, takie jak moduł równoważenia obciążenia platformy Azure i publiczny adres IP, są tworzone w pojedynczej określonej strefie.

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Proces tworzenia zestawu skalowania, który używa strefy dostępności, jest taki sam jak szczegółowy w [artykule wprowadzenie](quick-create-cli.md). Aby użyć Strefy dostępności, musisz utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure.

Dodaj `--zones` parametr do polecenia [AZ VMSS Create](/cli/azure/vmss) i określ, która strefa ma być używana (na przykład strefy *1* , *2* lub *3* ). Poniższy przykład tworzy zestaw skalowania z pojedynczą strefą o nazwie *myScaleSet* w strefie *1* :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Aby zapoznać się z kompletnym przykładem zestawu skalowania z pojedynczą strefą i zasobami sieciowymi, zobacz [Ten przykładowy skrypt interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh) .

### <a name="zone-redundant-scale-set"></a>Strefowo nadmiarowy zestaw skalowania

Aby utworzyć strefowo nadmiarowy zestaw skalowania, należy użyć *standardowego* publicznego adresu IP jednostki SKU i modułu równoważenia obciążenia. W celu zwiększenia nadmiarowości *standardowa* jednostka SKU tworzy strefowo nadmiarowe zasoby sieciowe. Aby uzyskać więcej informacji, zobacz [Omówienie standardowego Azure Load Balancer](../load-balancer/load-balancer-overview.md) i [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Aby utworzyć strefowo nadmiarowy zestaw skalowania, określ wiele stref z `--zones` parametrem. Poniższy przykład tworzy strefowo nadmiarowy zestaw skalowania o nazwie *myScaleSet* w strefach *1, 2, 3* :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych w określonych strefach może potrwać kilka minut. Aby zapoznać się z kompletnym przykładem nadmiarowego zestawu skalowania i zasobów sieciowych przez strefę, zobacz [Ten przykładowy skrypt interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh) .

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby użyć Strefy dostępności, musisz utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure. Dodaj `-Zone` parametr do polecenia [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) i określ, która strefa ma być używana (na przykład strefy *1* , *2* lub *3* ).

Poniższy przykład tworzy zestaw skalowania z pojedynczą strefą o nazwie *myScaleSet* w *regionie Wschodnie stany USA 2* *.* Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Strefowo nadmiarowy zestaw skalowania

Aby utworzyć strefowo nadmiarowy zestaw skalowania, określ wiele stref z `-Zone` parametrem. Poniższy przykład tworzy strefowo nadmiarowy zestaw skalowania o nazwie *myScaleSet* w *regionach Wschodnie stany USA 2* *, 2, 3* . Strefy nadmiarowe zasoby sieciowe platformy Azure dla sieci wirtualnej, publicznego adresu IP i usługi równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

Proces tworzenia zestawu skalowania, który używa strefy dostępności, jest taki sam jak szczegółowy w artykule wprowadzenie do systemu [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Aby użyć Strefy dostępności, musisz utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure. Dodaj `zones` Właściwość do typu zasobu *Microsoft. COMPUTE/virtualMachineScaleSets* w szablonie i określ, która strefa ma być używana (na przykład strefa *1* , *2* lub *3* ).

Poniższy przykład tworzy zestaw skalowania jednostrefowego systemu Linux o nazwie *myScaleSet* w *regionie Wschodnie stany USA 2* , strefa *1* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Aby zapoznać się z kompletnym przykładem zestawu skalowania z pojedynczą strefą i zasobami sieciowymi, zobacz [Ten przykładowy szablon Menedżer zasobów](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Strefowo nadmiarowy zestaw skalowania

Aby utworzyć strefowo nadmiarowy zestaw skalowania, określ wiele wartości we `zones` właściwości dla typu zasobu *Microsoft. COMPUTE/virtualMachineScaleSets* . Poniższy przykład tworzy strefowo nadmiarowy zestaw skalowania o nazwie *myScaleSet* w *regionach Wschodnie stany USA 2* *, 2, 3* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Jeśli tworzysz publiczny adres IP lub moduł równoważenia obciążenia, określ Właściwość *"SKU": {"name": "standard"} "* , aby utworzyć nadmiarowe zasoby sieciowe. Należy również utworzyć sieciową grupę zabezpieczeń i reguły, aby zezwolić na ruch. Aby uzyskać więcej informacji, zobacz [Omówienie standardowego Azure Load Balancer](../load-balancer/load-balancer-overview.md) i [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Aby zapoznać się z kompletnym przykładem nadmiarowego zestawu skalowania i zasobów sieciowych przez strefę, zobacz [Ten przykładowy szablon Menedżer zasobów](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu zestawu skalowania w strefie dostępności można dowiedzieć się, jak [wdrażać aplikacje w zestawach skalowania maszyn wirtualnych](tutorial-install-apps-cli.md) lub [używać funkcji automatycznego skalowania z zestawami skalowania maszyn wirtualnych](tutorial-autoscale-cli.md).
