---
title: Przenieś Azure Event Grid tematy systemowe do innego regionu
description: W tym artykule przedstawiono sposób przenoszenia tematów systemu Azure Event Grid z jednego regionu do innego.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89086186"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Przenieś Azure Event Grid tematy systemowe do innego regionu
Możesz chcieć przenieść zasoby do innego regionu z kilku powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, spełnić wymagania wewnętrzne zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności. 

Poniżej przedstawiono ogólne kroki opisane w tym artykule: 

- **Wyeksportuj grupę zasobów** zawierającą konto usługi Azure Storage i skojarzony z nim temat systemu do szablonu Menedżer zasobów. Możesz również wyeksportować szablon tylko dla tematu systemowego. Jeśli przejdziesz do tej trasy, pamiętaj, aby przenieść źródło zdarzeń platformy Azure (w tym przykładzie konto usługi Azure Storage) do innego regionu przed przeniesieniem tematu systemowego. Następnie w wyeksportowanym szablonie tematu system należy zaktualizować identyfikator zewnętrzny dla konta magazynu w regionie docelowym. 
- **Zmodyfikuj szablon** , aby dodać `endpointUrl` Właściwość w celu wskazywania elementu webhook, który subskrybuje temat systemowy. Po wyeksportowaniu tematu systemowego jego subskrypcję (w tym przypadku jest to element webhook) jest również eksportowany do szablonu, ale `endpointUrl` Właściwość nie jest uwzględniona. W związku z tym należy zaktualizować go, aby wskazywał punkt końcowy, który subskrybuje ten temat. Ponadto zaktualizuj wartość `location` właściwości do nowej lokalizacji lub regionu. W przypadku innych typów programów obsługi zdarzeń należy zaktualizować tylko lokalizację. 
- **Użyj szablonu, aby wdrożyć zasoby** w regionie docelowym. Należy określić nazwy dla konta magazynu i temat systemowy, który ma zostać utworzony w regionie docelowym. 
- **Sprawdź wdrożenie**. Sprawdź, czy element webhook jest wywoływany podczas przekazywania pliku do magazynu obiektów BLOB w regionie docelowym. 
- Aby **ukończyć przenoszenie**, Usuń zasoby (Źródło zdarzenia i temat systemu) z regionu źródłowego. 

## <a name="prerequisites"></a>Wymagania wstępne
- Ukończ [Przewodnik Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do punktu końcowego w sieci Web za pomocą Azure Portal](blob-event-quickstart-portal.md) w regionie źródłowym. Ten krok jest **opcjonalny**. Wykonaj kroki opisane w tym artykule. Przechowuj konto magazynu w osobnej grupie zasobów z poziomu App Service i App Service planu. 
- Upewnij się, że usługa Event Grid jest dostępna w regionie docelowym. Zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Przygotowywanie
Aby rozpocząć, wyeksportuj szablon Menedżer zasobów dla grupy zasobów zawierającej źródło zdarzeń systemu (konto usługi Azure Storage) i skojarzony z nim temat systemowy. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **grupy zasobów** . Następnie wybierz grupę zasobów zawierającą Źródło zdarzenia, dla którego utworzono temat systemowy. W poniższym przykładzie jest to konto **usługi Azure Storage** . Grupa zasobów zawiera konto magazynu i skojarzony z nim temat systemowy. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Strona grupy zasobów":::        
3. W menu po lewej stronie wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia**, a następnie wybierz pozycję **Pobierz** na pasku narzędzi. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Konto Storage — eksportowanie strony szablonu":::        
5. Znajdź plik **zip** pobrany z portalu i rozpakuj ten plik do wybranego folderu. Ten plik zip zawiera pliki szablonów i parametrów JSON. 
1. Otwórz **template.js** w wybranym edytorze. 
1. Adres URL elementu webhook nie jest eksportowany do szablonu. Wykonaj następujące czynności:
    1. W pliku szablonu Wyszukaj **element webhook**. 
    1. W sekcji **Właściwości** Dodaj znak przecinka ( `,` ) na końcu ostatniego wiersza. W tym przykładzie jest to `"preferredBatchSizeInKilobytes": 64` . 
    1. Dodaj `endpointUrl` Właściwość z wartością ustawioną na adres URL elementu webhook, jak pokazano w poniższym przykładzie. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > W przypadku innych typów programów obsługi zdarzeń wszystkie właściwości są eksportowane do szablonu. Wystarczy zaktualizować `location` Właściwość do regionu docelowego, jak pokazano w następnym kroku. 
