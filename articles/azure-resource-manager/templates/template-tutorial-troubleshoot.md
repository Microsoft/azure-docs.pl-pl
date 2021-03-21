---
title: Rozwiązywanie problemów z wdrożeniami
description: Dowiedz się, jak monitorować i rozwiązywać problemy z wdrożeniami szablonu Azure Resource Manager (szablon ARM). Pokazuje dzienniki aktywności i historię wdrożenia.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589121"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów ARM

Dowiedz się, jak rozwiązywać problemy z wdrażaniem szablonu Azure Resource Manager (szablon ARM). W tym samouczku skonfigurujesz dwa błędy w szablonie i dowiesz się, jak korzystać z dzienników aktywności i historii wdrażania, aby rozwiązać problemy.

Istnieją dwa typy błędów związanych z wdrażaniem szablonu:

- **Błędy weryfikacji** wynikają z sytuacji, które można rozpoznać przed przystąpieniem do wdrożenia. Są to na przykład błędy składniowe w szablonie lub próby wdrożenia zasobów, które przekraczają limity przydziału w ramach subskrypcji.
- **Błędy wdrażania** wynikają z warunków, które mają miejsce podczas procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany równolegle.

Oba rodzaje błędów zwracają kod błędu, którego należy użyć do rozwiązania problemów z wdrożeniem. Oba rodzaje błędów są wyświetlane w dzienniku aktywności. Błędy weryfikacji nie są jednak wyświetlane w historii wdrażania, ponieważ wdrożenie nie jest w takim przypadku rozpoczynane.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> - Tworzenie problematycznego szablonu
> - Rozwiązywanie problemów z błędami weryfikacji
> - Usuwanie błędów związanych z wdrażaniem
> - Czyszczenie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

- Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Tworzenie problematycznego szablonu

Otwórz szablon o nazwie [Utwórz konto magazynu w warstwie Standardowa](https://azure.microsoft.com/resources/templates/101-storage-account-create/) na podstawie [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)i skonfiguruj dwa problemy z szablonami.

1. W obszarze Visual Studio Code wybierz pozycję **plik**  >  **Otwórz plik**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Zmień `apiVersion` wiersz na następujący wiersz:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` jest nieprawidłową nazwą elementu. Jest to błąd weryfikacji.
    - Jest to wersja interfejsu API `"2018-07-01"` .  Jest to błąd wdrażania.

5. Wybierz pozycję **plik**  >  **Zapisz jako,** aby zapisać plik jako _azuredeploy.jsna_ komputerze lokalnym.

## <a name="troubleshoot-the-validation-error"></a>Rozwiązywanie problemów z błędami weryfikacji

Zapoznaj się sekcją [Wdrażanie szablonu](template-tutorial-create-multiple-instances.md#deploy-the-template), aby wdrożyć szablon.

Otrzymasz mniej więcej taki komunikat o błędzie z poziomu powłoki:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Komunikat o błędzie wskazuje, że problem dotyczy `apiVersion1` .

Użyj Visual Studio Code, aby rozwiązać problem, zmieniając `apiVersion1` `apiVersion` pozycję na, a następnie Zapisz szablon.

## <a name="troubleshoot-the-deployment-error"></a>Rozwiązywanie problemów związanych z błędami wdrażania

Zapoznaj się sekcją [Wdrażanie szablonu](template-tutorial-create-multiple-instances.md#deploy-the-template), aby wdrożyć szablon.

Otrzymasz mniej więcej taki komunikat o błędzie z poziomu powłoki:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Błąd wdrażania można odnaleźć w witrynie Azure Portal, korzystając z następującej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz grupę zasobów, wybierając pozycję **Grupa zasobów**, a następnie wybierz nazwę grupy zasobów. Zostanie wyświetlony komunikat **1 niepowodzenie** w obszarze **Wdrożenia**.

    ![Zrzut ekranu, który wyróżnia niepowodzenie wdrożenia.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Wybierz pozycję **Szczegóły błędu**.

    ![Zrzut ekranu, który podświetla link szczegóły błędu.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Komunikat o błędzie jest taki sam jak ten pokazany wcześniej:

    ![Zrzut ekranu pokazujący szczegóły błędu.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Ten błąd można również znaleźć w dzienniku aktywności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Monitoruj**  >  **Dziennik aktywności**.
3. Użyj filtrów, aby znaleźć dziennik.

    ![Samouczek dotyczący rozwiązywania problemów w usłudze Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Użyj programu Visual Studio Code, aby naprawić ten błąd, a następnie ponownie wdróż szablon.

Aby zapoznać się z listą typowych błędów, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](common-deployment-errors.md) (Rozwiązywanie typowych błędów z wdrożeniem na platformie Azure w usłudze Azure Resource Manager).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozwiązywania problemów z wdrażaniem szablonów ARM.  Aby uzyskać więcej informacji, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](common-deployment-errors.md) (Rozwiązywanie typowych błędów z wdrożeniem na platformie Azure w usłudze Azure Resource Manager).
