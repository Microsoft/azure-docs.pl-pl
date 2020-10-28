---
title: Praca z dużymi Virtual Machine Scale Sets platformy Azure
description: Co należy wiedzieć o dużych zestawach skalowania maszyn wirtualnych platformy Azure, aby móc używać ich w aplikacji.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: ffa2a3a921e988b92ad90831041a6fb4d321bc42
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747816"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Praca z dużymi zestawami skalowania maszyn wirtualnych
Możliwe jest teraz tworzenie [zestawów skalowania maszyn wirtualnych platformy Azure](./index.yml) o pojemności do 1000 maszyn wirtualnych. W tym dokumencie _duży zestaw skalowania maszyn wirtualnych_ jest zdefiniowany jako zestaw skalowania umożliwiający skalowanie do ponad 100 maszyn wirtualnych. Ta funkcja jest ustawiana za pomocą właściwości zestawu skalowania ( _singlePlacementGroup=False_ ). 

Niektóre aspekty dużych zestawów skalowania, na przykład równoważenie obciążenia i domeny błędów, działają inaczej niż w przypadku standardowych zestawów skalowania. Ten dokument zawiera wyjaśnienie charakterystyk dużych zestawów skalowania oraz informacje potrzebne, aby pomyślnie używać ich w ramach aplikacji. 

Powszechnym podejściem do wdrażania infrastruktury chmury na dużą skalę jest tworzenie zestawu _jednostek skalowania_ , na przykład przez utworzenie wielu zestawów skalowania maszyn wirtualnych w obrębie wielu sieci wirtualnych i kont magazynu. Metoda ta umożliwia łatwiejsze zarządzanie w porównaniu do pojedynczej maszyny wirtualnej, a posiadanie wielu jednostek skalowania jest przydatne w przypadku wielu aplikacji, zwłaszcza tych, które wymagają innych składników możliwych do umieszczenia na stosie, takich jak wiele sieci wirtualnych i punktów końcowych. Jeśli jednak aplikacja wymaga pojedynczego dużego klastra, prostsze może okazać się wdrożenie pojedynczego zestawu skalowania zawierającego maksymalnie 1000 maszyn wirtualnych. Przykładowe scenariusze obejmują scentralizowane wdrożenia danych big data lub sieci obliczeniowe wymagające prostego zarządzania dużą pulą węzłów procesu roboczego. Duże zestawy skalowania połączone z [dołączonymi dyskami danych](virtual-machine-scale-sets-attached-disks.md) zestawu skalowania maszyn wirtualnych umożliwiają wdrażanie w ramach jednej operacji skalowalnej infrastruktury składającej się z tysięcy procesorów wirtualnych i petabajtów magazynu.

## <a name="placement-groups"></a>Grupy umieszczania 
To, co sprawia, że _duże_ zestawy skalowania są wyjątkowe, to nie liczba maszyn wirtualnych, ale liczba _grup umieszczania_ , które się w nich znajdują. Grupa umieszczania to konstrukcja podobna do zestawu dostępności platformy Azure, która zawiera własne domeny błędów i domeny uaktualnień. Domyślnie zestaw skalowania składa się z pojedynczej grupy umieszczania zawierającej maksymalnie 100 maszyn wirtualnych. Jeśli właściwość zestawu skalowania o nazwie _singlePlacementGroup_ jest ustawiona na wartość _false_ , zestaw skalowania może składać się z wielu grup umieszczania i ma zakres od 0 do 1000 maszyn wirtualnych. Jeśli właściwość jest ustawiona na domyślną wartość _true_ , zestaw skalowania składa się z pojedynczej grupy umieszczania i ma zakres od 0 do 100 maszyn wirtualnych.

## <a name="checklist-for-using-large-scale-sets"></a>Lista kontrolna na potrzeby używania dużych zestawów skalowania
Aby zdecydować, czy aplikacja może w sposób efektywny używać dużych zestawów skalowania, należy wziąć pod uwagę następujące wymagania:

