---
title: Przenoszenie przestrzeni nazw Service Bus do innego regionu | Microsoft Docs
description: W tym artykule pokazano, jak przenieść przestrzeń nazw Azure Service Bus z bieżącego regionu do innego regionu.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88861065"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Przenoszenie przestrzeni nazw Azure Service Bus do innego regionu
Istnieją różne scenariusze, w których należy przenieść istniejącą przestrzeń nazw Service Bus z jednego regionu do innego. Na przykład możesz chcieć utworzyć przestrzeń nazw o tej samej konfiguracji do testowania. Możesz również utworzyć pomocniczą przestrzeń nazw w innym regionie w ramach [planowania odzyskiwania po awarii](service-bus-geo-dr.md).

Poniżej przedstawiono ogólne czynności:

1. Eksportuj Service Bus przestrzeń nazw w bieżącym regionie do szablonu Azure Resource Manager. 
1. Aktualizacja lokalizacji zasobów w szablonie. Ponadto Usuń domyślny filtr subskrypcji z szablonu, ponieważ nie możesz utworzyć reguły domyślnej, ponieważ jest ona automatycznie tworzona. 
1. Użyj szablonu, aby wdrożyć przestrzeń nazw Service Bus w regionie docelowym. 
1. Sprawdź wdrożenie, aby upewnić się, że wszystkie tematy dotyczące obszaru nazw, kolejek, tematów i subskrypcji tematów zostały utworzone w regionie docelowym. 
1. Ukończ przeniesienie, usuwając przestrzeń nazw z regionu źródłowego po przetworzeniu wszystkich komunikatów. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że Azure Service Bus i funkcje używane przez konto są obsługiwane w regionie docelowym.
 
## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów. Ten szablon zawiera ustawienia opisujące przestrzeń nazw Service Bus.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** , a następnie wybierz przestrzeń nazw Service Bus.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Na stronie **Eksportuj szablon** wybierz pozycję **Pobierz** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Pobierz szablon Menedżer zasobów":::
5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu. Ten plik zip zawiera pliki szablonu i parametrów JSON. 
1. Otwórz template.jsna pliku w wyodrębnionym folderze. 
1. Wyszukaj `location` i Zastąp wartość właściwości nową nazwą regionu lub lokalizacji. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .
1. Usuń definicje zasobów typu: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Nie zapomnij usunąć znaku przecinka ( `,` ) poprzedzającego tę sekcję, aby zachować prawidłowy kod JSON.  

    > [!NOTE]
    > Nie można utworzyć reguły domyślnej dla subskrypcji przy użyciu szablonu Menedżer zasobów. Reguła domyślna jest tworzona automatycznie podczas tworzenia subskrypcji w regionie docelowym. 

## <a name="move"></a>Move
Wdróż szablon, aby utworzyć Service Bus przestrzeń nazw w regionie docelowym. 

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu** dla tekstu wyszukiwania, wybierz **Template Deployment (Wdróż przy użyciu szablonów niestandardowych)**, a następnie naciśnij klawisz **Enter**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Wdrożenie nowego szablonu":::    
1. Na stronie **Template Deployment** wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Wdrażanie nowego szablonu — przycisk Utwórz":::        
1. Na stronie **wdrożenie niestandardowe** wybierz opcję **Kompiluj własny szablon w edytorze**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Tworzenie własnego szablonu przy użyciu linku edytora":::            
1. Na stronie **Edytuj szablon** wybierz pozycję **Załaduj plik** na pasku narzędzi, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** do pliku pobranego w ostatniej sekcji.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Wybierz szablon":::                
1. Wybierz pozycję **Zapisz** , aby zapisać szablon. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Zapisz szablon":::                    
1. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję** platformy Azure. 
    2. Wybierz istniejącą **grupę zasobów** lub utwórz ją. 
    3. Wybierz **lokalizację** docelową lub region. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    4. Wprowadź nową **nazwę przestrzeni nazw**.
    1. Wybierz pozycję **Przejrzyj i utwórz**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Wdróż szablon Menedżer zasobów":::
    1. Na stronie **Recenzja i tworzenie** wybierz pozycję **Utwórz** w dolnej części strony. 
    
## <a name="verify"></a>Weryfikacja
1. Po pomyślnym wdrożeniu wybierz pozycję **Przejdź do grupy zasobów**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Łącze przejdź do grupy zasobów":::    
1. Na stronie **Grupa zasobów** wybierz przestrzeń nazw Service Bus. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Wybierz Service Bus przestrzeń nazw":::    
1. Na stronie **obszar nazw Service Bus** Sprawdź, czy są widoczne kolejki, tematy i subskrypcje z regionu źródłowego. 
    1. Wyświetlane są **kolejki** w przestrzeni nazw u dołu okienka po prawej stronie.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Kolejki w przestrzeni nazw":::
    2. Przejdź do karty **Tematy** , aby wyświetlić tematy w przestrzeni nazw
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Tematy w przestrzeni nazw":::
    3. Wybierz temat, aby sprawdzić, czy są tworzone subskrypcje. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Subskrypcje tematów":::      
    
    

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Jeśli po wdrożeniu chcesz zacząć od początku, możesz usunąć **docelową przestrzeń nazw Service Bus** i powtórzyć kroki opisane w sekcjach [przygotowanie](#prepare) i [przeniesienie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie przestrzeni nazw Service Bus, Usuń **przestrzeń nazw Service Bus źródłowej**. Upewnij się, że wszystkie komunikaty są przetwarzane przed usunięciem przestrzeni nazw. 

Aby usunąć Service Bus przestrzeń nazw (Źródło lub cel) przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **Service Bus** i wybierz **Service Bus** z wyników wyszukiwania. Na liście są widoczne Service Bus przestrzenie nazw.
2. Wybierz docelową przestrzeń nazw do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 

    ![Usuń przestrzeń nazw — przycisk](./media/move-across-regions/delete-namespace-button.png)
3. Na stronie **usuwanie zasobów** Sprawdź wybrane zasoby i Potwierdź usunięcie, wpisując **tak**, a następnie wybierz pozycję **Usuń**. 

    Inna opcja polega na usunięciu grupy zasobów, która ma Service Bus przestrzeni nazw. Na stronie **Grupa zasobów** wybierz pozycję **Usuń grupę zasobów** na pasku narzędzi, a następnie Potwierdź usunięcie. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono przestrzeń nazw Azure Service Bus z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:

- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
