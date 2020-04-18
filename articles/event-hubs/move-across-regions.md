---
title: Przenoszenie obszaru nazw usługi Azure Event Hubs do innego regionu | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak przenieść obszar nazw usługi Azure Event Hubs z bieżącego regionu do innego regionu.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606810"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Przenoszenie obszaru nazw usługi Azure Event Hubs do innego regionu
Istnieją różne scenariusze, w których chcesz przenieść istniejący obszar nazw centrów zdarzeń z jednego regionu do drugiego. Na przykład można utworzyć obszar nazw z tą samą konfiguracją do testowania. Można również utworzyć pomocniczy obszar nazw w innym regionie w ramach [planowania odzyskiwania po awarii](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> W tym artykule pokazano, jak wyeksportować szablon usługi Azure Resource Manager dla istniejącego obszaru nazw Centrum zdarzeń, a następnie użyć szablonu do utworzenia obszaru nazw z tymi samymi ustawieniami konfiguracji w innym regionie. Jednak ten proces nie przenosi zdarzeń, które nie są jeszcze przetwarzane. Przed usunięciem zdarzenia z oryginalnej przestrzeni nazw należy je przetworzyć.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez Twoje konto są obsługiwane w regionie docelowym.
- W przypadku funkcji w wersji zapoznawczej upewnij się, że subskrypcja znajduje się na białej liście regionu docelowego.
- Jeśli **funkcja przechwytywania** jest włączona dla centrów zdarzeń w obszarze nazw, przed przeniesieniem obszaru nazw usługi [Azure Storage lub Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) lub [Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) przed przeniesieniem obszaru nazw centrum zdarzeń. Można również przenieść grupę zasobów, która zawiera zarówno magazynowania i event hubów obszarów nazw do innego regionu, wykonując kroki podobne do tych opisanych w tym artykule. 
- Jeśli obszar nazw Centrum zdarzeń znajduje się w **klastrze centrum zdarzeń,** przed przejściem przez kroki opisane w tym artykule [należy utworzyć dedykowany klaster](event-hubs-dedicated-cluster-create-portal.md) w **regionie docelowym.** 

## <a name="prepare"></a>Przygotowanie
Aby rozpocząć, wyeksportuj szablon Menedżera zasobów. Ten szablon zawiera ustawienia opisujące obszar nazw centrum zdarzeń.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **pozycję Wszystkie zasoby,** a następnie wybierz obszar nazw Centrum zdarzeń.

3. Wybierz**szablon eksportu** **> Ustawienia** > .

4. Wybierz **pozycję Pobierz** na stronie Szablon **eksportu.**

    ![Pobierz szablon Menedżera zasobów](./media/move-across-regions/download-template.png)

5. Znajdź plik zip pobrany z portalu i rozpaj ten plik do wybranego folderu.

   Ten plik zip zawiera pliki .json, które zawierają szablon i skrypty do wdrożenia szablonu.


## <a name="move"></a>Move

Wdrażanie szablonu w celu utworzenia obszaru nazw centrum zdarzeń w regionie docelowym. 


1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz pozycję **Wdrożenie szablonu**.

4. Wybierz pozycję **Utwórz**.

5. Wybierz pozycję **Utwórz własny szablon w edytorze**.

6. Wybierz **pozycję Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik **template.json** pobrany w ostatniej sekcji.

7. Wybierz **pozycję Zapisz,** aby zapisać szablon. 

8. Na stronie **Wdrożenia niestandardowego** wykonaj następujące kroki: 

    1. Wybierz **subskrypcję**platformy Azure . 

    2. Wybierz istniejącą **grupę zasobów** lub utwórz jedną. Jeśli źródłowa przestrzeń nazw znajdowała się w klastrze centrum zdarzeń, wybierz grupę zasobów zawierającą klaster w regionie docelowym. 

    3. Wybierz **lokalizację** docelową lub region. Jeśli wybrano istniejącą grupę zasobów, to ustawienie jest tylko do odczytu. 

    4. W sekcji **USTAWIENIA** wykonaj następujące czynności:
    
        1. wprowadź nową **nazwę obszaru nazw**. 

            ![Wdrażanie szablonu Menedżera zasobów](./media/move-across-regions/deploy-template.png)

        2. Jeśli źródłowy obszar nazw znajdował się w **klastrze Centrum zdarzeń,** wprowadź nazwy **grupy zasobów** i **klastra Usługi eventów** jako część **identyfikatora zewnętrznego**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Jeśli Centrum zdarzeń w obszarze nazw używa konta magazynu do przechwytywania zdarzeń, określ nazwę grupy zasobów i konto magazynu dla `StorageAccounts_<original storage account name>_external` pola. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Zaznacz pole **wyboru Zgadzam się z warunkami podanymi powyżej.** 
    
    6. Teraz wybierz **pozycję Wybierz zakup,** aby rozpocząć proces wdrażania. 

## <a name="discard-or-clean-up"></a>Wyrzucić lub oczyścić
Po wdrożeniu, jeśli chcesz rozpocząć od nowa, można usunąć **docelowy obszar nazw Centrum zdarzeń**i powtórzyć kroki opisane w sekcji [Przygotowywanie](#prepare) i [przenoszenie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie obszaru nazw Centrum zdarzeń, usuń **źródłowy obszar nazw Centrum zdarzeń**. Przed usunięciem obszaru nazw upewnij się, że wszystkie zdarzenia w obszarze nazw zostały przetworzone. 

Aby usunąć obszar nazw Centrum zdarzeń (źródło lub miejsce docelowe) przy użyciu witryny Azure portal:

1. W oknie wyszukiwania u góry **witryny**Azure portal wpisz centrum zdarzeń i wybierz centrum **zdarzeń** z wyników wyszukiwania. Na liście są widoczne przestrzenie nazw centrów zdarzeń.

2. Wybierz docelowy obszar nazw do usunięcia i wybierz pozycję **Usuń** z paska narzędzi. 

    ![Przycisk Usuń obszar nazw](./media/move-across-regions/delete-namespace-button.png)

3. Na stronie **Usuń zasoby*** sprawdź wybrane zasoby i potwierdź usunięcie, wpisując **przycisk tak,** a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono obszar nazw usługi Azure Event Hubs z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
