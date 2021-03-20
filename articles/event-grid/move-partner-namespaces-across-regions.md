---
title: Przenoszenie przestrzeni nazw partnerów Azure Event Grid do innego regionu
description: W tym artykule pokazano, jak przenieść przestrzenie nazw partnerów Azure Event Grid z jednego regionu do innego.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89086211"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Przenoszenie przestrzeni nazw partnerów Azure Event Grid do innego regionu
Możesz chcieć przenieść zasoby do innego regionu z kilku powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, spełnić wymagania wewnętrzne zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności. 

Poniżej przedstawiono ogólne kroki opisane w tym artykule: 

- **Wyeksportuj zasób przestrzeni nazw partnera** do szablonu Azure Resource Manager. Usuń definicje zasobów kanału zdarzeń w szablonie. Kanał zdarzenia może odwoływać się do Azure Resource Manager identyfikator tematu partnera, który jest własnością klienta. Dlatego nie można ich utworzyć przy użyciu szablonu w regionie docelowym.  
- **Użyj szablonu, aby wdrożyć przestrzeń nazw partnera** w regionie docelowym. Następnie utwórz kanały zdarzeń w przestrzeni nazw New Partner w regionie docelowym. 
- Aby **zakończyć przenoszenie**, Usuń przestrzeń nazw partner z regionu źródłowego. 

    > [!NOTE]
    > - Eksportowanie **tematów partnerskich** do szablonu Azure Resource Manager nie jest obsługiwane, ponieważ klienci nie mogą bezpośrednio utworzyć tematu partnera. 
    > - **Rejestracje partnerów** to zasoby globalne (niepowiązane z żadnym regionem), więc przeniesienie ich z jednego regionu do innego nie ma zastosowania. 

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że usługa Event Grid jest dostępna w regionie docelowym. Zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów dla przestrzeni nazw partnera. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku wyszukiwania u góry wpisz **Event Grid przestrzenie nazw partnerów** i wybierz **Event Grid przestrzenie nazw partnerów** z listy wyników. 
3. Wybierz **przestrzeń nazw partnera** , która ma zostać wyeksportowana do szablonu Menedżer zasobów. 
4. Na stronie **przestrzeń nazw partnera Event Grid** wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia** w menu po lewej stronie, a następnie wybierz pozycję **Pobierz** na pasku narzędzi. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Eksportuj szablon — pobieranie >" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Znajdź plik **zip** pobrany z portalu i rozpakuj ten plik do wybranego folderu. Ten plik zip zawiera pliki szablonów i parametrów JSON. 
1. Otwórz **template.js** w wybranym edytorze. 
8. Zaktualizuj `location` zasób **tematu** do docelowego regionu lub lokalizacji. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Zapisz** szablon. 

## <a name="recreate"></a>Utwórz ponownie 
Wdróż szablon, aby utworzyć przestrzeń nazw partnera w regionie docelowym. 

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
    1. W polu **Lokalizacja** wybierz region docelowy. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    1. W polu **nazwa przestrzeni nazw partnera** wprowadź nazwę nowej przestrzeni nazw partnera. 
    1. W polu Identyfikator zewnętrzny rejestracji partnera wprowadź identyfikator zasobu rejestracji partnera w następującym formacie: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Zaznacz pole wyboru **Wyrażam zgodę na powyższe warunki i postanowienia** .     
    1. Wybierz pozycję **Przegląd + Utwórz** , aby rozpocząć proces wdrażania. 
    1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Aby zakończyć przenoszenie, Usuń przestrzeń nazw partner w regionie źródłowym.  

Jeśli chcesz zacząć od początku, Usuń przestrzeń nazw partner w regionie docelowym i powtórz kroki opisane w sekcjach [przygotowanie](#prepare) i [ponowne utworzenie](#recreate) w tym artykule.

Aby usunąć przestrzeń nazw partnera przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **Event Grid przestrzenie nazw partnera**, a następnie wybierz pozycję **Event Grid przestrzenie nazw partnerów** z wyników wyszukiwania. 
2. Wybierz przestrzeń nazw partnera do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. **Potwierdź** usunięcie, aby usunąć przestrzeń nazw partnera. 

## <a name="next-steps"></a>Następne kroki
Wiesz już, jak przenieść przestrzeń nazw partnera Event Grid z jednego regionu do innego. Zapoznaj się z następującymi artykułami dotyczącymi przemieszczania tematów systemowych, niestandardowych tematów i domen w różnych regionach.

- [Przenoszenie tematów systemowych między regionami](move-system-topics-across-regions.md). 
- [Przenoszenie niestandardowych tematów między regionami](move-custom-topics-across-regions.md). 
- [Przenoszenie domen między regionami](move-domains-across-regions.md).

Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz następujący artykuł: [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).