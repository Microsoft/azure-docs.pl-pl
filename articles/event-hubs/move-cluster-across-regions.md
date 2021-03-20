---
title: Przenoszenie dedykowanego klastra Event Hubs platformy Azure do innego regionu | Microsoft Docs
description: W tym artykule pokazano, jak przenieść dedykowany klaster platformy Azure Event Hubs z bieżącego regionu do innego regionu.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89380835"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Przenoszenie dedykowanego klastra Event Hubs platformy Azure do innego regionu
W tym artykule opisano sposób eksportowania szablonu Azure Resource Manager dla istniejącego Event Hubs dedykowanego klastra, a następnie użycia tego szablonu do utworzenia klastra z tymi samymi ustawieniami konfiguracji w innym regionie. 

Jeśli masz inne zasoby, takie jak przestrzenie nazw i centra zdarzeń w grupie zasobów platformy Azure, która zawiera klaster Event Hubs, możesz chcieć wyeksportować szablon na poziomie grupy zasobów, aby wszystkie powiązane zasoby mogły zostać przeniesione do nowego regionu w jednym kroku. Kroki opisane w tym artykule pokazują, jak wyeksportować **klaster Event Hubs** do szablonu. Procedura eksportowania **grupy zasobów** do szablonu jest podobna. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że dedykowany klaster można utworzyć w regionie docelowym. Najprostszym sposobem, aby dowiedzieć się, jest użycie Azure Portal w celu [utworzenia Event Hubs dedykowanego klastra](event-hubs-dedicated-cluster-create-portal.md). Zostanie wyświetlona lista regionów, które są obsługiwane w tym momencie podczas tworzenia klastra. 

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów. Ten szablon zawiera ustawienia opisujące Event Hubs dedykowany klaster.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** , a następnie wybierz dedykowany klaster Event Hubs.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Na stronie **Eksportuj szablon** wybierz pozycję **Pobierz** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Pobierz szablon Menedżer zasobów" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu.

   Ten plik zip zawiera pliki. JSON, które zawierają szablon i skrypty do wdrożenia szablonu.


## <a name="move"></a>Move

Wdróż szablon, aby utworzyć dedykowany klaster Event Hubs w regionie docelowym. 


1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu** i wybierz **Template Deployment (Wdróż przy użyciu szablonów niestandardowych)**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** pliku pobranego w ostatniej sekcji.
1. Zaktualizuj wartość `location` właściwości, aby wskazywała na nowy region. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .
1. Wybierz pozycję **Zapisz** , aby zapisać szablon. 
1. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję** platformy Azure. 
    2. Wybierz istniejącą **grupę zasobów** lub utwórz ją. 
    3. Wybierz **lokalizację** docelową lub region. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    4. W sekcji **Ustawienia** wykonaj następujące czynności:    
        1. Wprowadź nazwę nowego **klastra**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Wdróż szablon Menedżer zasobów":::
    5. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony. 
    1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.  

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Jeśli po wdrożeniu chcesz zacząć od początku, możesz usunąć **element docelowy Event Hubs dedykowany klaster** i powtórzyć kroki opisane w sekcjach [przygotowanie](#prepare) i [przeniesienie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie klastra Event Hubs, Usuń **klaster Event Hubs** w pierwotnym regionie. 

Aby usunąć klaster Event Hubs (Źródło lub cel) przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **Event Hubs klastrów**, a następnie wybierz pozycję **klastry Event Hubs** z wyników wyszukiwania. Na liście zostanie wyświetlony klaster Event Hubs.
2. Wybierz klaster do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. Na stronie **usuwanie klastra** Potwierdź usunięcie, wpisując **nazwę klastra**, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób przenoszenia Event Hubs dedykowanego klastra z jednego regionu do innego. 

Aby uzyskać instrukcje dotyczące przenoszenia przestrzeni nazw z jednego regionu do innego regionu, zobacz artykuł [przenieś Event Hubs przestrzenie nazw w różnych regionach](move-across-regions.md) . 

Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:

- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../site-recovery/azure-to-azure-tutorial-migrate.md)
