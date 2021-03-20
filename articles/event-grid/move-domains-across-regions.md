---
title: Przenoszenie domen Azure Event Grid do innego regionu
description: W tym artykule przedstawiono sposób przenoszenia domen Azure Event Grid z jednego regionu do innego.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89086216"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Przenoszenie domen Azure Event Grid do innego regionu
Możesz chcieć przenieść zasoby do innego regionu z kilku powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, spełnić wymagania wewnętrzne zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności. 

Poniżej przedstawiono ogólne kroki opisane w tym artykule: 

- **Wyeksportuj zasób domeny** do szablonu Azure Resource Manager. 

    > [!IMPORTANT]
    > Zasób domeny i tematy w domenie są eksportowane do szablonu. Subskrypcje dotyczące domen nie są eksportowane. 
- **Użyj szablonu, aby wdrożyć domenę** w regionie docelowym. 
- **Ręczne tworzenie subskrypcji tematów dotyczących domeny** w regionie docelowym. Po wyeksportowaniu domeny do szablonu w bieżącym regionie subskrypcje dotyczące domen nie są eksportowane. Dlatego należy je utworzyć po utworzeniu tematów dotyczących domen i domen w regionie docelowym. 
- **Sprawdź wdrożenie**. Wyślij zdarzenie do tematu domeny w domenie i sprawdź, czy program obsługi zdarzeń skojarzony z subskrypcją jest wywoływany. 
- Aby **zakończyć przenoszenie**, Usuń domenę z regionu źródłowego. 

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że usługa Event Grid jest dostępna w regionie docelowym. Zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów dla domeny. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku wyszukiwania wpisz **Event Grid domen**, a następnie wybierz pozycję **domeny Event Grid** z listy wyników. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Wyszukaj i wybierz Event Grid domeny":::
3. Wybierz **domenę** , która ma zostać wyeksportowana do szablonu Menedżer zasobów. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Wybierz domenę":::   
4. Na stronie **domena Event Grid** wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia** w menu po lewej stronie, a następnie wybierz pozycję **Pobierz** na pasku narzędzi. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Eksportuj szablon — pobieranie >" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Tematy dotyczące domen i domen są eksportowane. Subskrypcje dotyczące domen nie są eksportowane. W związku z tym należy utworzyć subskrypcje dla tematów domeny po przeniesieniu tematów dotyczących domen. 
5. Znajdź plik **zip** pobrany z portalu i rozpakuj ten plik do wybranego folderu. Ten plik zip zawiera pliki szablonów i parametrów JSON. 
1. Otwórz **template.js** w wybranym edytorze. 
8. Zaktualizuj `location` zasób **domeny** do docelowego regionu lub lokalizacji. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Zapisz** szablon. 

## <a name="recreate"></a>Utwórz ponownie 
Wdróż szablon, aby utworzyć tematy domen i domen w regionie docelowym. 

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
3. Wybierz **Template Deployment**.
4. Wybierz przycisk **Utwórz**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** pliku pobranego w ostatniej sekcji.
7. Wybierz pozycję **Zapisz** , aby zapisać szablon. 
8. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki:
    1. Wybierz **subskrypcję** platformy Azure. 
    1. Wybierz istniejącą **grupę zasobów** w regionie docelowym lub utwórz ją. 
    1. W polu **region** wybierz region docelowy. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    1. W polu **nazwa domeny** wprowadź nową nazwę domeny. 
    1. Wybierz pozycję **Przejrzyj i utwórz**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Wdrażanie szablonu":::        
    1. Po pomyślnym sprawdzeniu szablonu wybierz pozycję **Utwórz** w dolnej części strony, aby wdrożyć zasób. 
    1. Po pomyślnym wdrożeniu wybierz pozycję **Przejdź do grupy zasobów** , aby przejść do strony Grupa zasobów. Upewnij się, że w grupie zasobów znajduje się domena. Wybierz domenę. Upewnij się, że w domenie znajdują się tematy dotyczące domeny. 

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Aby zakończyć przenoszenie, Usuń domenę w regionie źródłowym.  

Jeśli chcesz zacząć od początku, Usuń domenę w regionie docelowym i powtórz kroki opisane w sekcjach [przygotowanie](#prepare) i [ponowne utworzenie](#recreate) w tym artykule.

Aby usunąć domenę przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **Event Grid domen**, a następnie wybierz pozycję **Event Grid domen** z wyników wyszukiwania. 
2. Wybierz domenę do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. Na stronie Potwierdzenie wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.  

Aby usunąć grupę zasobów zawierającą domenę przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **grupy zasobów**, a następnie wybierz pozycję **grupy zasobów** z wyników wyszukiwania. 
2. Wybierz grupę zasobów do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. Na stronie Potwierdzenie wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki
Wiesz już, jak przenieść domenę Event Grid z jednego regionu do innego. Zapoznaj się z następującymi artykułami dotyczącymi przemieszczania tematów systemowych, niestandardowych tematów i przestrzeni nazw partnerów w różnych regionach.

- [Przenoszenie tematów systemowych między regionami](move-system-topics-across-regions.md). 
- [Przenoszenie niestandardowych tematów między regionami](move-custom-topics-across-regions.md). 
- [Przenieś przestrzenie nazw partnerów między regionami](move-partner-namespaces-across-regions.md).

Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz następujący artykuł: [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).