7. Zaktualizuj `location` zasób **konta magazynu** do docelowego regionu lub lokalizacji. Aby uzyskać kody lokalizacji, zapoznaj się z tematem [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu bez spacji, na przykład, `West US` jest równa `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Powtórz ten krok, aby zaktualizować `location` zasób **tematu systemu** w szablonie. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Zapisz** szablon. 

## <a name="recreate"></a>Utwórz ponownie 
Wdróż szablon w celu utworzenia konta magazynu i tematu systemu dla konta magazynu w regionie docelowym. 

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
3. Wybierz **Template Deployment**.
4. Wybierz przycisk **Utwórz**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** pliku pobranego w ostatniej sekcji.
7. Wybierz pozycję **Zapisz** , aby zapisać szablon. 
8. Na stronie **wdrożenie niestandardowe** wykonaj następujące kroki. 
    1. Wybierz **subskrypcję** platformy Azure. 
    1. Wybierz istniejącą **grupę zasobów** w regionie docelowym lub utwórz ją. 
    1. W polu **region** wybierz region docelowy. W przypadku wybrania istniejącej grupy zasobów to ustawienie jest tylko do odczytu.
    1. W polu **nazwa tematu systemu** wprowadź nazwę tematu systemowego, który zostanie skojarzony z kontem magazynu.  
    1. W polu **nazwa konta magazynu** wprowadź nazwę konta magazynu, które ma zostać utworzone w regionie docelowym. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Wdróż szablon Menedżer zasobów":::
    5. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony. 
    1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 

## <a name="verify"></a>Weryfikacja
1. Po pomyślnym wdrożeniu wybierz pozycję **Przejdź do grupy zasobów**. 
1. Na stronie **Grupa zasobów** Sprawdź, czy jest tworzone źródło zdarzeń (w tym przykładzie konto usługi Azure Storage) i temat systemowy. 
1. Przekaż plik do kontenera w usłudze Azure Blob Storage i sprawdź, czy element webhook otrzymał zdarzenie. Aby uzyskać więcej informacji, zobacz [wysyłanie zdarzenia do punktu końcowego](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie
Aby zakończyć przenoszenie, Usuń grupę zasobów zawierającą konto magazynu i skojarzony z nim temat systemu w regionie źródłowym.  

Jeśli chcesz zacząć od początku, Usuń grupę zasobów w regionie docelowym i powtórz kroki opisane w sekcjach [przygotowanie](#prepare) i [ponowne utworzenie](#recreate) w tym artykule.

Aby usunąć grupę zasobów (źródłową lub docelową) przy użyciu Azure Portal:

1. W oknie wyszukiwania w górnej części Azure Portal wpisz **grupy zasobów**, a następnie wybierz pozycję **grupy zasobów** z wyników wyszukiwania. 
2. Wybierz grupę zasobów do usunięcia, a następnie wybierz pozycję **Usuń** z paska narzędzi. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Usuwanie grupy zasobów":::
3. Na stronie Potwierdzenie wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki
Zawarto informacje na temat przenoszenia źródła zdarzeń platformy Azure i skojarzonego z nim tematu systemu z jednego regionu do innego. Zapoznaj się z następującymi artykułami dotyczącymi przemieszczania niestandardowych tematów, domen i przestrzeni nazw partnerów w różnych regionach.

- [Przenoszenie niestandardowych tematów między regionami](move-custom-topics-across-regions.md). 
- [Przenoszenie domen między regionami](move-domains-across-regions.md). 
- [Przenieś przestrzenie nazw partnerów między regionami](move-partner-namespaces-across-regions.md). 

Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz następujący artykuł: [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).
