---
title: Samouczek — wysoka dostępność maszyn wirtualnych z systemem Linux na platformie Azure
description: Z tego samouczka dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure wdrażać maszyny wirtualne o wysokiej dostępności w zestawach dostępności
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e04f6120f7d9a5646a3b30553b5083c5259acae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499640"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Samouczek: tworzenie i wdrażanie maszyn wirtualnych o wysokiej dostępności za pomocą interfejsu wiersza polecenia platformy Azure

W tym samouczku dowiesz się, jak zwiększyć dostępność i niezawodność rozwiązań korzystających z maszyn wirtualnych na platformie Azure przy użyciu funkcji zestawów dostępności. Zestawy dostępności zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi klastrami sprzętowymi. Dzięki temu ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a całe rozwiązanie nadal będzie dostępne i funkcjonalne.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Omówienie

Zestaw dostępności to dostępna na platformie Azure funkcja grupowania logicznego, zapewniająca izolację zawartych w tej grupie maszyn wirtualnych wdrożonych w centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. Ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a cała aplikacja nadal będzie działała i pozostanie dostępna dla klientów. Zestawy dostępności stanowią niezbędną funkcję podczas tworzenia niezawodnych rozwiązań w chmurze.

Rozważmy typowe rozwiązanie z użyciem maszyn wirtualnych, obejmujące cztery serwery internetowe frontonu oraz dwie maszyny wirtualne zaplecza, na których jest hostowana baza danych. Przed wdrożeniem maszyn wirtualnych na platformie Azure należałoby w takim przypadku zdefiniować dwa zestawy dostępności: jeden dla warstwy „Internet”, a drugi dla warstwy „baza danych”. Podczas tworzenia nowej maszyny wirtualnej można określić zestaw dostępności jako parametr polecenia az vm create, a platforma Azure automatycznie zapewni izolację maszyn wirtualnych tworzonych w ramach tego zestawu dostępności na wielu fizycznych zasobach sprzętowych. W przypadku problemu ze sprzętem fizycznym, na którym jest uruchomiona jedna z maszyn wirtualnych serwera internetowego lub serwera bazy danych, masz pewność, że pozostałe wystąpienia maszyn wirtualnych serwera internetowego i bazy danych będą nadal działać, ponieważ korzystają z innego sprzętu.


## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Aby utworzyć zestaw dostępności, użyj polecenia [az vm availability-set create](/cli/azure/vm/availability-set). W tym przykładzie liczbę domen aktualizacji i domen błędów ustawiono na *2* dla zestawu dostępności o nazwie *myAvailabilitySet* w grupie zasobów *myResourceGroupAvailability*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create), a następnie utwórz zestaw dostępności:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Zestawy dostępności pozwalają izolować zasoby w domenach błędów i aktualizować domeny. **Domeny błędów** reprezentują izolowaną kolekcję zasobów serwer + sieć + magazyn. W poprzednim przykładzie zestaw dostępności jest rozpraszany w co najmniej dwóch domenach błędów, gdy są wdrażane maszyny wirtualne. Zestaw dostępności jest również rozpraszany w dwóch **domenach aktualizacji**. Dwie domeny aktualizacji zapewniają, że w przypadku, gdy platforma Azure przeprowadza aktualizacje oprogramowania, zasoby maszyn wirtualnych są izolowane, zapobiegając jednoczesnej aktualizacji całego oprogramowania działającego na maszynie wirtualnej.


## <a name="create-vms-inside-an-availability-set"></a>Tworzenie maszyn wirtualnych w zestawie dostępności

Aby zapewnić właściwe rozproszenie maszyn wirtualnych na sprzęcie, należy utworzyć je w ramach zestawu dostępności. Do zestawu dostępności nie można dodać istniejącej maszyny wirtualnej po jej utworzeniu.

Podczas tworzenia maszyny wirtualnej za pomocą polecenia [az vm create](/cli/azure/vm) określ nazwę zestawu dostępności przy użyciu parametru `--availability-set`.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

W zestawie dostępności istnieją teraz dwie maszyny wirtualne. Ponieważ są one w tym samym zestawie dostępności, platforma Azure zapewnia, że maszyny wirtualne i ich zasoby (w tym dyski z danymi) są rozmieszczone na izolowanym sprzęcie fizycznym. Takie rozmieszczenie zapewnia znacznie wyższą dostępność ogólnego rozwiązania maszyny wirtualnej.

Rozmieszczenie zestawu dostępności można wyświetlić w portalu, wybierając pozycję Grupy zasobów > myResourceGroupAvailability > myAvailabilitySet. Maszyny wirtualne są rozproszone w dwóch domenach błędów i aktualizacji, jak pokazano na poniższym przykładzie:

![Zestaw dostępności w portalu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sprawdzanie dostępnych rozmiarów maszyn wirtualnych

Dodatkowe maszyny wirtualne można dodać do zestawu dostępności później, o ile rozmiary maszyn wirtualnych są dostępne na sprzęcie. Użyj opcji [AZ VM Availability-Set list-](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) Sizes, aby wyświetlić listę wszystkich dostępnych rozmiarów w klastrze sprzętowym zestawu dostępności:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych

Przejdź do następnego samouczka, aby poznać zestawy skalowania maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md)

* Aby dowiedzieć się więcej o strefach dostępności, zapoznaj się z  [dokumentacją strefy dostępności](../../availability-zones/az-overview.md).
* [Dostępna jest](./manage-availability.md)również większa dokumentacja dotycząca obu zestawów dostępności i stref dostępności.
* Aby wypróbować strefy dostępności, odwiedź stronę [Tworzenie maszyny wirtualnej z systemem Linux w strefie dostępności przy użyciu interfejsu wiersza polecenia platformy Azure](./create-cli-availability-zone.md)