- Jeśli planujesz wdrożyć wiele maszyn wirtualnych, konieczne może być zwiększenie limitów przydziału obliczeniowych procesorów wirtualnych. 
- Zestawy skalowania utworzone na podstawie obrazów portalu Azure Marketplace można skalować w górę do 1000 maszyn wirtualnych.
- Zestawy skalowania utworzone na podstawie obrazów niestandardowych (samodzielnie utworzone i przekazane obrazy maszyn wirtualnych) można aktualnie skalować w górę do 600 maszyn wirtualnych.
- Duże zestawy skalowania wymagają użycia funkcji Dyski zarządzane platformy Azure. Zestawy skalowania, które nie zostaną utworzone za pomocą funkcji Dyski zarządzane, wymagają wielu kont magazynu (jednego dla każdych 20 maszyn wirtualnych). Duże zestawy skalowania są przeznaczone do użytku tylko z funkcją Dyski zarządzane w celu ograniczenia narzutu związanego z zarządzaniem magazynem oraz uniknięcia ryzyka przekroczenia limitów subskrypcji dla kont magazynu. 
- Duża skala (SPG = false) nie obsługuje sieci InfiniBand
- Równoważenie obciążenia w warstwie 4 za pomocą zestawów skalowania składających się z wielu grup umieszczania wymaga [jednostki SKU usługi Azure Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md). Jednostka SKU usługi Load Balancer w warstwie Standardowa zapewnia dodatkowe korzyści, takie jak możliwość równoważenia obciążenia między wieloma zestawami skalowania. Jednostka SKU w warstwie Standardowa wymaga również, aby zestaw skalowania miał skojarzoną sieciową grupę zabezpieczeń. W przeciwnym razie pule translatora adresów sieciowych nie będą działać poprawnie. Jeśli konieczne jest użycie jednostki SKU usługi Azure Load Balancer w warstwie Podstawowa, upewnij się, że zestaw skalowania jest skonfigurowany pod kątem używania pojedynczej grupy umieszczania (jest to ustawienie domyślne).
- Równoważenie obciążenia w warstwie 7 za pomocą usługi Azure Application Gateway jest obsługiwane dla wszystkich zestawów skalowania.
- Zestaw skalowania jest zdefiniowany z jedną podsiecią — upewnij się, że podsieć ma wystarczająco dużą przestrzeń adresową dla wszystkich wymaganych maszyn wirtualnych. Domyślnie zestaw skalowania w celu poprawy niezawodności i wydajności wdrożenia przeprowadza nadmiarową aprowizację, czyli tworzy dodatkowe maszyny wirtualne w czasie wdrażania lub skalowania w poziomie, za które nie są naliczane opłaty. Przestrzeń adresowa powinna być o 20% większa niż liczba maszyn wirtualnych, do której planowane jest skalowanie.
- Domeny błędów i domeny uaktualnień są spójne tylko w ramach grupy umieszczania. Taka architektura nie zmienia ogólnej dostępności zestawu skalowania, ponieważ maszyny wirtualne są równomiernie rozpraszane na różnym sprzęcie fizycznym, ale w celu zagwarantowania, że dwie maszyny wirtualne znajdują się na różnym sprzęcie, upewnij się, że znajdują się one w różnych domenach błędów w tej samej grupie umieszczania. Zapoznaj się z tymi [opcjami dostępności](../virtual-machines/availability.md)linku. 
- Identyfikatory domeny błędów i grupy umieszczania są wyświetlane w _widoku wystąpienia_ maszyny wirtualnej zestawu skalowania. Widok wystąpienia maszyny wirtualnej zestawu skalowania można otworzyć w [Eksploratorze zasobów Azure](https://resources.azure.com/).

## <a name="creating-a-large-scale-set"></a>Tworzenie dużego zestawu skalowania
Podczas tworzenia zestawu skalowania w witrynie Azure Portal wystarczy określić wartość *Liczba wystąpień* nie większą niż 1000. Jeśli jest więcej niż 100 wystąpień, pozycja *Włącz skalowanie powyżej 100 wystąpień* zostanie ustawiona na *Tak* , co pozwoli na jej skalowanie do wielu grup umieszczania. 

![Ten obraz przedstawia blok wystąpienia w witrynie Azure Portal. Dostępne są opcje wybierania liczby wystąpień i rozmiaru wystąpienia.](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

Duży zestaw skalowania maszyn wirtualnych można utworzyć za pomocą polecenia [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) _az vmss create_ . To polecenie ustawia inteligentne wartości domyślne, takie jak rozmiar podsieci, na podstawie argumentu _instance-count_ :

```azurecli
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```

Polecenie _vmss create_ powoduje ustawienie pewnych domyślnych wartości konfiguracji, jeśli nie zostaną określone. Aby wyświetlić dostępne opcje, które można przesłonić:

```azurecli
az vmss create --help
```

Jeśli tworzysz duży zestaw skalowania za pośrednictwem usługi Azure Resource Manager, upewnij się, że szablon tworzy zestaw skalowania na podstawie funkcji Dyski zarządzane platformy Azure. Właściwość _singlePlacementGroup_ można ustawić na wartość _false_ w sekcji _properties_ zasobu _Microsoft.Compute/virtualMachineScaleSets_ . Poniższy fragment kodu JSON zawiera początek szablonu zestawu skalowania, w tym pojemność wynoszącą 1000 maszyn wirtualnych i ustawienie _"singlePlacementGroup" : false_ :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```

Aby zapoznać się z kompletnym przykładem szablonu dużego zestawu skalowania, zapoznaj się z tematem [https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json) .

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Konwertowanie istniejącego zestawu skalowania, aby uwzględniał wiele grup umieszczania
Aby możliwe było skalowanie istniejącego zestawu skalowania maszyn wirtualnych do ponad 100 maszyn wirtualnych, w modelu zestawu skalowania należy zmienić właściwość _singlePlacementGroup_ na wartość _false_ . Zmianę tej właściwości można przetestować za pomocą [Eksploratora zasobów Azure](https://resources.azure.com/). Znajdź istniejący zestaw skalowania, wybierz pozycję _Edytuj_ i zmień wartość właściwości _singlePlacementGroup_ . Jeśli ta właściwość nie jest widoczna, być może zestaw skalowania jest wyświetlany za pomocą starszej wersji interfejsu API Microsoft.Compute.

> [!NOTE]
> Zestaw skalowania można zmienić z obsługującego tylko pojedynczą grupę umieszczania (domyślne zachowanie) na obsługujący wiele grup umieszczania, ale nie odwrotnie. W związku z tym przed przeprowadzeniem konwersji zapoznaj się z właściwościami dużych zestawów skalowania.
