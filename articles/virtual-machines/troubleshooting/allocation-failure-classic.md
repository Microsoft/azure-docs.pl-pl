---
title: Rozwiązywanie problemów z błędami alokacji maszyn wirtualnych platformy Azure w klasycznym modelu wdrażania | Microsoft Docs
description: Rozwiązywanie problemów z błędami alokacji podczas tworzenia, ponownego uruchamiania lub zmiany rozmiaru klasycznej maszyny wirtualnej na platformie Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77913382"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Procedury rozwiązywania problemów dotyczących scenariuszy awarii alokacji w klasycznym modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Poniżej przedstawiono typowe scenariusze alokacji, które powodują Przypinanie żądania alokacji. Szczegółowe się w każdym scenariuszu w dalszej części tego artykułu.

- Zmienianie rozmiaru maszyny wirtualnej lub Dodawanie maszyn wirtualnych lub wystąpień ról do istniejącej usługi w chmurze
- Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Wdrożenia przejściowe i produkcyjne (tylko platforma jako usługa)
- Grupa koligacji (bliskość maszyny wirtualnej lub usługi)
- Koligacja — Sieć wirtualna oparta na grupach

Po otrzymaniu błędu alokacji Sprawdź, czy którykolwiek z wymienionych scenariuszy ma zastosowanie do błędu. Użyj błędu alokacji zwracanego przez platformę Azure, aby zidentyfikować odpowiedni scenariusz. Jeśli Twoje żądanie jest przypięte, Usuń część ograniczeń przypinania, aby otworzyć żądanie do większej liczby klastrów, a tym samym zwiększyć szansę sukcesu alokacji.
Ogólnie rzecz biorąc, jeśli błąd nie wskazuje, że "żądany rozmiar maszyny wirtualnej nie jest obsługiwany", zawsze można ponowić próbę później. Wynika to z faktu, że wystarczająca ilość zasobów została zwolniona w klastrze w celu uwzględnienia Twojego żądania. Jeśli problem nie jest obsługiwany przez żądany rozmiar maszyny wirtualnej, spróbuj użyć innego rozmiaru maszyny wirtualnej. W przeciwnym razie jedyną opcją jest usunięcie ograniczenia przypinania.

Dwa typowe scenariusze awarii są powiązane z grupami koligacji. W przeszłości grupa koligacji została użyta w celu zapewnienia bliskiej bliskości maszynom wirtualnym i wystąpieniu usług lub została użyta do utworzenia sieci wirtualnej. Po wprowadzeniu regionalnych sieci wirtualnych grupy koligacji nie są już wymagane do utworzenia sieci wirtualnej. W przypadku zmniejszenia opóźnień sieci w infrastrukturze platformy Azure zaleca się użycie grup koligacji dla maszyn wirtualnych lub bliskości usługi.

Na poniższym diagramie przedstawiono taksonomię (przypiętą) scenariuszy alokacji. 

