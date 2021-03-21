---
title: Przenoszenie przestrzeni nazw platformy Azure Event Hubs do innego regionu | Microsoft Docs
description: W tym artykule pokazano, jak przenieść przestrzeń nazw platformy Azure Event Hubs z bieżącego regionu do innego regionu.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019926"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Przenoszenie przestrzeni nazw Event Hubs platformy Azure do innego regionu
W tym artykule pokazano, jak wyeksportować szablon Azure Resource Manager dla istniejącej przestrzeni nazw Event Hubs, a następnie użyć szablonu, aby utworzyć przestrzeń nazw z tymi samymi ustawieniami konfiguracji w innym regionie. Jednak ten proces nie powoduje przeniesienia zdarzeń, które nie zostały jeszcze przetworzone. Przed usunięciem należy przetworzyć zdarzenia z oryginalnej przestrzeni nazw.
 
Jeśli masz inne zasoby w grupie zasobów platformy Azure, która zawiera obszar nazw Event Hubs, możesz chcieć wyeksportować szablon na poziomie grupy zasobów, aby wszystkie powiązane zasoby można przenieść do nowego regionu w jednym kroku. Kroki opisane w tym artykule pokazują, jak wyeksportować **przestrzeń nazw** do szablonu. Procedura eksportowania **grupy zasobów** do szablonu jest podobna. 

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez konto są obsługiwane w regionie docelowym.
- Jeśli **Funkcja przechwytywania** została włączona dla centrów zdarzeń w przestrzeni nazw, przenieś ją do [usługi Azure Storage lub Azure Data Lake Store generacji 2](../storage/common/storage-account-move.md) lub [Azure Data Lake Store generacji 1](../data-lake-store/data-lake-store-migration-cross-region.md) przed przeniesieniem przestrzeni nazw Event Hubs. Możesz również przenieść grupę zasobów zawierającą przestrzenie nazw magazynu i Event Hubs do innego regionu, wykonując czynności podobne do tych opisanych w tym artykule. 
- Jeśli przestrzeń nazw Event Hubs znajduje się w **klastrze Event Hubs**, [Przenieś dedykowany klaster](move-cluster-across-regions.md) do **regionu docelowego** przed wykonaniem kroków opisanych w tym artykule. Możesz również użyć [szablonu szybkiego startu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) , aby utworzyć klaster Event Hubs. W szablonie Usuń część przestrzeni nazw pliku JSON, aby utworzyć tylko klaster. 

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów. Ten szablon zawiera ustawienia opisujące przestrzeń nazw Event Hubs.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** , a następnie wybierz przestrzeń nazw Event Hubs.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Na stronie **Eksportuj szablon** wybierz pozycję **Pobierz** .

    ![Pobierz szablon Menedżer zasobów](./media/move-across-regions/download-template.png)
5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu.

   Ten plik zip zawiera pliki. JSON, które zawierają szablon i skrypty do wdrożenia szablonu.


## <a name="move"></a>Move

Wdróż szablon w celu utworzenia Event Hubs przestrzeni nazw w regionie docelowym. 


1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu** i wybierz **Template Deployment (Wdróż przy użyciu szablonów niestandardowych)**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** pliku pobranego w ostatniej sekcji.
1. Zaktualizuj wartość `location` właściwości, aby wskazywała na nowy region. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .
1. Wybierz pozycję **Zapisz** , aby zapisać szablon. 
1. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję** platformy Azure. 
    2. Wybierz istniejącą **grupę zasobów** lub utwórz ją. Jeśli źródłowa przestrzeń nazw znajdowała się w klastrze Event Hubs, wybierz grupę zasobów zawierającą klaster w regionie docelowym. 
    3. Wybierz **lokalizację** docelową lub region. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    4. W sekcji **Ustawienia** wykonaj następujące czynności:    
        1. Wprowadź nową **nazwę przestrzeni nazw**. 

            ![Wdróż szablon Menedżer zasobów](./media/move-across-regions/deploy-template.png)
        2. Jeśli źródłowa przestrzeń nazw znajdowała się w **klastrze Event Hubs**, wprowadź nazwy **grupy zasobów** i **Event Hubs klastra** jako część **identyfikatora zewnętrznego**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Jeśli centrum zdarzeń w przestrzeni nazw używa konta magazynu do przechwytywania zdarzeń, określ nazwę grupy zasobów i konto magazynu dla `StorageAccounts_<original storage account name>_external` pola. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony. 
    1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.   

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Jeśli po wdrożeniu chcesz zacząć od początku, możesz usunąć **docelową przestrzeń nazw Event Hubs** i powtórzyć kroki opisane w sekcjach [przygotowanie](#prepare) i [przeniesienie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie przestrzeni nazw Event Hubs, Usuń **przestrzeń nazw Event Hubs** w pierwotnym regionie. Upewnij się, że wszystkie zdarzenia w przestrzeni nazw zostały przetworzone przed usunięciem przestrzeni nazw. 

Aby usunąć Event Hubs przestrzeń nazw (źródłowa lub docelowa) przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **Event Hubs** i wybierz **Event Hubs** z wyników wyszukiwania. Na liście są widoczne Event Hubs przestrzenie nazw.
2. Wybierz docelową przestrzeń nazw do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 

    ![Usuń przestrzeń nazw — przycisk](./media/move-across-regions/delete-namespace-button.png)
3. Na stronie **usuwanie przestrzeni nazw** Potwierdź usunięcie, wpisując **nazwę przestrzeni nazw**, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono przestrzeń nazw Event Hubs platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../site-recovery/azure-to-azure-tutorial-migrate.md)
