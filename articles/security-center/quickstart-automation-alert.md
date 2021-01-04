---
title: Tworzenie automatyzacji zabezpieczeń dla konkretnych alertów zabezpieczeń przy użyciu szablonu Azure Resource Manager (szablon ARM)
description: Dowiedz się, jak utworzyć automatyzację Azure Security Center, aby wyzwolić aplikację logiki, która będzie wyzwalana przez określone alerty Security Center przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: e53a5c4840cee0651090bec0b68cc53e13102299
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705493"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Szybki Start: Tworzenie automatycznej odpowiedzi na określony alert zabezpieczeń przy użyciu szablonu usługi ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia automatyzacji przepływu pracy, który wyzwala aplikację logiki, gdy określone alerty zabezpieczeń są odbierane przez Azure Security Center.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby uzyskać listę ról i uprawnień wymaganych do pracy z funkcją automatyzacji przepływu pracy Azure Security Center, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Odpowiednie zasoby

- [**Microsoft. Security/automations**](/azure/templates/microsoft.security/automations): Automatyzacja, która wywoła aplikację logiki, po odebraniu alertu Azure Security Center zawierającego określony ciąg.
- [**Microsoft. logiki/przepływy pracy**](/azure/templates/microsoft.logic/workflows): pusta aplikacja logiki z wyzwalaczem.

Inne Security Center Szablony szybkiego startu można znaleźć w [szablonach](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular)utworzonych przez społeczność.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

- Program **PowerShell**:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Interfejs wiersza polecenia**:

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portal**:

  [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Aby uzyskać więcej informacji na temat tej opcji wdrożenia, zobacz [Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Użyj Azure Portal, aby sprawdzić, czy Automatyzacja przepływu pracy został wdrożony.

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center**.
1. Na górnym pasku menu wybierz ikonę filtru, a następnie wybierz konkretną subskrypcję, w której wdrożono nową automatyzację przepływu pracy.
1. Na pasku bocznym Security Center Otwórz **automatyzację przepływu pracy** i Sprawdź nową automatyzację.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Lista skonfigurowanych automatyzacji" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Jeśli masz wiele automatyzacji przepływu pracy w subskrypcji, użyj opcji **Filtruj według nazwy** .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń automatyzację przepływu pracy przy użyciu Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center**.
1. Na górnym pasku menu wybierz ikonę filtru, a następnie wybierz konkretną subskrypcję, w której wdrożono nową automatyzację przepływu pracy.
1. Na pasku bocznym Security Center Otwórz **automatyzację przepływu pracy** i Znajdź automatyzację do usunięcia.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Procedura usuwania automatyzacji przepływu pracy" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Zaznacz pole wyboru dla elementu, który ma zostać usunięty.
1. Na pasku narzędzi wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
