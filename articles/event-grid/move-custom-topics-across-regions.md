---
title: Przenoszenie Azure Event Grid niestandardowych tematów do innego regionu
description: W tym artykule przedstawiono sposób przenoszenia niestandardowych tematów Azure Event Grid z jednego regionu do innego.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145347"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Przenoszenie Azure Event Grid niestandardowych tematów do innego regionu
Możesz chcieć przenieść zasoby do innego regionu z kilku powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, spełnić wymagania wewnętrzne zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności. 

Poniżej przedstawiono ogólne kroki opisane w tym artykule: 

- **Wyeksportuj zasób niestandardowego tematu** do szablonu Azure Resource Manager. 

    > [!IMPORTANT]
    > Tylko temat niestandardowy jest eksportowany do szablonu. Żadna subskrypcja tematu nie zostanie wyeksportowana.
- **Użyj szablonu, aby wdrożyć niestandardowy temat** w regionie docelowym. 
- **Ręcznie Twórz subskrypcje** w regionie docelowym. Po wyeksportowaniu tematu niestandardowego do szablonu w bieżącym regionie jest eksportowany tylko ten temat. Subskrypcje nie są uwzględnione w szablonie, dlatego należy je utworzyć ręcznie po utworzeniu tematu niestandardowego w regionie docelowym. 
- **Sprawdź wdrożenie**. Upewnij się, że temat niestandardowy został utworzony w regionie docelowym. 
- Aby **zakończyć przenoszenie**, Usuń temat niestandardowy z regionu źródłowego. 

## <a name="prerequisites"></a>Wymagania wstępne
- Wykonaj kroki [szybkiego startu: Roześlij zdarzenia niestandardowe do punktu końcowego sieci Web](custom-event-quickstart-portal.md) w regionie źródłowym. Wykonaj ten krok, aby przetestować kroki opisane w tym artykule. 
- Upewnij się, że usługa Event Grid jest dostępna w regionie docelowym. Zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów dla tematu niestandardowego. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku wyszukiwania wpisz **tematy Event Grid**i wybierz pozycję **Event Grid tematy** z listy wyników. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Wyszukaj i wybierz tematy Event Grid":::
3. Wybierz **temat** , który chcesz wyeksportować do szablonu Menedżer zasobów. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Wyszukaj i wybierz tematy Event Grid":::   
4. Na stronie **temat Event Grid** wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia** w menu po lewej stronie, a następnie wybierz pozycję **Pobierz** na pasku narzędzi. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Wyszukaj i wybierz tematy Event Grid"
    ```
1. **Zapisz** szablon. 

## <a name="recreate"></a>Utwórz ponownie 
Wdróż szablon, aby utworzyć niestandardowy temat w regionie docelowym. 

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace**wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
3. Wybierz **Template Deployment**.
4. Wybierz przycisk **Utwórz**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** pliku pobranego w ostatniej sekcji.
7. Wybierz pozycję **Zapisz** , aby zapisać szablon. 
8. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję**platformy Azure. 
    1. Wybierz istniejącą **grupę zasobów** w regionie docelowym lub utwórz ją. 
    1. W polu **region**wybierz region docelowy. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu. 
    1. W polu **nazwa tematu**wprowadź nową nazwę tematu. 
    1. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Wyszukaj i wybierz tematy Event Grid":::
    1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 

## <a name="verify"></a>Weryfikacja

1. Po pomyślnym wdrożeniu wybierz pozycję **Przejdź do zasobu**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Wyszukaj i wybierz tematy Event Grid":::
1. Upewnij się, że na stronie **tematu Event Grid** nie ma widocznego tematu niestandardowego.   
1. Wykonaj kroki opisane w temacie [trasy niestandardowe zdarzenia do punktu końcowego sieci Web](custom-event-quickstart-portal.md#send-an-event-to-your-topic) , aby wysłać zdarzenia do tematu. Sprawdź, czy program obsługi zdarzeń elementu webhook jest wywoływany. 

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Aby zakończyć przenoszenie, Usuń temat niestandardowy w regionie źródłowym.  

Jeśli chcesz zacząć od początku, Usuń temat w regionie docelowym i powtórz kroki opisane w sekcjach [przygotowanie](#prepare) i [ponowne utworzenie](#recreate) w tym artykule.

Aby usunąć temat niestandardowy przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **tematy Event Grid**i wybierz **Tematy Event Grid** z wyników wyszukiwania. 
2. Wybierz temat do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. Na stronie Potwierdzenie wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.  

Aby usunąć grupę zasobów zawierającą temat niestandardowy przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **grupy zasobów**, a następnie wybierz pozycję **grupy zasobów** z wyników wyszukiwania. 
2. Wybierz grupę zasobów do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 
3. Na stronie Potwierdzenie wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tematem przenoszenie Event Grid tematu niestandardowego z jednego regionu do innego. Zapoznaj się z następującymi artykułami dotyczącymi przemieszczania tematów systemowych, domen i przestrzeni nazw partnerów w różnych regionach.

- [Przenoszenie tematów systemowych między regionami](move-system-topics-across-regions.md). 
- [Przenoszenie domen między regionami](move-domains-across-regions.md). 
- [Przenieś przestrzenie nazw partnerów między regionami](move-partner-namespaces-across-regions.md).

Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz następujący artykuł: [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).