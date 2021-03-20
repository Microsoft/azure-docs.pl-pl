---
title: Przenoszenie przestrzeni nazw Azure Relay do innego regionu
description: W tym artykule pokazano, jak przenieść przestrzeń nazw Azure Relay z bieżącego regionu do innego regionu.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463830"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Przenoszenie przestrzeni nazw Azure Relay do innego regionu
W tym artykule pokazano, jak przenieść przestrzeń nazw Azure Relay z jednego regionu do innego. Poniżej przedstawiono ogólne czynności:

1. **Wyeksportuj** przestrzeń nazw przekaźnika do szablonu Azure Resource Manager.
1. **Aktualizuj lokalizację (region)** zasobów w szablonie. Usuń także wszystkie **dynamiczne** przekaźniki WCF z szablonu. 

    Przekaźniki WCF mają dwa tryby. W pierwszym trybie przekaźnik WCF jest jawnie tworzony przy użyciu szablonu Azure Portal lub Azure Resource Manager. Na stronie **przekaźniki WCF** w Azure Portal zobaczysz Właściwość **IsDynamic** ustawioną na **wartość false** dla przekaźnika w tym trybie. 

    W drugim trybie przekaźnik WCF jest generowany automatycznie, gdy odbiornik (serwer) nawiązuje połączenie z danym adresem punktu końcowego. Pod warunkiem, że odbiornik jest połączony z przekaźnikiem, na liście przekaźników WCF w Azure Portal zostanie wyświetlony ten przekaźnik. W przypadku przekaźnika w tym trybie Właściwość **IsDynamic** ma **wartość true** , ponieważ jest generowana dynamicznie. Dynamiczne przekaźniki WCF trafia po rozłączeniu odbiornika. 
1. **Wdróż** zasoby przy użyciu szablonu w regionie docelowym.

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że usługa Azure Relay jest dostępna w regionie docelowym. Zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów. Ten szablon zawiera ustawienia opisujące przestrzeń nazw Azure Relay.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** , a następnie wybierz przestrzeń nazw Azure Relay.
3. Wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia** w menu po lewej stronie.
4. Na stronie **Eksportuj szablon** wybierz pozycję **Pobierz** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Pobierz szablon Menedżer zasobów":::
5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu. Ten plik zip zawiera pliki szablonu i parametrów JSON. 
1. Otwórz **template.jsna** pliku z wyodrębnionego folderu w wybranym edytorze.
1. Wyszukaj `location` i Zastąp wartość właściwości nową nazwą regionu. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .
1. Usuń definicje **dynamicznych zasobów przekaźnika WCF** (typ: `Microsoft.Relay/namespaces/WcfRelays` ). Dynamiczne przekaźniki WCF są tymi, których właściwość **IsDynamic** ma wartość **true** na stronie **przekaźników** . W poniższym przykładzie **EchoService** jest dynamicznym przekaźnikiem WCF, a jego definicja powinna zostać usunięta z szablonu. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Przekaźniki dynamiczne":::

## <a name="move"></a>Move
Wdróż szablon w celu utworzenia przestrzeni nazw przekaźnika w regionie docelowym. 

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
1. Na stronie **Grupa zasobów** wybierz przestrzeń nazw Azure Relay. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Wybierz Azure Relay przestrzeń nazw":::    
1. Na stronie **obszar nazw Azure Relay** wybierz pozycję **połączenia hybrydowe** lub **przekaźniki WCF** w menu po lewej stronie, aby sprawdzić, czy są tworzone połączenia hybrydowe i przekaźniki WCF. Jeśli nie chcesz usunąć definicji dynamicznych przekaźników WCF przed zaimportowaniem szablonu, usuń je na stronie **przekaźniki WCF** . Dynamiczne przekaźniki WCF są tworzone automatycznie, gdy klienci łączą się z przestrzenią nazw usługi Relay. 

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Jeśli po wdrożeniu chcesz zacząć od początku, możesz usunąć **docelową przestrzeń nazw Azure Relay** i powtórzyć kroki opisane w sekcjach [przygotowanie](#prepare) i [przeniesienie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie przestrzeni nazw, Usuń **przestrzeń nazw Azure Relay** w regionie źródłowym. 

Aby usunąć Azure Relay przestrzeń nazw (źródłowa lub docelowa) przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **przekaźniki**, a następnie w wynikach wyszukiwania wybierz pozycję **przekaźniki** z **usług** . Wszystkie Azure Relay przestrzenie nazw są widoczne na liście.
2. Wybierz docelową przestrzeń nazw do usunięcia, aby otworzyć stronę **przekaźnika** . 
1. Na stronie **przekaźnika** wybierz pozycję **Usuń** z paska narzędzi. 

    ![Usuń przestrzeń nazw — przycisk](./media/move-across-regions/delete-namespace-button.png)
3. Na stronie **usuwanie przestrzeni nazw** wpisz nazwę przestrzeni nazw Azure Relay, aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku przeniesiono przestrzeń nazw Azure Relay z jednego regionu do innego. Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:

- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