![Przypięta Taksonomia alokacji](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Zmienianie rozmiaru maszyny wirtualnej lub Dodawanie maszyn wirtualnych lub wystąpień ról do istniejącej usługi w chmurze
**Błąd**

Upgrade_VMSizeNotSupported lub GeneralError

**Przyczyna przypinania klastra**

Żądanie zmiany rozmiaru maszyny wirtualnej lub dodanie maszyny wirtualnej lub wystąpienia roli do istniejącej usługi w chmurze musi być podejmowane w oryginalnym klastrze, który hostuje istniejącą usługę w chmurze. Utworzenie nowej usługi w chmurze umożliwi platformie Azure znalezienie innego klastra z bezpłatnymi zasobami lub obsługę żądanego rozmiaru maszyny wirtualnej.

**Obejście**

Jeśli błąd jest Upgrade_VMSizeNotSupported *, spróbuj użyć innego rozmiaru maszyny wirtualnej. Jeśli nie jest używana opcja innego rozmiaru maszyny wirtualnej, ale jeśli jest ona akceptowalna do użycia innego wirtualnego adresu IP (VIP), Utwórz nową usługę w chmurze do obsługi nowej maszyny wirtualnej i Dodaj nową usługę w chmurze do regionalnej sieci wirtualnej, w której są uruchomione istniejące maszyny wirtualne. Jeśli istniejąca usługa w chmurze nie używa regionalnej sieci wirtualnej, można nadal utworzyć nową sieć wirtualną dla nowej usługi w chmurze, a następnie połączyć [istniejącą sieć wirtualną z nową siecią wirtualną](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej na temat [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Jeśli błąd to GeneralError *, prawdopodobnie typ zasobu (na przykład określony rozmiar maszyny wirtualnej) jest obsługiwany przez klaster, ale w tej chwili klaster nie ma bezpłatnych zasobów. Podobnie jak w powyższym scenariuszu, Dodaj żądany zasób obliczeniowy za pomocą tworzenia nowej usługi w chmurze (należy pamiętać, że nowa usługa w chmurze musi używać innego adresu VIP) i używać regionalnej sieci wirtualnej do łączenia usług w chmurze.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Błąd**

GeneralError*

**Przyczyna przypinania klastra**

Częściowe cofanie alokacji oznacza, że zostało zatrzymane (cofnięto przydział) co najmniej jednej maszyny wirtualnej w usłudze w chmurze. Po zatrzymaniu (cofnięciu) maszyny wirtualnej skojarzone zasoby zostaną wydane. Ponowne uruchomienie maszyny wirtualnej zatrzymanej (z cofniętą alokacją) jest w związku z tym nowym żądaniem alokacji. Ponowne uruchamianie maszyn wirtualnych w częściowo cofniętej usłudze w chmurze jest równoznaczne z dodaniem maszyn wirtualnych do istniejącej usługi w chmurze. Żądanie alokacji musi być podejmowane w oryginalnym klastrze, który jest hostem istniejącej usługi w chmurze. Utworzenie innej usługi w chmurze pozwala platformie Azure znaleźć inny klaster, który ma bezpłatny zasób lub obsługuje żądany rozmiar maszyny wirtualnej.

**Obejście**

Jeśli jest to możliwe, aby użyć innego adresu VIP, Usuń zatrzymane (cofnięte) maszyny wirtualne (ale przechowuj skojarzone dyski) i Dodaj maszyny wirtualne z powrotem za pośrednictwem innej usługi w chmurze. Połącz usługi w chmurze za pomocą regionalnej sieci wirtualnej:

* Jeśli istniejąca usługa w chmurze używa regionalnej sieci wirtualnej, wystarczy dodać nową usługę w chmurze do tej samej sieci wirtualnej.
* Jeśli istniejąca usługa w chmurze nie używa regionalnej sieci wirtualnej, Utwórz nową sieć wirtualną dla nowej usługi w chmurze, a następnie [Połącz istniejącą sieć wirtualną z nową siecią wirtualną](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej na temat [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Błąd**

GeneralError*

**Przyczyna przypinania klastra**

Całkowite cofanie alokacji oznacza, że wszystkie maszyny wirtualne z usługi w chmurze zostały zatrzymane (cofnięte alokacje). Żądania alokacji dotyczące ponownego uruchomienia tych maszyn wirtualnych muszą być podejmowane w oryginalnym klastrze, który hostuje usługę w chmurze. Utworzenie nowej usługi w chmurze umożliwi platformie Azure znalezienie innego klastra z bezpłatnymi zasobami lub obsługę żądanego rozmiaru maszyny wirtualnej.

**Obejście**

Jeśli jest to możliwe, aby użyć innego adresu VIP, należy usunąć oryginalne zatrzymane (bez przydziału) maszyny wirtualne (ale zachować skojarzone dyski) i usunąć odpowiednią usługę w chmurze (skojarzone zasoby obliczeniowe zostały już wydane po zatrzymaniu (cofnięciu przydziału) maszyn wirtualnych. Utwórz nową usługę w chmurze, aby ponownie dodać maszyny wirtualne.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Wdrożenia przejściowe/produkcyjne (tylko platforma jako usługa)
**Błąd**

New_General * lub New_VMSizeNotSupported *

**Przyczyna przypinania klastra**

Wdrożenie przejściowe oraz wdrożenie produkcyjne usługi w chmurze są hostowane w tym samym klastrze. Po dodaniu drugiego wdrożenia zostanie podjęta próba odpowiedniego żądania alokacji w tym samym klastrze, który hostuje pierwsze wdrożenie.

**Obejście**

Usuń pierwsze wdrożenie i oryginalną usługę w chmurze i ponownie Wdróż usługę w chmurze. Ta akcja może obsłużyć pierwsze wdrożenie w klastrze z wystarczającą ilością wolnego miejsca do wdrożenia lub w klastrze, który obsługuje żądane rozmiary maszyn wirtualnych.

## <a name="affinity-group-vmservice-proximity"></a>Grupa koligacji (bliskość maszyny wirtualnej/usługi)
**Błąd**

New_General * lub New_VMSizeNotSupported *

**Przyczyna przypinania klastra**

Wszystkie zasoby obliczeniowe przypisane do grupy koligacji są powiązane z jednym klastrem. Nowe żądania zasobów obliczeniowych w tej grupie koligacji są podejmowane w tym samym klastrze, w którym są hostowane istniejące zasoby. Jest to prawdziwe, czy nowe zasoby są tworzone za pomocą nowej usługi w chmurze, czy za pomocą istniejącej usługi w chmurze.

**Obejście**

Jeśli grupa koligacji nie jest konieczna, nie należy używać grupy koligacji ani grupować zasobów obliczeniowych w wielu grupach koligacji.

## <a name="affinity-group-based-virtual-network"></a>Koligacja — Sieć wirtualna oparta na grupach
**Błąd**

New_General * lub New_VMSizeNotSupported *

**Przyczyna przypinania klastra**

Przed wprowadzeniem regionalnych sieci wirtualnych należy skojarzyć sieć wirtualną z grupą koligacji. W związku z tym zasoby obliczeniowe znajdujące się w grupie koligacji są powiązane z tymi samymi ograniczeniami, co opisano w sekcji "scenariusz alokacji: koligacja (maszyna wirtualna/Usługa bliska)". Zasoby obliczeniowe są powiązane z jednym klastrem.

**Obejście**

Jeśli nie potrzebujesz grupy koligacji, Utwórz nową regionalną sieć wirtualną dla nowych dodawanych zasobów, a następnie [Połącz istniejącą sieć wirtualną z nową siecią wirtualną](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej na temat [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternatywnie można [przeprowadzić migrację sieci wirtualnej opartej na grupach koligacji do regionalnej sieci wirtualnej](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), a następnie ponownie dodać wymagane zasoby.


